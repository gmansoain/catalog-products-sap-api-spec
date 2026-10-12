# Authentication

Catalog Products SAP System API uses **MuleSoft Client ID Enforcement** at the gateway. Every request must carry a valid `client_id` and `client_secret`; unauthenticated calls return `401 Unauthorized`.

This is the org-standard scheme for Process and System APIs. There is no token exchange — credentials are sent on every call.

## Obtaining credentials

1. Request a client app in Anypoint Exchange against `catalog-products-sap-api`.
2. Approve the contract; copy the issued `client_id` and `client_secret`.
3. Store credentials in your secrets manager — never commit them to source control.

## Sending credentials

Send credentials on every request via headers:

```
client_id: YOUR_CLIENT_ID
client_secret: YOUR_CLIENT_SECRET
```

## Consumer scope

Access is granted at the API-Manager contract level, not through OAuth scopes. Contracts are issued only to internal Mule apps in the `catalog` and `ingestion` domains. Any consumer requesting a contract must justify the read/write access it needs — least-privilege applies.

## Authentication errors

| Status | Cause | Action |
|--------|-------|--------|
| 401 | Missing or invalid `client_id` / `client_secret` | Verify credentials and retry. |
| 403 | Credentials valid but the contract does not grant access to this resource | Request access through the Catalog Platform team. |

## Correlation

Every request must carry a `X-Correlation-Id` header (RFC 4122 v4 UUID). This is not an authentication concern but is enforced alongside auth at the gateway — see [Errors](errors.md).
