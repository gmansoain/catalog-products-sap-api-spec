# Changelog

All notable changes to `catalog-products-sap-api` are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [2.0.2] - 2026-10-15

### Changed
- Error envelope assets (envelope datatype, detail datatype, response trait, example payloads) extracted into the shared Exchange library `98b7f48b-6753-4ba8-bcf3-7c0fb76bb6f2:ecommerce-common-errors:1.0.0`. `api.raml` consumes the library via `uses: errors: …ecommerce-common-errors.raml`; `hasErrors` is applied as `errors.hasErrors`; `ErrorResponse` and `ErrorDetail` are aliased to `errors.ErrorResponse` / `errors.ErrorDetail` in the top-level `types:` block.
- `securitySchemes/mulesoft-client-id.raml` now `uses:` the shared library; type references switched from local `!include` to `errors.ErrorResponse`; 401/403 examples inlined as YAML values.
- Per-method 404 overrides on `/products/{productId}` (GET/PUT/PATCH/DELETE) now inline the example value (same payload as the fragment's `ErrorResponse-CTLG-RES-001.json`) instead of `!include`-ing an inner-dependency file — complies with Design Center library-encapsulation rules.
- `exchange.json` declares the dependency on `ecommerce-common-errors@1.0.0`.

### Removed
- Local copies of the error assets, now owned by the shared library: `types/ErrorResponse.raml`, `types/ErrorDetail.raml`, `traits/has-errors.raml`, and all 20 `examples/ErrorResponse-CTLG-*.json`.

### Not changed (contract-stable)
- Envelope shape, error codes, HTTP-status mapping, response descriptions, operations, and all other types are byte-identical to `2.0.1`. This release is a packaging refactor; consumers see the same API.

## [2.0.1] - previously released

### Changed (BREAKING)
- Error envelope refactored to align with the project-wide error-handling strategy (`docs/error-handling-strategy.md`). **Next publish: `2.0.1` with API path version bumped from `v1` to `v2`** (Design Center enforces alignment between asset major version and `apiVersion`, so any `2.x.x` asset requires the API path to move from `/v1/` to `/v2/`). Both are now set in `exchange.json` and `api.raml`.
  - New required field `errorCategory` (enum of ten long-form categories).
  - `errorCode` format changed from `NOT_FOUND`-style to `CTLG-RES-001`-style (`<DOMAIN>-<CATEGORY>-NNN`, matching `^[A-Z]{3,6}-[A-Z]{3,5}-\d{3}$`).
  - `details[]` entries changed shape: `{ field, message }` → `{ fieldName, reason, expectedValue?, actualValue?, context? }`.
  - `details` is always an array; omitted entirely when not applicable (previously inconsistent).
- `traits/has-errors.raml` rewritten to cover every HTTP status this API can return (400, 401, 403, 404, 405, 406, 409, 415, 500, 501, 503). Each response body now uses an `examples:` map — one entry per `errorCode` variant that can produce the status (4 variants for 400, 2 for 404, 5 for 409, 2 for 500).
- `securitySchemes/mulesoft-client-id.raml` updated to reference the new `CTLG-AUTH-001` (401) and `CTLG-AUTHZ-001` (403) examples. Previously referenced the deleted `ErrorResponse-{401,403}-example.json` files; Design Center flagged the stale `!include` paths at validation time.
- `ProductUpdateRequest` and `ProductPatchRequest` no longer include `isActive`. Activation state is managed by the dedicated `DELETE /products/{productId}` (deactivate) and (v2) reactivate operations — PUT/PATCH cannot modify `is_active`. Payloads containing `isActive` are rejected with 400 `CTLG-VAL-001` because the request schemas carry `additionalProperties: false`. Rationale: a PUT/PATCH flow shouldn't be able to accidentally reactivate a soft-deleted product; activation is a dedicated operation. The server-side `createProduct` flow still accepts `isActive` on `POST` (so operators can seed inactive imports); updated-type and patched-type payloads reject it.

### Changed
- `GET /products` now returns a flat JSON array of `Product` records. The `{ items, pagination }` envelope is removed; pagination metadata moves to the `X-Total-Count`, `X-Offset`, and `X-Limit` response headers.
- Base currency defaults to `EUR` (was `USD`). All type defaults, descriptions, and examples updated.
- `documentation/errors.md` rewritten to document the new envelope and the full `CTLG` code catalog this API emits.
- `api.raml` registers the new `ErrorDetail` type alongside `ErrorResponse`; per-operation `404` responses now reference `ErrorResponse-CTLG-RES-001.json`.

### Added
- `types/ErrorDetail.raml` — field-level error detail type (`fieldName`, `reason`, `expectedValue`, `actualValue`, `context`).
- 20 code-named error examples under `examples/` covering every HTTP status this API can return, with multiple variants for codes that have multiple triggers (`CTLG-VAL-001` × 4, `CTLG-BUS-001` × 2, `CTLG-BUS-002` × 3).

### Removed
- `types/ProductList.raml`. The list operation types directly as `Product[]`.
- Legacy HTTP-status-named error examples (`ErrorResponse-{400,401,403,404,409,500,503}-example.json`). Replaced by code-named examples per `errorCode`.

## [1.0.0] - 2026-09-19

### Added
- Initial API specification with CRUD operations on `/products`.
- Two functional-monitoring endpoints: `GET /alive` (liveness) and `GET /ready` (readiness with per-dependency detail).
- Seven mandatory documentation chapters under `documentation/`.
- MuleSoft Client ID Enforcement security scheme applied by default; opted out on `/alive` and `/ready`.
- Standard org error envelope wired into every 4xx/5xx response via the `hasErrors` trait.
- Mandatory `X-Correlation-Id` (v4 UUID) enforced on every operational request via the `correlatable` trait.

[Unreleased]: https://exchange.leno.com/catalog-products-sap-api/compare/v2.0.1...HEAD
[2.0.1]: https://exchange.leno.com/catalog-products-sap-api/releases/tag/v2.0.1
[1.0.0]: https://exchange.leno.com/catalog-products-sap-api/releases/tag/v1.0.0
