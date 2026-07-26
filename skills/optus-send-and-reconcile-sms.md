---
name: optus-send-and-reconcile-sms
description: Send a single SMS through the Optus SMS Suite REST API v2 and reconcile its real delivery outcome from the DLR callback rather than from the submit response.
api: openapi/optus-sms-suite-rest-v2-openapi.yml
base_url: https://api.sms.optus.com.au/rest/sms/v2
operations:
  - POST /messages
  - GET /messages
generated: '2026-07-25'
method: generated
source: https://sms.optus.com.au/docs/en/integrations/api-documentation/rest-v2/
---

# Send and reconcile an SMS (Optus SMS Suite REST v2)

The provider's REST v2 spec declares no `operationId`s, so operations below are named by
method and path exactly as they appear in
`openapi/optus-sms-suite-rest-v2-openapi.yml`.

## Before you start

- You need per-application Basic credentials ("application name" + password) issued at
  `https://sms.optus.com.au/gateway/api_config/restv2`. There is no self-service token
  endpoint, no OAuth, and no key-issuance API.
- A **DLR callback URL must already be configured** for the application in the platform's API
  Configuration screen. On REST v2 this is not optional: without it you never receive error
  codes or delivery outcomes at all. The URL is configured per application, never per request.
- If the application has any authorised IP addresses registered, your calling host must be one
  of them or every request fails authentication.
- Destination numbers must be E.164 with a leading `+` and no leading zero, e.g.
  `+61414123456`.

## Step 1 — Submit the message

`POST /messages` with HTTP Basic auth and `Content-Type: application/json`.

Required body fields (from `components.schemas.SingleMessage`): `destination`, `content`.
Useful optional fields: `source`, `reference`, `mask`, `scheduled`, `class`, `sms_class`,
`integration_id`.

A success is **HTTP 202 Accepted**, not 201 and not 200. The response body is a
`StatusCallback` object carrying the `id` — a UUID.

## Step 2 — Store the UUID before doing anything else

The UUID is the only key that ties the submit to every later event. There is no request-id
header and no other correlation mechanism. If you drop it you cannot reconcile the message.

Set `reference` on submit if you need your own key echoed back — it is returned on retrieval
and on the DLR callback. **`reference` is not an idempotency key.** The API de-duplicates
nothing: if you retry a submit, a second SMS is sent and billed. Guard retries on your side.

## Step 3 — Do not treat 202 as delivered

202 means accepted for processing. Delivery outcome arrives asynchronously on the DLR callback
your application hosts. Handle these `status` values (published DLR set):

| status | meaning |
|---|---|
| `sent` | sent by the carrier transport |
| `received` | received by the handset |
| `rejected` | the carrier rejected the message |
| `expired` | carrier could not deliver in time (e.g. handset off) |

The spec enum in `components.schemas.StatusCallback` is wider than the documented table and
also contains `accepted`, `queued`, `submitted`, `blocked`, `deficient`, `frozen`, `failed`,
`dead`. Treat any unrecognised value as non-terminal and keep the message open rather than
failing hard.

Callback rules the provider states: use HTTPS, the TLS certificate must match the domain,
self-signed certificates will not verify, and any credentials embedded in the callback URL must
be URL-encoded.

## Step 4 — Poll only as a fallback

`GET /messages?id={uuid}` returns a `MessageDetails` object (`id`, `source`, `destination`,
`direction`, `reference`, `content`, `status`). Use it to reconcile a message whose callback
you may have missed — not as the primary delivery signal. A `404` means no message matched
that id.

## Scheduling

Set `scheduled` to an RFC 3339 timestamp (`YYYY-MM-DDThh:mm:ss±hh:mm`). Two rules are enforced:
`400` if the value is not RFC 3339, `422` if it is in the past. The documented upper bound is
60 days ahead.

## Errors

Submit errors return `send_failed`, `invalid_json`, `missing_attrib`, or `invalid_attrib`
inside an `ApiErrorResponse` (`message`, `statusCode`, `error`). `401` means missing or wrong
credentials, or no permission for this API. Full catalogue across every interface:
`errors/optus-error-codes.yml`.

## What this API does not give you

No rate-limit headers and no published request ceiling — build your own throttle. No
pagination. No bulk send on v2: for multiple destinations use REST v1
`POST /messages/broadcast` (up to 1000) or the Campaign Manager.
