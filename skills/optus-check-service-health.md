---
name: optus-check-service-health
description: Check whether Optus SMS Suite services are available before sending production traffic, using the unauthenticated Health Monitoring API and the Statuspage feed.
api: openapi/optus-sms-suite-status-openapi.json
base_url: https://api.sms.optus.com.au/rest/services/v1
operations:
  - GET /services
  - GET /status
generated: '2026-07-25'
method: generated
source: https://sms.optus.com.au/docs/en/integrations/api-documentation/health-api/
---

# Check Optus SMS Suite service health

The one Optus API you can call with no credentials at all. Use it as a pre-flight check and as
an alerting signal — never as a delivery signal.

## Step 1 — Discover the service names

`GET /services` returns `{"services": [...]}`.

Live values observed 2026-07-25: `https`, `rest`, `soap`, `smpp`, `restv2`, `email2sms`.

Service names are **case-sensitive**. An unsupported or wrongly cased name comes back with
status `unknown` rather than an error, so validate against this list rather than guessing.

## Step 2 — Query the services you actually depend on

`GET /status?service=rest&service=smpp` — the `service` query parameter repeats, one per
service.

```
GET https://api.sms.optus.com.au/rest/services/v1/status?service=restv2&service=smpp
→ 200 {"services":{"restv2":{"status":"available"},"smpp":{"status":"available"}}}
```

Per-service `status` is one of `available`, `unavailable`, `unknown`.

**Calling `/status` with no `service` parameter returns `{"services":{}}`** — an empty object,
not everything. You must name what you want.

## Step 3 — Respect the cache interval

The platform polls each service once every 60 seconds from an external, geographically
distributed provider independent of its own infrastructure, and caches the result. Polling
faster than once a minute returns the same cached answer and adds no information.

## Step 4 — Handle degradation of the health API itself

`503` means the health-check service is temporarily unavailable — that is a statement about the
monitor, not about the messaging services. Do not fail your integration open or closed on a
`503`; fall back to the status page.

## Complementary signal — the status page

`https://status.sms.optus.com.au/` is an Atlassian Statuspage with a public JSON API and 17
tracked components (Mobile Gateway (API), Campaign Manager, Two Factor Authentication,
Email2SMS, Web2SMS, Mobile Studio, SSO, and others):

- `https://status.sms.optus.com.au/api/v2/status.json` — overall indicator
- `https://status.sms.optus.com.au/api/v2/components.json` — per-component state
- `https://status.sms.optus.com.au/api/v2/incidents.json` — incident history
- `https://status.sms.optus.com.au/api/v2/scheduled-maintenances.json` — planned windows
- `https://status.sms.optus.com.au/history.rss` — RSS

Subscribe to the status page for one specific reason beyond outages: **SSL certificate changes
for `*.sms.optus.com.au` are announced there as scheduled maintenance.** If you pin
certificates in a custom trust store, that feed is how you find out before your integration
breaks. The provider recommends pinning the DigiCert intermediate CA rather than the leaf; both
are published in PEM at
`https://sms.optus.com.au/docs/en/integrations/api-documentation/ssl-certificates/`.

## What health does not tell you

Availability is not deliverability. A service reporting `available` says nothing about whether
your messages reach handsets — that only ever arrives on the DLR callback. See
`skills/optus-send-and-reconcile-sms.md`.
