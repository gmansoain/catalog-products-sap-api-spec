# Publish Summary — catalog-products-sap-api v2.0.1 (pending publish)

**Generated:** 2026-10-13
**Prior release:** v1.0.0 (2026-09-19)
**Breaking changes since v1.0.0:** See [`CHANGELOG.md`](./CHANGELOG.md). Two breaking concerns in this publish:

1. **Error envelope refactor** — new shape (`errorCategory`, `errorCode` in `CTLG-RES-001` format, `details[]` restructured, 14-code catalog). Response contract on every 4xx/5xx operation changes.
2. **`isActive` removed from PUT/PATCH request bodies** — activation state is server-managed via `DELETE /products/{productId}` (deactivate) and the v2 reactivate operation. Payloads carrying `isActive` are rejected with 400 `CTLG-VAL-001`.

**API path version bumps v1 → v2.** Design Center enforces alignment between the asset's major SemVer (`2.x.x`) and the `apiVersion` field — any 2.x asset must publish under `apiVersion: v2`. The RAML's `version:` field, the `exchange.json` `apiVersion` field, and the `baseUri` `{version}` substitution all now resolve to `v2`.

---

## API Overview

| Field | Value |
|-------|-------|
| **Asset Name** | catalog-products-sap-api |
| **Display Name** | Catalog Products SAP System API |
| **Version** | 2.0.1 (pending publish) |
| **API Version** | v2 |
| **Base URL (prod)** | `http://api.leno.com/catalog-products-sap/v2` |
| **Description** | System-layer adapter over the SAP ERP product master and its supporting MySQL persistence. Exposes canonical CRUD on `Product` records and functional-monitoring probes. |

---

## Exchange Taxonomy

| Dimension | Value |
|-----------|-------|
| **Category** | `catalog` |
| **Tags — API Layer** | `system-api` |
| **Tags — Integration Pattern** | `sync` |
| **Tags — Visibility & Consumer Scope** | `internal` |
| **Tags — Lifecycle** | `stable` |
| **Tags — SLA Tier** | `sla-silver` |
| **Tags — Data Classification** | `data-internal` |
| **Tags — Authentication Method** | `auth-mulesoft-client-id` |
| **Tags — Channel** | (not applicable — System API) |
| **Tags — Capability** | (none) |
| **Tags — Customer Journey Stage** | (none — System APIs are journey-agnostic) |
| **Tags — Physical-Goods Concern** | (none at v1; may add `serialized-inventory` once IMEI capture lands) |
| **Tags — Regulatory** | (none) |

---

## Files

### Spec root
- `catalog-products-sap-api/api.raml` — main API specification (RAML `version: v2`)
- `catalog-products-sap-api/exchange.json` — Exchange metadata (asset `2.0.1`, apiVersion `v2`)
- `catalog-products-sap-api/CHANGELOG.md`
- `catalog-products-sap-api/PUBLISH_SUMMARY.md`

### Documentation
- `catalog-products-sap-api/documentation/overview.md`
- `catalog-products-sap-api/documentation/getting-started.md`
- `catalog-products-sap-api/documentation/authentication.md`
- `catalog-products-sap-api/documentation/environments.md`
- `catalog-products-sap-api/documentation/errors.md`
- `catalog-products-sap-api/documentation/changelog.md`
- `catalog-products-sap-api/documentation/support.md`

### Types
- `catalog-products-sap-api/types/Product.raml`
- `catalog-products-sap-api/types/ProductCategory.raml`
- `catalog-products-sap-api/types/ProductCreateRequest.raml`
- `catalog-products-sap-api/types/ProductUpdateRequest.raml` *(2.0.1 — isActive removed)*
- `catalog-products-sap-api/types/ProductPatchRequest.raml` *(2.0.1 — isActive removed)*
- `catalog-products-sap-api/types/ErrorResponse.raml`
- `catalog-products-sap-api/types/ErrorDetail.raml` *(new in 2.0.0)*
- `catalog-products-sap-api/types/HealthAlive.raml`
- `catalog-products-sap-api/types/HealthReady.raml`

### Traits
- `catalog-products-sap-api/traits/correlatable.raml`
- `catalog-products-sap-api/traits/paginated.raml`
- `catalog-products-sap-api/traits/has-errors.raml`

### Resource types
- `catalog-products-sap-api/resourceTypes/collection.raml`
- `catalog-products-sap-api/resourceTypes/item.raml`

### Security schemes
- `catalog-products-sap-api/securitySchemes/mulesoft-client-id.raml`

### Examples — resources and health
- `catalog-products-sap-api/examples/Product-example.json`
- `catalog-products-sap-api/examples/Product-list-example.json`
- `catalog-products-sap-api/examples/ProductCreateRequest-example.json`
- `catalog-products-sap-api/examples/ProductUpdateRequest-example.json` *(2.0.1 — isActive removed)*
- `catalog-products-sap-api/examples/ProductPatchRequest-example.json` *(2.0.1 — isActive removed)*
- `catalog-products-sap-api/examples/HealthAlive-example.json`
- `catalog-products-sap-api/examples/HealthReady-ok-example.json`
- `catalog-products-sap-api/examples/HealthReady-offline-example.json`

### Examples — error responses *(code-named catalogue, new in 2.0.0)*

One file per `errorCode`. Codes with multiple triggers get one file per variant.

- `catalog-products-sap-api/examples/ErrorResponse-CTLG-VAL-001-missing-field.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-VAL-001-invalid-type.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-VAL-001-invalid-enum.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-VAL-001-invalid-pattern.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-VAL-002.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-VAL-003.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-VAL-004.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-RES-000.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-RES-001.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-AUTH-001.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-AUTHZ-001.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-BUS-001-already-inactive.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-BUS-001-soft-deleted.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-BUS-002-fk-violation.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-BUS-002-unique-violation.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-BUS-002-check-violation.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-DEP-001.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-SYS-001.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-SYS-002.json`
- `catalog-products-sap-api/examples/ErrorResponse-CTLG-UNK-001.json`

**Removed in 2.0.0** (replaced by the code-named catalogue above):
- `ErrorResponse-400-example.json`, `ErrorResponse-401-example.json`, `ErrorResponse-403-example.json`, `ErrorResponse-404-example.json`, `ErrorResponse-409-example.json`, `ErrorResponse-500-example.json`, `ErrorResponse-503-example.json`

---

## anypoint-cli publish command

```bash
anypoint-cli exchange asset upload \
  --classifier raml \
  --apiVersion v2 \
  --mainFile api.raml \
  --name "Catalog Products SAP System API" \
  --tags "system-api,sync,internal,stable,sla-silver,data-internal,auth-mulesoft-client-id" \
  --files "$(pwd)/catalog-products-sap-api" \
  "${ANYPOINT_ORG_ID}/catalog-products-sap-api/2.0.1"
```

Requires `ANYPOINT_USERNAME`, `ANYPOINT_PASSWORD`, and `ANYPOINT_ORG_ID` environment variables — supply them via your secrets manager, never inline.

---

## Deployment implications of the v1 → v2 path bump

Per [`docs/versioning-strategy.md`](../docs/versioning-strategy.md) §3, a major API path version bump means a **new CloudHub app alongside the v1 app** — not an in-place redeploy. Expected deployment layout once v2 is live:

```
catalog-products-sap-api-v1-sandbox   ← deprecated (sunset date TBD)
catalog-products-sap-api-v1-prod      ← deprecated
catalog-products-sap-api-v2-sandbox   ← active
catalog-products-sap-api-v2-prod      ← active
```

For this demo, the v1 app can be redeployed with the new RAML in-place during development; a proper side-by-side deployment is a demo-polish task for later.

---

## Health check status-code decision

At v1 this API has a single dependency — `SAP`, simulated in the demo by the `ecommerce_sap` MySQL schema — so only two aggregate states are reachable. `DEGRADED` is kept in the `status` enum for forward compatibility when additional dependencies are added.

| Aggregate status | HTTP | Emitted at v1? | Rationale |
|------------------|------|----------------|-----------|
| `OK` | 200 | Yes | SAP is `UP`. |
| `DEGRADED` | 200 | No — reserved | Mix of `UP` / `DOWN` requires ≥2 dependencies. Pod would stay in rotation; alerts fire off the body payload. |
| `OFFLINE` | 503 | Yes | SAP is `DOWN`. Orchestrator removes the pod from rotation; consumers back off. |
