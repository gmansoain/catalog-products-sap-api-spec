# Getting Started

## Prerequisites

- `client_id` / `client_secret` for the target environment — see [Authentication](authentication.md).
- Base URL for the target environment — see [Environments](environments.md).
- HTTP client (curl, Postman, or your preferred library).

## Your first call

Replace `<client_id>` / `<client_secret>` with the credentials issued for your consumer application and `<base-url>` with the appropriate URL from [Environments](environments.md).

```bash
curl -X GET <base-url>/products?limit=5 \
  -H "client_id: <client_id>" \
  -H "client_secret: <client_secret>" \
  -H "X-Correlation-Id: $(uuidgen)" \
  -H "Accept: application/json"
```

`X-Correlation-Id` is **mandatory** on every request (this is a System API) — missing or malformed values are rejected with `400`.

### Expected response (200 OK)

```http
HTTP/1.1 200 OK
Content-Type: application/json
X-Correlation-Id: 5f9c2e1a-3b8d-4f7e-9a1c-2d4e6f8a0b3c
X-Total-Count: 12
X-Offset: 0
X-Limit: 5
```

```json
[
  {
    "productId": "PROD-000001",
    "productDescription": "Sony Playstation 5",
    "productCategory": "Games Console",
    "imagePath": "images/playstation_5.png",
    "unitPrice": 499.99,
    "currency": "EUR",
    "brand": "Sony",
    "longDescription": "Next-gen gaming console with ultra-high-speed SSD, 4K graphics and haptic-feedback DualSense controller.",
    "weightKg": 4.50,
    "warrantyMonths": 12,
    "isActive": true,
    "createdAt": "2026-09-01T09:14:22Z",
    "updatedAt": "2026-09-15T10:22:47Z"
  }
]
```

The body is a flat JSON array of `Product` records — pagination metadata lives in the `X-Total-Count`, `X-Offset`, and `X-Limit` response headers. The response also echoes back the `X-Correlation-Id` you supplied on the request.

## Health checks

Two unauthenticated probes are available for functional monitoring:

- `GET /alive` — returns `200 OK` whenever the process is up. Does not exercise any dependency.
- `GET /ready` — pings the sole dependency (`SAP`, simulated in the demo by the `ecommerce_sap` MySQL schema) and returns `200` with `status: "OK"` when it is `UP`, or `503` with `status: "OFFLINE"` when it is `DOWN`.

See [Errors](errors.md) for the full status-code mapping and body semantics.

## Forward-compatibility note

Responses may include fields not documented in this spec. Consumers must **ignore unknown fields**. New non-breaking response fields are added over time; clients that reject unknown fields will fail unnecessarily.

## Next steps

- Full API reference in Anypoint Exchange
- [Errors](errors.md) — error envelope and code catalog
- [Support](support.md) — owner, on-call, issue reporting
