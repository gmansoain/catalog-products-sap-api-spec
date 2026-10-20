# Changelog

## 1.0.0 — 2026-10-15

Initial release. Extracted from `catalog-products-sap-api-spec@2.0.1`:

- `types/ErrorResponse.raml` — envelope datatype.
- `types/ErrorDetail.raml` — field-level detail datatype.
- `traits/has-errors.raml` — standard 4xx/5xx response set (11 responses):
  400, 401, 403, 404, 405, 406, 409, 415, 500, 501, 503.
- 20 example payloads keyed on `CTLG-*` codes.

No shape changes vs. the original catalog spec — this release just moves
the assets into a reusable location.
