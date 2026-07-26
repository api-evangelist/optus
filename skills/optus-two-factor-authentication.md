---
name: optus-two-factor-authentication
description: Issue and validate an SMS one-time-code challenge with the Optus SMS Suite 2FA REST API, which is documented but ships no OpenAPI definition.
api: null
base_url: https://api.sms.optus.com.au/rest/2fa
operations:
  - POST /challenges
  - POST /responses
generated: '2026-07-25'
method: generated
source: https://sms.optus.com.au/docs/en/integrations/messaging-apis/2fa/
---

# Two-factor authentication over SMS (Optus SMS Suite 2FA REST API)

> **No machine-readable contract.** Optus publishes no OpenAPI or Swagger document for this
> API, so nothing here is grounded in a spec file — every operation, path, and header below is
> transcribed from the published documentation page. Treat request/response shapes as
> documented prose, and validate against a live sandbox call before relying on them. This is
> the single largest spec gap on the Optus surface: the 2FA pair is the most useful
> agent-facing capability Optus offers and it has no contract.

## Before you start

- The 2FA application must be provisioned; ask the support desk if it is not.
- HTTP Basic credentials — your application username is the same as the application name shown
  on the platform's 2FA configuration page.
- Every request needs `Accept: application/vnd.modica.2fa.v1+json` (a custom media type, not
  `application/json`) and `Content-Type: application/json`.
- Destinations in E.164, e.g. `+61414123456`.
- If you customise the message copy, it **must** contain the named variable `%(code)s` — that
  is where the generated one-time code is substituted. Omit it and the recipient gets no code.
- An alphanumeric `mask` is only honoured where the destination country and carrier support it;
  in Australia sender-ID rules apply.

## Step 1 — Create the challenge

`POST /challenges` with at minimum `{"destination": "+61414123456"}`.

The platform generates the code, sends it by SMS, and returns a challenge identifier. Optional
controls documented on the page: custom message content (containing `%(code)s`), an optional
alphanumeric mask, and a configurable validity window.

Store the returned challenge id against the user's session. It is the only handle for the
validation step.

## Step 2 — Collect the code from the user

Nothing API-side happens here. Prompt the user, and let your own UI enforce attempt limits —
the API documents no lockout or attempt-count contract, so brute-force protection is your
responsibility.

## Step 3 — Validate the response

`POST /responses` with the challenge id and the code the user entered. Check the response
status and branch on failed versus expired codes in your application.

## Rules an agent must follow

- **Never send the code to the model or the caller.** The code exists only between the
  handset and the user. An agent orchestrating this flow issues the challenge and validates the
  answer; it must not read, log, or echo the code.
- **Do not retry a challenge as an idempotency recovery.** There is no idempotency key on any
  Optus interface. Re-issuing `POST /challenges` sends a second SMS, bills a second message,
  and invalidates the user's mental model of which code is current.
- **Do not use this for bulk sends.** For ordinary notifications use REST v2
  (`skills/optus-send-and-reconcile-sms.md`); for marketing use the Campaign Manager.
- 2FA messages are transactional, so the consent rules that govern commercial SMS do not apply
  — but sender identification still does.

## Errors

`401` for missing or wrong credentials, or no permission for this API. No per-code error table
is published for 2FA specifically; the general REST error vocabulary
(`invalid_json`, `missing_attrib`, `invalid_attrib`) applies. See
`errors/optus-error-codes.yml`.

## Health

The status page tracks **Two Factor Authentication** as its own component
(`https://status.sms.optus.com.au/`). Check it before assuming a validation failure is the
user's fault.
