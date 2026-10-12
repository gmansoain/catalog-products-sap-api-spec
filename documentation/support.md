# Support

## Owner

**Team:** Catalog Platform Team
**Slack:** #catalog-platform
**Email:** catalog-platform@leno.com
**On-call:** https://leno.pagerduty.com/schedules/catalog-platform

## Reporting an issue

1. Capture the `correlationId` from the error response (or the `X-Correlation-Id` you sent if the call timed out).
2. Note the environment (DEV / SIT / UAT / PROD), endpoint, HTTP method, and approximate timestamp.
3. Open a ticket in [Jira: CATALOG](https://leno.atlassian.net/browse/CATALOG) using the **API Issue** template, or post in `#catalog-platform` for quick triage.

## Security incidents

Do **not** post security issues in public channels. Email security@leno.com or follow the org's vulnerability disclosure process at https://security.leno.com/disclosure.

## SLAs

Catalog Products SAP System API is classified as **Silver** tier.

| Severity | Response | Resolution target |
|----------|----------|-------------------|
| Sev 1 — outage | 30 min | 4 h |
| Sev 2 — degraded | 2 h | 1 business day |
| Sev 3 — minor | 1 business day | 5 business days |

## Status page and dashboards

- Status page: https://status.leno.com
- Anypoint Monitoring dashboard: https://anypoint.mulesoft.com/monitoring/api/catalog-products-sap-api
- Liveness endpoint: `<base-url>/alive`
- Readiness endpoint (dependency detail): `<base-url>/ready`

## Documentation

- API reference in Anypoint Exchange
- [Authentication](authentication.md)
- [Environments](environments.md)
- [Errors](errors.md)
- [Changelog](changelog.md)
