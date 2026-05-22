# Okta (okta)

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
