# Paragon (paragon-api)

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

Paragon is an embedded iPaaS that lets SaaS companies build native, in-app integrations for their own customers. Rather than shipping a raw public API for anyone to call, Paragon is the infrastructure a SaaS product embeds so each of its end customers - a **Connected User** - can authenticate their own third-party accounts (Salesforce, HubSpot, Slack, Google, and 100+ more) and have data sync, workflows, and actions run on their behalf. Paragon exposes documented REST APIs: a **Connect/SDK API**, a **Workflows API**, an **ActionKit** tool-calling API for AI agents, and a **Proxy API** that forwards requests to third-party providers.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/paragon-api/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/paragon-api/refs/heads/main/apis.yml)

## Access Model (Read This First)

Paragon is a **B2B, contact-sales product**, and its APIs are not a general-purpose open API you sign up for and immediately call:

- **You onboard as a SaaS builder, not an end user.** You create a Paragon **Project**, configure integrations/workflows/tools in the dashboard, and embed the Connect Portal in your app. There is a build-for-free developer tier and a 14-day trial; production plans (Pro, Enterprise) are quote-based - you talk to sales. Pricing is not published.
- **Authentication is a signed JWT per Connected User, not a static API key.** Every API request is made *on behalf of a Connected User* by presenting a **Paragon User Token** - an **RS256-signed JWT** - as `Authorization: Bearer <token>`. Your server signs the token with a private **Signing Key** generated in **Settings > SDK Setup**; Paragon verifies it with the matching public key. The JWT's claims identify the user (`sub`), the project (`aud` = `useparagon.com/{projectId}`), and issue/expiry (`iat`/`exp`, typically 1 hour).
- **Everything is scoped to a Project ID.** A Project ID (e.g. `38b1f170-0c43-4eae-9a04-ab85325d99f7`) uniquely identifies your Paragon application and appears in nearly every path.

Because of this model, the endpoints below are **documented and their paths/methods are confirmed from Paragon's docs**, but they cannot be exercised anonymously - they require a live Project, a Signing Key, and a Connected User who has authorized the relevant integration.

## Base Hosts

| API | Base Host |
|-----|-----------|
| Connect / SDK API | `https://api.useparagon.com` |
| Workflows API | `https://api.useparagon.com` |
| ActionKit API | `https://actionkit.useparagon.com` |
| Proxy API | `https://proxy.useparagon.com` |

On-premise (self-hosted) deployments use `worker-*.{your-hostname}` variants (e.g. `worker-proxy.`, `worker-actionkit.`).

## Tags

- Embedded iPaaS
- Integrations
- Embedded Integrations
- Native Integrations
- Workflow Automation
- Integration Platform
- API Integration
- SaaS Integrations
- Connectors

## Timestamps

- **Created:** 2026-07-12
- **Modified:** 2026-07-12

## APIs

### Paragon Connect API

REST SDK API for the embedded integration experience. Retrieve project and integration metadata, read the authenticated Connected User and their connected credentials, load dynamic action options, and disconnect integrations - all scoped under `/projects/{projectId}/sdk` and authenticated with a Paragon User Token. Backs the Connect Portal and Managed Sync onboarding.

- **Human URL:** [https://docs.useparagon.com/apis/api-reference](https://docs.useparagon.com/apis/api-reference)
- **Base URL:** `https://api.useparagon.com`

Confirmed endpoints include:

- `GET /projects/{projectId}/sdk/metadata`
- `GET /projects/{projectId}/sdk/integrations`
- `GET /projects/{projectId}/sdk/integrations/{integrationType}`
- `GET /projects/{projectId}/sdk/me`
- `PATCH /projects/{projectId}/sdk/me`
- `POST /projects/{projectId}/sdk/actions`
- `GET /projects/{projectId}/sdk/credentials`
- `PATCH /projects/{projectId}/sdk/credentials/{credentialId}`
- `DELETE /projects/{projectId}/sdk/integrations/{integrationId}`

### Paragon Workflows API

Programmatically enable or disable Paragon Workflows for a Connected User, trigger request-type Workflows, and send App Events that fire Workflows.

- **Human URL:** [https://docs.useparagon.com/apis/api-reference](https://docs.useparagon.com/apis/api-reference)
- **Base URL:** `https://api.useparagon.com`

Confirmed endpoints include:

- `POST /projects/{projectId}/sdk/workflows/{workflowId}` (enable)
- `DELETE /projects/{projectId}/sdk/workflows/{workflowId}` (disable)
- `POST /projects/{projectId}/sdk/triggers/{workflowId}`
- `POST /projects/{projectId}/sdk/events/trigger`

### Paragon ActionKit API

Tool-calling API for AI agents. List the third-party actions (tools) available to a Connected User as JSON Schema, then run a tool by name with parameters.

- **Human URL:** [https://docs.useparagon.com/actionkit/api-reference](https://docs.useparagon.com/actionkit/api-reference)
- **Base URL:** `https://actionkit.useparagon.com`

Confirmed endpoints:

- `GET /projects/{projectId}/tools` (alias `GET /projects/{projectId}/actions`)
- `POST /projects/{projectId}/tools` (alias `POST /projects/{projectId}/actions`)

### Paragon Proxy API

Passthrough (formerly "Connect") API that forwards any HTTP request to a third-party integration provider on behalf of a Connected User, reusing the credentials they authorized in the Connect Portal.

- **Human URL:** [https://docs.useparagon.com/api/making-api-requests](https://docs.useparagon.com/api/making-api-requests)
- **Base URL:** `https://proxy.useparagon.com`

Confirmed path patterns (any HTTP verb):

- `ANY /projects/{projectId}/sdk/proxy/{integrationType}/{apiPath}`
- `ANY /projects/{projectId}/sdk/proxy/custom/{integrationId}/{apiPath}`

## Authentication

Every request is authenticated with a **Paragon User Token**: an RS256-signed JWT presented as `Authorization: Bearer <token>`. Your server signs it with the private Signing Key from **Settings > SDK Setup**; Paragon verifies it with the stored public key. See [authentication/paragon-api-authentication.yml](authentication/paragon-api-authentication.yml).

## Common Properties

- [Domain Security](security/paragon-api-domain-security.yml)
- [Authentication](authentication/paragon-api-authentication.yml)
- [Website](https://www.useparagon.com)
- [Documentation](https://docs.useparagon.com)
- [Plans](plans/paragon-api-plans-pricing.yml)
- [Rate Limits](rate-limits/paragon-api-rate-limits.yml)
- [Fin Ops](finops/paragon-api-finops.yml)
- [LinkedIn](https://www.linkedin.com/company/useparagon)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
