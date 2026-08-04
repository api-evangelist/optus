# Optus (optus)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Optus (Singtel Optus Pty Limited) is Australia's second-largest telecommunications carrier, wholly owned by Singapore's Singtel and headquartered in Macquarie Park, Sydney. It runs a national mobile network reaching over 99% of the Australian population, alongside NBN and cable broadband, satellite capacity inherited from AUSSAT, enterprise and government connectivity, IoT/M2M services, and consumer media.

Its API posture is split in two. The only genuinely public, self-serve developer surface Optus operates is **The Optus SMS Suite** — an enterprise messaging platform white-labelled from New Zealand's Modica Group at `sms.optus.com.au`, which publishes open documentation, downloadable OpenAPI/Swagger definitions, a WSDL, delivery-receipt callbacks, and a free-trial signup. Everything else is partner-gated: the IoT Control Centre is a Cisco/Jasper platform behind a referral-coded starter kit, and Optus has no first-party network-API portal — `developer.optus.com.au` and `api.optus.com.au` resolve on Akamai but return HTTP 403 Access Denied.

On network APIs Optus is a stated GSMA Open Gateway participant and an endorser of the Bridge Alliance API Exchange (BAEx), but as of this review no CAMARA API is callable from Optus directly. Its route to developers runs through its parent Singtel's Paragon-powered BAEx and through Aduna — not through anything Optus publishes itself.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/optus/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/optus/refs/heads/main/apis.yml)

## Tags

- Telecommunications
- Australia
- Mobile Network Operator
- Messaging
- SMS
- MMS
- Two-Factor Authentication
- Network APIs
- CAMARA
- Open Gateway
- IoT
- 5G
- Broadband
- Satellite
- Enterprise

## Timestamps

- **Created:** 2026-07-25
- **Modified:** 2026-07-25

## APIs

### Optus SMS Suite REST API v1

JSON REST API for sending single and bulk SMS from The Optus SMS Suite, including scheduled sends, broadcast to a list, and retrieval of a submitted message by 64-bit message ID. Authenticated with HTTP Basic credentials issued per application in the SMS Suite platform.

- **Human URL:** [https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/rest-v1/](https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/rest-v1/)
- **Base URL:** `https://api.sms.optus.com.au/rest/gateway`

#### Properties

- [Documentation](https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/rest-v1/)
- [API Reference](https://sms.optus.com.au/docs/api/mobile-gateway-rest-v1.html)
- [OpenAPI](openapi/optus-sms-suite-rest-v1-openapi.yml) — Swagger 2.0

### Optus SMS Suite SMS Gateway REST API v2

High-volume asynchronous SMS API using UUID-based message tracking, mandatory delivery-receipt (DLR) callbacks, and advanced scheduling.

- **Human URL:** [https://sms.optus.com.au/docs/en/integrations/api-documentation/rest-v2/](https://sms.optus.com.au/docs/en/integrations/api-documentation/rest-v2/)
- **Base URL:** `https://api.sms.optus.com.au/rest/sms/v2`

#### Properties

- [Documentation](https://sms.optus.com.au/docs/en/integrations/api-documentation/rest-v2/)
- [API Reference](https://sms.optus.com.au/docs/api/mobile-gateway-rest-v2.html)
- [OpenAPI](openapi/optus-sms-suite-rest-v2-openapi.yml) — OpenAPI 3.0.3

### Optus SMS Suite Campaign Manager API

REST API v1 for programmatic control of SMS marketing campaigns — recipient lists, message templates, scheduled campaigns, and per-campaign reporting.

- **Human URL:** [https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/campaign-manager-api/](https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/campaign-manager-api/)
- **Base URL:** `https://api.sms.optus.com.au/rest/campaign_manager/v1`

#### Properties

- [Documentation](https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/campaign-manager-api/)
- [API Reference](https://sms.optus.com.au/docs/api/campaign-manager.html)
- [OpenAPI](openapi/optus-sms-suite-campaign-manager-openapi.yml) — Swagger 2.0

### Optus SMS Suite Health Monitoring API

Unauthenticated status API returning near real-time availability for individual Optus SMS Suite services, polled every 60 seconds from an external, geographically distributed provider.

- **Human URL:** [https://sms.optus.com.au/docs/en/integrations/api-documentation/health-api/](https://sms.optus.com.au/docs/en/integrations/api-documentation/health-api/)
- **Base URL:** `https://api.sms.optus.com.au/rest/services/v1`

#### Properties

- [Documentation](https://sms.optus.com.au/docs/en/integrations/api-documentation/health-api/)
- [OpenAPI](openapi/optus-sms-suite-status-openapi.json) — OpenAPI 3.0.0
- [Status Page](https://status.sms.optus.com.au/)

### Optus SMS Suite 2FA REST API

Hosted two-factor authentication API — `POST /challenges` creates an SMS one-time-code challenge, `POST /responses` validates the user's entry. No published OpenAPI definition.

- **Human URL:** [https://sms.optus.com.au/docs/en/integrations/messaging-apis/2fa/](https://sms.optus.com.au/docs/en/integrations/messaging-apis/2fa/)

### Optus SMS Suite REST Email API

Companion REST API for scheduling and sending plain-text transactional email over a separate endpoint, with DLR callbacks. No published OpenAPI definition.

- **Human URL:** [https://sms.optus.com.au/docs/en/integrations/api-documentation/rest-email/](https://sms.optus.com.au/docs/en/integrations/api-documentation/rest-email/)

### Optus SMS Suite HTTPS API

Legacy GET-based HTTPS interface for SMS sending, delivery receipts, and inbound message callbacks. No published OpenAPI definition.

- **Human URL:** [https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/https/](https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/https/)
- **Base URL:** `https://api.sms.optus.com.au/message`

### Optus SMS Suite SOAP API

WSDL-described SOAP interface for SMS integration with custom source addressing and message classification.

- **Human URL:** [https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/soap/](https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/soap/)
- [WSDL](https://api.sms.optus.com.au/ModicaSoap.wsdl)

### Optus SMS Suite MMS MM7 API

Implementation of the 3GPP MM7 SOAP protocol for sending and receiving MMS with text, image, audio, video, and SMIL content.

- **Human URL:** [https://sms.optus.com.au/docs/en/integrations/messaging-apis/mm7/](https://sms.optus.com.au/docs/en/integrations/messaging-apis/mm7/)

### Optus SMS Suite SMPP API

Direct SMSC connectivity over SMPP 3.3 and 3.4 with mandatory TLS. A wire protocol, not an HTTP API.

- **Human URL:** [https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/smpp/](https://sms.optus.com.au/docs/en/integrations/messaging-apis/sms-apis/smpp/)

## CAMARA / GSMA Open Gateway Posture

**Stated GSMA Open Gateway participant and Bridge Alliance API Exchange endorser with nothing callable — a press release, not an implementation.**

- **CAMARA APIs with real evidence at Optus:** none.
- **GSMA Open Gateway:** named in Australian trade press as a participant alongside Telstra.
- **Aduna:** Optus is not a venture partner; its parent Singtel is. Optus reaches Aduna indirectly.
- **Bridge Alliance API Exchange (BAEx):** Optus is one of fourteen endorsing operators. BAEx runs on Singtel's Paragon orchestration platform and markets Silent Network Authentication APIs (Number Verify, SIM Swap) to multi-market enterprises under a single contract.
- **Telstra went first in Australia**, going live with Number Verification and releasing Number Verification and SIM Swap to Aduna Global. Optus's own on-record contribution is a spokesperson statement about "a major step in making available standardised, network-level capabilities" and a promise of more "in the next few weeks."
- **No CIBA.** CAMARA specifies OIDC and Client-Initiated Backchannel Authentication for network-based authorization. Neither appears anywhere in Optus's public surface — `/.well-known/openid-configuration` returns 404 on both `www.optus.com.au` and `sms.optus.com.au`. The published auth model is HTTP Basic.
- **No TM Forum Open API conformance certification** was found.
- **No NEF/SCEF, network-slicing, or edge/MEC API** is publicly documented.

## Links

- [Website](https://www.optus.com.au/)
- [Developer Docs](https://sms.optus.com.au/docs/en/developer-docs/)
- [Getting Started](https://sms.optus.com.au/docs/en/developer-getting-started/)
- [Free Trial Signup](https://sms.optus.com.au/customer/online_signup)
- [Status Page](https://status.sms.optus.com.au/)
- [Security](https://sms.optus.com.au/docs/en/security/)
- [LinkedIn](https://www.linkedin.com/company/optus)
- [Wikipedia](https://en.wikipedia.org/wiki/Optus)
