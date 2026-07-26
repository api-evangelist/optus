---
name: optus-run-sms-campaign
description: Build a recipient list, schedule an SMS campaign against it, and pull the per-recipient delivery report using the Optus SMS Suite Campaign Manager API.
api: openapi/optus-sms-suite-campaign-manager-openapi.yml
base_url: https://api.sms.optus.com.au/rest/campaign_manager/v1
operations:
  - getLists
  - createList
  - deleteList
  - deleteMultList
  - scheduleCampaign
  - getTemplates
  - getReport
generated: '2026-07-25'
method: generated
source: https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/campaign-manager-api/
---

# Run an SMS campaign (Optus SMS Suite Campaign Manager)

Every `operationId` below exists verbatim in
`openapi/optus-sms-suite-campaign-manager-openapi.yml`.

## Before you start

- HTTP Basic credentials for a Campaign Manager–enabled application. A `403 Forbidden` means
  the credentials are valid but that application is not entitled to Campaign Manager.
- Every path is scoped by `{application_name}` — the same value as your Basic auth username.
- Confirm you have consent for every recipient. Australian and New Zealand consent rules
  (express / inferred / deemed) are documented at
  `https://sms.optus.com.au/docs/en/useful-information/message-compliance-and-regulatory-information/`.
  Informational and transactional messages do not need consent; commercial ones do.

## Step 1 — Check what already exists

- `getLists` — `GET /{application_name}/lists` returns every active list with `id`, `name`,
  `created`, and `members`.
- `getTemplates` — `GET /{application_name}/templates` returns `id` and `name` for each saved
  template.

Neither collection paginates. Both return in full, so expect a large payload on a mature
account.

## Step 2 — Create the recipient list

`createList` — `POST /{application_name}/lists`.

Required: `name` (1–50 chars) and `members[]`. Each `Member` requires `destination` in E.164
and may carry `param1`..`param6` (50 chars each) as merge fields.

Optional and worth setting deliberately:

- `remove_opt_outs` (default `true`) — strips previously opted-out recipients. **Leave it
  true.** It is the only opt-out control on the surface; there is no API to read or manage
  opt-out records.
- `strict_validation` (default `true`) — rejects malformed destinations rather than silently
  dropping them.
- `batch` — free-text reference. `email` — address for replies.

Success is `201` with a `Location` header and a `ListCreateResponse` containing `id`. **Store
that id** — it is the only handle for deleting the list later. The spec allows up to 1,000,000
members.

## Step 3 — Schedule the campaign

`scheduleCampaign` — `POST /{application_name}/scheduled_campaigns`.

Required: `name`, `schedule` (RFC 3339 date-time), `members[]`. Supply either `content`
(inline body) or `template` (a saved template name). Also available: `mask` (alphanumeric
sender, max 11 chars), `opt_out_response` and `reply_response` (max 160 chars each),
`remove_opt_outs`, `strict_validation`, `batch`, `email`.

Success is `201` with a `Location` header and a `ScheduleCampaignResponse` containing `id`.
Store the campaign id — it is the key for the report in step 4.

**This is the highest-consequence operation on the entire Optus public surface.** One call can
dispatch up to a million SMS at a scheduled time, there is no preview, no dry-run, and no
documented cancel operation in the API. An agent must get explicit human confirmation of
recipient count, sender mask, and scheduled time before calling it. See
`agentic-access/optus-agentic-access.yml`.

## Step 4 — Pull the delivery report

`getReport` — `GET /{application_name}/{campaign_id}/report` returns a `CampaignReport` array,
one row per recipient: `list_name`, `message_id`, `mobile`, `sent_date`, `status`,
`status_date`, `timezone`.

Campaign `status` values follow the platform reporting vocabulary — `submitted`, `sent`,
`received`, `frozen`, `rejected`, `failed`, `dead`, `expired`. `frozen` is transient (retryable
at the platform), `dead` means an administrator killed it. Full table in
`errors/optus-error-codes.yml`.

Reports are pulled, not pushed: there is no campaign-completion callback. Poll on a schedule
that respects the campaign's own send window.

## Step 5 — Clean up

- `deleteList` — `DELETE /{application_name}/lists/{list_id}`, returns `204`.
- `deleteMultList` — `POST /{application_name}/delete_multiple_lists` with
  `{"list_ids": [...]}`, maximum 100 ids, returns the ids that were actually deleted.

Both are irreversible and there is no undelete. `deleteMultList` may partially succeed —
always compare the returned `list_ids` against what you asked to delete.

## Errors

`401` incorrect username/password · `403` no access to the application or to Campaign Manager ·
`404` wrong URL · `422` validation errors in the data · `4xx` other request problem · `5xx`
service problem. Envelope: `{"code": 401, "message": "unauthenticated for invalid credentials"}`.
