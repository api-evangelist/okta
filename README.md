# Okta (okta)

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
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Okta is the workforce identity incumbent. Its Identity Cloud platform — also branded the Okta Workforce Identity
Platform — covers Single Sign-On, Adaptive MFA, Universal Directory, Lifecycle Management, Identity Governance,
Privileged Access, Device Access, Identity Threat Protection, Identity Security Posture Management, Access Gateway,
and API Access Management. Customer identity is handled by **Auth0**, which Okta acquired in 2021 and operates as
its consumer-facing identity arm. The platform now extends to securing AI agents via **Okta for AI Agents** and the
**Cross-App Access (XAA)** protocol — an emerging OAuth profile based on the IETF *Identity Assertion Authorization
Grant* (ID-JAG) draft for agent-to-app authorization. The Okta Management API is the primary developer surface, with
an official **MCP server** (`okta-mcp-server`) exposing administrative operations to LLM agents under human-in-the-loop
confirmation.

**URL:** [apis.yml on GitHub](https://raw.githubusercontent.com/api-evangelist/okta/refs/heads/main/apis.yml)

## Scope

- **Type:** Contract
- **Position:** Consuming
- **Access:** 3rd-Party
- **x-type:** company

## Tags

- Identity
- Workforce Identity
- Customer Identity
- Authentication
- Authorization
- Single Sign-On
- Multi-Factor Authentication
- Identity Governance
- Privileged Access
- AI Agents
- Cross-App Access
- MCP
- Platform

## Timestamps

- **Created:** 2023-11-20
- **Modified:** 2026-05-22

## APIs

### Okta API (Management)

Unified identity and access management interface for the Okta Workforce Identity Platform. 26 resource tags
across 338 operations cover applications, authenticators, authorization servers, brands, domains, event hooks,
features, groups, group schemas, identity providers, inline hooks, linked objects, system logs, network zones,
org, policies, profile mappings, sessions, subscriptions, templates, ThreatInsight, trusted origins, users, user
factors, user schemas, and user types. Access uses scoped OAuth 2.0 tokens or legacy API tokens; rate limits apply
per org and SKU.

- **Human URL:** https://developer.okta.com/docs/reference/
- **Base URL:** https://your-subdomain.okta.com
- **OpenAPI (local):** [`openapi/okta-openapi-original.yml`](openapi/okta-openapi-original.yml)
- **OpenAPI (source of truth):** https://github.com/okta/okta-management-openapi-spec (`dist/current/`)

### Cross-App Access (XAA)

Okta's emerging OAuth profile for secure agent-to-app and app-to-app authorization, based on the IETF draft
`draft-ietf-oauth-identity-assertion-authz-grant` (ID-JAG). An IdP-minted identity assertion is exchanged for a
scoped resource access token, eliminating long-lived unmanaged credentials between AI agents and SaaS apps.
[`xaa.dev`](https://xaa.dev/) hosts the public sandbox.

### Okta for AI Agents

Add-on covering AI agent discovery, registration with mandatory human owner, least-privilege scope enforcement,
runtime monitoring, and instant revocation — all within the Okta identity control plane. Sold as an add-on across
Workforce Identity SKUs.

## Common Properties

- [Website](https://www.okta.com/)
- [Portal](https://developer.okta.com/)
- [Documentation](https://developer.okta.com/docs/reference/)
- [Authentication](https://developer.okta.com/docs/guides/implement-grant-type/authcodepkce/main/)
- [GitHub Organization](https://github.com/okta) (88 public repos)
- [Status](https://status.okta.com/)
- [Support](https://support.okta.com/)
- [Concepts](https://developer.okta.com/docs/concepts/)
- [Guides](https://developer.okta.com/docs/guides/)
- [SDKs](https://developer.okta.com/code/)
- [Change Log](https://developer.okta.com/docs/release-notes/)
- [Login](https://developer.okta.com/login/)
- [Sign Up](https://developer.okta.com/signup/)
- [Blog](https://developer.okta.com/blog/)
- [Plans](https://www.okta.com/pricing/)
- [Forum](https://devforum.okta.com/)
- [Terms of Service](https://developer.okta.com/terms/)
- [Privacy Policy](https://www.okta.com/privacy-policy/)
- [Okta Integration Network](https://www.okta.com/integrations/)
- [Cross-App Access Sandbox (xaa.dev)](https://xaa.dev/)

## SDKs, CLIs and Tools

| Type | Repo |
|---|---|
| MCP Server | https://github.com/okta/okta-mcp-server |
| CLI | https://github.com/okta/okta-cli |
| CLI (Go) | https://github.com/okta/okta-cli-client |
| Terraform | https://github.com/okta/terraform-provider-okta |
| Terraform (PAM) | https://github.com/okta/terraform-provider-oktapam |
| OpenAPI Spec | https://github.com/okta/okta-management-openapi-spec |
| Java | https://github.com/okta/okta-sdk-java |
| .NET | https://github.com/okta/okta-sdk-dotnet |
| Go | https://github.com/okta/okta-sdk-golang |
| Python | https://github.com/okta/okta-sdk-python |
| Node.js | https://github.com/okta/okta-sdk-nodejs |
| JavaScript Auth | https://github.com/okta/okta-auth-js |
| React | https://github.com/okta/okta-react |
| Angular | https://github.com/okta/okta-angular |
| Vue | https://github.com/okta/okta-vue |
| React Native | https://github.com/okta/okta-react-native |
| Swift (iOS/macOS) | https://github.com/okta/okta-mobile-swift |
| Kotlin (Android) | https://github.com/okta/okta-mobile-kotlin |
| Spring Boot | https://github.com/okta/okta-spring-boot |
| Sign-In Widget | https://github.com/okta/okta-signin-widget |
| OIDC Middleware (Node) | https://github.com/okta/okta-oidc-middleware |
| JWT Verifier (Java) | https://github.com/okta/okta-jwt-verifier-java |
| JWT Verifier (Python) | https://github.com/okta/okta-jwt-verifier-python |
| Design System | https://github.com/okta/odyssey |
| Workflows Templates | https://github.com/okta/workflows-templates |
| Customer Detections | https://github.com/okta/customer-detections |

## Artifacts in this Repo

- **OpenAPI:** `openapi/okta-openapi-original.yml` (Okta Management API v2.16.0, 338 operations, 26 tags)
- **Naftiko Capabilities:** `capabilities/` — 26 per-tag YAML capability files
- **Spectral Rules:** `rules/okta-rules.yml`
- **JSON Schema:** `json-schema/okta-user-schema.json`, `okta-group-schema.json`, `okta-application-schema.json`
- **JSON Structure:** `json-structure/okta-user-structure.json`
- **JSON-LD:** `json-ld/okta-context.jsonld`
- **Examples:** `examples/okta-user-active-example.json`, `okta-group-example.json`,
  `okta-application-oidc-example.json`, `okta-cross-app-access-grant-example.json`
- **Vocabulary:** `vocabulary/okta-vocabulary.yml`
- **Plans / Pricing:** `plans/okta-plans-pricing.yml` (Workforce + Customer Identity + add-ons)
- **Rate Limits:** `rate-limits/okta-rate-limits.yml`
- **FinOps (FOCUS-aligned):** `finops/okta-finops.yml`
- **Blog Index:** `blogs/blogs.json` plus posts (Cross-App Access, XAA, MCP Server, Entitlements, push-MFA)

## Pricing Highlights

| Tier | Price | Notes |
|---|---|---|
| Workforce Starter | $6 / user / mo | $1,500/yr minimum; SSO + MFA + Universal Directory + 5 Workflows |
| Workforce Core Essentials | $14 / user / mo | + Adaptive MFA, Privileged Access, Lifecycle Mgmt, Access Governance |
| Workforce Essentials | $17 / user / mo | + 50 Workflows |
| Workforce Professional | Custom | + Device Access, ISPM, ITP, Sandbox, Unlimited Workflows |
| Workforce Enterprise | Custom | + API Access Mgmt, Access Gateway, M2M Tokens |
| Customer Identity (Auth0) Base | $3K / mo | Enterprise Base; B2C / B2B suites custom |

## Rate Limit Highlights

- Default 600 req/min on most endpoints
- OAuth `/token` 1,000 req/min
- System Log 120 req/min
- `X-Rate-Limit-Limit` / `-Remaining` / `-Reset` headers; HTTP 429 on throttle

## Maintainers

- **FN:** Kin Lane
- **Email:** kin@apievangelist.com
