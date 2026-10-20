# E-commerce Common Errors

RAML 1.0 Library — shared error envelope, detail type, response trait, and
example payloads for every e-commerce API in the suite (System, Process,
Experience).

Published to Anypoint Exchange under the project business group
(`98b7f48b-6753-4ba8-bcf3-7c0fb76bb6f2`) as `ecommerce-common-errors`.

## What's inside

| File | Role |
|---|---|
| `ecommerce-common-errors.raml` | Library entry point. Exposes `ErrorResponse`, `ErrorDetail`, `hasErrors`. |
| `types/ErrorResponse.raml` | Envelope datatype — `errorCategory`, `errorCode`, `message`, `correlationId`, `timestamp`, optional `details[]`. |
| `types/ErrorDetail.raml` | Field-level breakdown — `fieldName`, `reason`, optional `expectedValue`, `actualValue`, `context`. |
| `traits/has-errors.raml` | Attaches the standard 4xx/5xx responses (400, 401, 403, 404, 405, 406, 409, 415, 500, 501, 503) to a resource or method. One `examples:` key per error-code variant. |
| `examples/ErrorResponse-CTLG-*.json` | 20 example payloads keyed on `CTLG-*` codes — the baseline catalog shared across all tiers. |

## Consuming the library

In your RAML spec:

```raml
#%RAML 1.0
title: Your API

uses:
  errors: exchange_modules/98b7f48b-6753-4ba8-bcf3-7c0fb76bb6f2/ecommerce-common-errors/1.0.0/ecommerce-common-errors.raml

traits:
  hasErrors: errors.hasErrors

/your-resource:
  is: [hasErrors]
  get:
    responses:
      404:
        body:
          application/json:
            type: errors.ErrorResponse
            example: !include examples/your-404-example.json
```

The `hasErrors` trait pre-wires the full 11-response set. You only need to
declare resource-specific response bodies (e.g. for 200/201) and overrides.

## Error codes

Codes are **per business domain, not per API tier**. `CTLG-RES-001` means
"product not found" whether it's emitted by SAP, Process, or Experience —
the emitting tier is identified by `apiLayer`/`apiName` in the log context,
not in the error code.

Current baseline (shipped as examples here): `CTLG-*` for catalog.

Each consuming app extends via its own `src/main/resources/dwl/appMappings.dwl`
when it starts emitting codes from additional domains (e.g. `ORDR-*`,
`CART-*`, `INVT-*`).

Full catalog + category/HTTP-status mapping is in
[`docs/error-handling-strategy.md`](../docs/error-handling-strategy.md) §4–§5.

## Versioning

SemVer:
- **Patch** — example/doc changes, non-schema fixes.
- **Minor** — new fields, new examples, new trait responses (non-breaking).
- **Major** — envelope-shape break. Requires a coordinated bump across
  every API that consumes the library — not routine.

## Related

- Project error strategy: [`docs/error-handling-strategy.md`](../docs/error-handling-strategy.md)
- Project logging strategy: [`docs/logging-strategy.md`](../docs/logging-strategy.md)
- Refactor plan: [`refactor-error-handling.md`](../refactor-error-handling.md)
