# Environments

Catalog Products SAP System API is available in the following environments. Each environment uses a distinct base URL, credentials, and backing SAP tenant.

## Environment list

| Environment | Purpose | Base URL | Notes |
|-------------|---------|----------|-------|
| DEV | Active development | `http://dev.api.leno.com/catalog-products-sap/v2` | Backed by the shared demo `ecommerce_sap` schema; data may be reset without notice. |
| SIT | System integration testing | `http://sit.api.leno.com/catalog-products-sap/v2` | Refreshed nightly from a SAP export snapshot. |
| UAT | User acceptance | `http://uat.api.leno.com/catalog-products-sap/v2` | Production-like SAP tenant; refreshes on release cadence. |
| PROD | Production | `http://api.leno.com/catalog-products-sap/v2` | Live SAP tenant; internal Mule consumers only. |

Scheme is `http://` on every environment — this is a System API and TLS terminates at the API gateway. Consumers must call through the gateway, not directly.

## Credentials per environment

- DEV / SIT: self-service via the internal Anypoint Exchange portal.
- UAT / PROD: requested via the Catalog Platform team with change-management approval.
- Credentials are **not** portable across environments. A DEV `client_id` will not authenticate in PROD.

## Connectivity

- Reachable only from within the LENO VPC — public access is denied by the gateway.
- No mTLS required — Client ID Enforcement is the only auth control on this API.

## Data refresh and freshness

- DEV: data may be reset at any time.
- SIT: synthetic data refreshed nightly at 02:00 UTC.
- UAT: SAP export refreshed on each release cut (roughly fortnightly).
- PROD: live SAP data; never refreshed from a non-prod source.

## Health check

Each environment exposes two unauthenticated functional-monitoring endpoints:

```
<base-url>/alive
<base-url>/ready
```

- `/alive` returns `200 OK` whenever the process is running.
- `/ready` pings the sole dependency (`SAP`, simulated in the demo by the `ecommerce_sap` MySQL schema) and returns `200` with `status: "OK"` when it is `UP`, or `503` with `status: "OFFLINE"` when it is `DOWN`. See [Errors](errors.md) for the full mapping.
