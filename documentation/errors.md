# Errors

Catalog Products SAP System API returns errors using the project-wide error envelope. Treat `errorCategory` + `errorCode` as the stable machine-readable contract; `message` is human-readable and may evolve between releases.

See [`docs/error-handling-strategy.md`](../../docs/error-handling-strategy.md) for the full approach — envelope, taxonomy, central handler, reliability patterns.

## Error envelope

```json
{
  "errorCategory": "VALIDATION_ERROR",
  "errorCode": "CTLG-VAL-001",
  "message": "Request body is invalid.",
  "correlationId": "5f9c2e1a-3b8d-4f7e-9a1c-2d4e6f8a0b3c",
  "timestamp": "2026-10-10T09:14:22Z",
  "details": [
    {
      "fieldName": "productDescription",
      "reason": "Required field is missing.",
      "expectedValue": "non-empty string, max 200 chars",
      "context": "Request body"
    }
  ]
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `errorCategory` | yes | One of ten long-form categories (`VALIDATION_ERROR`, `AUTHENTICATION_ERROR`, `AUTHORIZATION_ERROR`, `RESOURCE_NOT_FOUND`, `BUSINESS_RULE_VIOLATION`, `DEPENDENCY_FAILURE`, `NETWORK_ERROR`, `SYSTEM_ERROR`, `SECURITY_VIOLATION`, `UNKNOWN_ERROR`). |
| `errorCode` | yes | Stable machine-readable code `<DOMAIN>-<CATEGORY>-NNN`. For this API the `DOMAIN` is `CTLG` in v1. Use for programmatic handling. |
| `message` | yes | Human-readable summary. May change between releases — do not parse. |
| `correlationId` | yes | UUID that ties this response to every log line for this request. Echoes `X-Correlation-Id` when supplied; auto-generated otherwise. Quote this in support tickets. |
| `timestamp` | yes | ISO 8601 UTC instant the error was generated. |
| `details[]` | optional | Array of field-level or contextual breakdowns. Populated for `VALIDATION_ERROR` and some `BUSINESS_RULE_VIOLATION` codes; omitted entirely for categories that are not field-level. See [ErrorDetail shape](#errordetail-shape). |

## ErrorDetail shape

Each entry in `details[]`:

| Field | Required | Description |
|-------|----------|-------------|
| `fieldName` | yes | The field, parameter, or header at fault. For body fields, uses JSON-style dotted paths matching the request body's structure. |
| `reason` | yes | Human-readable explanation of why this field failed. |
| `expectedValue` | optional | Documented or inferred expected shape — pattern name, enum values, numeric constraints. |
| `actualValue` | optional | What was received. Sanitize when the field may carry PII or secrets. |
| `context` | optional | Where the problem lives — e.g. `Request body`, `URL path parameter`, `HTTP request header`, `Current resource state`, `Foreign key constraint …`. |

## Code catalog (SAP tier)

Every error code this API can emit. HTTP status is deterministic from the code.

| `errorCode` | HTTP | Category | When this API emits it |
|-------------|------|----------|------------------------|
| `CTLG-VAL-001` | 400 | VALIDATION_ERROR | Request body or path parameter fails schema validation. `details[]` populated with one or more field entries. |
| `CTLG-VAL-002` | 405 | VALIDATION_ERROR | HTTP method not supported on this resource. |
| `CTLG-VAL-003` | 406 | VALIDATION_ERROR | `Accept` header requests a media type this API doesn't produce. |
| `CTLG-VAL-004` | 415 | VALIDATION_ERROR | `Content-Type` not supported on this write operation. |
| `CTLG-RES-000` | 404 | RESOURCE_NOT_FOUND | Request path is not defined on this API. |
| `CTLG-RES-001` | 404 | RESOURCE_NOT_FOUND | Request path is defined but the specific product does not exist (including soft-deleted products). |
| `CTLG-AUTH-001` | 401 | AUTHENTICATION_ERROR | Missing or invalid `client_id` / `client_secret`. |
| `CTLG-AUTHZ-001` | 403 | AUTHORIZATION_ERROR | Credentials valid but contract does not grant access to this operation. |
| `CTLG-BUS-001` | 409 | BUSINESS_RULE_VIOLATION | Business-rule violation — a state transition inconsistent with the resource's current state (e.g. deactivate an already-deactivated product, update a soft-deleted product). `details[]` carries the specific reason. |
| `CTLG-BUS-002` | 409 | BUSINESS_RULE_VIOLATION | Database constraint violation — foreign key, unique, or check. `details[]` names the constraint and the field at fault. |
| `CTLG-DEP-001` | 503 | DEPENDENCY_FAILURE | The SAP backing store (`ecommerce_sap` MySQL schema in the demo) is unreachable. Retry with exponential backoff. |
| `CTLG-SYS-001` | 500 | SYSTEM_ERROR | Unexpected internal error — typically a non-constraint SQL error or an unhandled branch. |
| `CTLG-SYS-002` | 501 | SYSTEM_ERROR | A documented operation is not yet implemented. |
| `CTLG-UNK-001` | 500 | UNKNOWN_ERROR | Catch-all fallthrough — the central handler did not recognize the underlying Mule error type. Fires alerts; investigate with the `correlationId`. |

## Parsing errors in consumer code

- Treat `errorCode` as the primary key for programmatic handling. It is stable; `message` is not.
- `errorCategory` is useful for broad classification — e.g. a retry policy keyed on `DEPENDENCY_FAILURE` and `NETWORK_ERROR` without enumerating every code.
- Check for `details[]` presence before iterating; the field is absent (not `null`, not `[]`) for most categories.
- `errorCode` matches `^[A-Z]{3,6}-[A-Z]{3,5}-\d{3}$` across every API in the project — a single parse pattern handles all tiers.

## Correlation IDs

- Supply `X-Correlation-Id` as a v4 UUID on every request. If present, it echoes in both the response envelope and every log line emitted during the request's lifetime.
- If absent, the API generates one on entry. It is still visible in the response envelope.
- Always quote `correlationId` in support tickets — a single query across the stack reconstructs the full request lifecycle.

## Health check status semantics

`/alive` and `/ready` do not use the standard error envelope — they always return a health-shaped body regardless of status.

| Endpoint | Body `status` | HTTP status | Meaning |
|----------|---------------|-------------|---------|
| `/alive` | `OK` | 200 | Process is running. |
| `/ready` | `OK` | 200 | The `SAP` dependency is `UP`. |
| `/ready` | `OFFLINE` | 503 | The `SAP` dependency is `DOWN`; the API cannot serve business requests. Orchestrator removes the pod from rotation. |

`DEGRADED` is defined in the `status` enum for forward compatibility but is not emitted at v1 — with a single dependency, the aggregate can only be `OK` (UP) or `OFFLINE` (DOWN). Once additional dependencies are added, a mix of `UP` and `DOWN` will emit `DEGRADED` with HTTP `200` so the pod stays in load-balancer rotation.

In this demo the `SAP` dependency is simulated by the `ecommerce_sap` MySQL schema — the readiness check pings the DB and reports the result under the `SAP` name.

## Retrying

- **4xx** errors are not retryable without changing the request. (This API does not emit `429` at v1.)
- **5xx** errors — retry policy driven by `errorCategory`:
  - `DEPENDENCY_FAILURE` (503), `NETWORK_ERROR` (502, 504): retry with exponential backoff + jitter. Transient by nature.
  - `SYSTEM_ERROR` (500), `UNKNOWN_ERROR` (500): retry **once** with backoff. Repeated failures indicate a bug or outage — raise a ticket with `correlationId`.
- Idempotent operations (`GET`, `HEAD`, `PUT`, `DELETE`) are always safe to retry.
- `POST /products` is **not idempotent** — retrying on a network timeout can create duplicates. Supply an explicit `productId` on retry when duplicate creation is a concern.

## Localization

- `errorCategory` and `errorCode` are **never** localized — they are part of the contract.
- `message` and `details[].reason` are English-only on this System API. Localization is applied at the Experience layer if required.
