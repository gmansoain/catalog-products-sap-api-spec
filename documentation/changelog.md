# Changelog

All notable changes to Catalog Products SAP System API are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this API adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

The major version of every release matches the API's `version` field (`v<major>`) and the `/v<major>` segment in the `baseUri`.

## [Unreleased]

### Changed
- `GET /products` now returns a flat JSON array of `Product` records. The previous `{ items, pagination }` envelope has been removed; pagination metadata moves to the `X-Total-Count`, `X-Offset`, and `X-Limit` response headers.
- Base currency changed from `USD` to `EUR` — reflected in the `currency` field's default, examples, and description.

### Removed
- `ProductList` type. The list operation types directly as `Product[]`.

---

## [1.0.0] - 2026-09-19

### Added
- Initial release of the SAP System API for the LENO electronics catalog.
- `GET /products` — paginated list with `category`, `brand`, `isActive` filters and `sortBy` / `sortOrder` controls.
- `POST /products` — create a product; backend auto-assigns `productId` (`PROD-XXXXXX`) when omitted.
- `GET /products/{productId}` — retrieve a product by SAP master identifier.
- `PUT /products/{productId}` — full-replace update.
- `PATCH /products/{productId}` — partial update.
- `DELETE /products/{productId}` — soft delete via `isActive=false`.
- `GET /alive` — unauthenticated liveness probe.
- `GET /ready` — unauthenticated readiness probe with per-dependency detail (`SAP`, `Database`) and `OK` / `DEGRADED` / `OFFLINE` aggregated status.
- Seven mandatory documentation chapters (Overview, Getting Started, Authentication, Environments, Errors, Changelog, Support).
- MuleSoft Client ID Enforcement security scheme.
- Mandatory `X-Correlation-Id` header on every operational request.

---

[Unreleased]: https://exchange.leno.com/catalog-products-sap-api/compare/v1.0.0...HEAD
[1.0.0]: https://exchange.leno.com/catalog-products-sap-api/releases/tag/v1.0.0
