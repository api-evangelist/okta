# Okta

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

Okta is the workforce identity incumbent — its Identity Cloud platform (also called the Okta Workforce Identity Platform) covers Single Sign-On, Adaptive MFA, Universal Directory, Lifecycle Management, Identity Governance, Privileged Access, Device Access, Identity Threat Protection, Identity Security Posture Management, Access Gateway, and API Access Management. Customer identity is handled by Auth0, which Okta acquired in 2021 and now operates as its consumer-facing identity arm. The platform now extends to securing AI agents via Okta for AI Agents and the Cross-App Access (XAA) protocol — an emerging OAuth profile based on the IETF ID-JAG draft for agent-to-app authorization. The Okta Management API is the primary developer surface, with an official MCP server (okta-mcp-server) exposing administrative operations to LLM agents under human-in-the-loop confirmation.

**Areas:** SSO · Okta · Identity · Workforce Identity · Customer Identity · Authentication · Authorization · Multi-Factor Authentication · Identity Governance · Privileged Access Management · AI Agents · Cross-App Access

## Okta on APIs.io

Everything profiled for Okta is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/okta/](https://apis.io/providers/okta/)** — or go straight to one of the APIs or properties below.

### APIs (37)

- [Cross-App Access (XAA)](https://apis.io/apis/okta/okta-cross-app-access/) — Cross-App Access is Okta's emerging OAuth profile for secure agent-to-app and app-to-app authorization, based on the IETF draft "OAuth Identity Assertion…
- [Okta for AI Agents](https://apis.io/apis/okta/okta-for-ai-agents/) — Okta for AI Agents secures the full lifecycle of AI agents — discovery, registration with mandatory human ownership, least-privilege scope enforcement,…
- [Okta Application API](https://apis.io/apis/okta/okta-application-api/) — The Application API from Okta — 31 operation(s) for application.
- [Okta Authenticator API](https://apis.io/apis/okta/okta-authenticator-api/) — The Authenticator API from Okta — 4 operation(s) for authenticator.
- [Okta AuthorizationServer API](https://apis.io/apis/okta/okta-authorizationserver-api/) — The AuthorizationServer API from Okta — 21 operation(s) for authorizationserver.
- [Okta Brand API](https://apis.io/apis/okta/okta-brand-api/) — The Brand API from Okta — 15 operation(s) for brand.
- [Okta Domain API](https://apis.io/apis/okta/okta-domain-api/) — The Domain API from Okta — 4 operation(s) for domain.
- [Okta EventHook API](https://apis.io/apis/okta/okta-eventhook-api/) — The EventHook API from Okta — 5 operation(s) for eventhook.
- [Okta Feature API](https://apis.io/apis/okta/okta-feature-api/) — The Feature API from Okta — 5 operation(s) for feature.
- [Okta Group API](https://apis.io/apis/okta/okta-group-api/) — The Group API from Okta — 16 operation(s) for group.
- [Okta GroupSchema API](https://apis.io/apis/okta/okta-groupschema-api/) — The GroupSchema API from Okta — 1 operation(s) for groupschema.
- [Okta InlineHook API](https://apis.io/apis/okta/okta-inlinehook-api/) — The InlineHook API from Okta — 5 operation(s) for inlinehook.
- [Okta LinkedObject API](https://apis.io/apis/okta/okta-linkedobject-api/) — The LinkedObject API from Okta — 2 operation(s) for linkedobject.
- [Okta Log API](https://apis.io/apis/okta/okta-log-api/) — The Log API from Okta — 1 operation(s) for log.
- [Okta NetworkZone API](https://apis.io/apis/okta/okta-networkzone-api/) — The NetworkZone API from Okta — 4 operation(s) for networkzone.
- [Okta Org API](https://apis.io/apis/okta/okta-org-api/) — The Org API from Okta — 14 operation(s) for org.
- [Okta Policy API](https://apis.io/apis/okta/okta-policy-api/) — The Policy API from Okta — 8 operation(s) for policy.
- [Okta ProfileMapping API](https://apis.io/apis/okta/okta-profilemapping-api/) — The ProfileMapping API from Okta — 2 operation(s) for profilemapping.
- [Okta Session API](https://apis.io/apis/okta/okta-session-api/) — The Session API from Okta — 3 operation(s) for session.
- [Okta Subscription API](https://apis.io/apis/okta/okta-subscription-api/) — The Subscription API from Okta — 6 operation(s) for subscription.
- [Okta Template API](https://apis.io/apis/okta/okta-template-api/) — The Template API from Okta — 2 operation(s) for template.
- [Okta ThreatInsight API](https://apis.io/apis/okta/okta-threatinsight-api/) — The ThreatInsight API from Okta — 1 operation(s) for threatinsight.
- [Okta TrustedOrigin API](https://apis.io/apis/okta/okta-trustedorigin-api/) — The TrustedOrigin API from Okta — 4 operation(s) for trustedorigin.
- [Okta User API](https://apis.io/apis/okta/okta-user-api/) — The User API from Okta — 36 operation(s) for user.
- [Okta UserFactor API](https://apis.io/apis/okta/okta-userfactor-api/) — The UserFactor API from Okta — 7 operation(s) for userfactor.
- [Okta UserSchema API](https://apis.io/apis/okta/okta-userschema-api/) — The UserSchema API from Okta — 2 operation(s) for userschema.
- [Okta App Authenticator API](https://apis.io/apis/okta/okta-appauthenticator-api/) — The MyAccount App Authenticators API provides operations to enroll, update, and delete an app authenticator. The API also allows users to view and verify…
- [Okta Authenticators API](https://apis.io/apis/okta/okta-authenticators-api/) — <div class="x-lifecycle-container"><x-lifecycle class="lea"></x-lifecycle> <x-lifecycle class="oie"></x-lifecycle></div>The MyAccount Authenticators API…
- [Okta Email API](https://apis.io/apis/okta/okta-email-api/) — The MyAccount Email API provides operations to enroll, update, and delete emails. The API also provides utilities to create, view, and answer verification…
- [Okta Okta Applications API](https://apis.io/apis/okta/okta-oktaapplications-api/) — <div class="x-lifecycle-container"><x-lifecycle class="lea"></x-lifecycle> <x-lifecycle class="oie"></x-lifecycle></div>The MyAccount Profile API provides…
- [Okta Organization API](https://apis.io/apis/okta/okta-organization-api/) — <div class="x-lifecycle-container"><x-lifecycle class="lea"></x-lifecycle> <x-lifecycle class="oie"></x-lifecycle></div>The MyAccount Profile API provides…
- [Okta Password API](https://apis.io/apis/okta/okta-password-api/) — The MyAccount Password API provides operations to enroll, update, and delete passwords. > **Note:** Super admins can enable the IDP MyAccount API password…
- [Okta Phone API](https://apis.io/apis/okta/okta-phone-api/) — The MyAccount Phone API provides operations to enroll, update, and delete phone numbers. The API also provides utilities to create, view, and answer…
- [Okta Profile API](https://apis.io/apis/okta/okta-profile-api/) — The MyAccount Profile API provides operations to enroll and update profile fields. The API also allows viewing of all allowed profile fields. ### API…
- [Okta Sessions API](https://apis.io/apis/okta/okta-sessions-api/) — The MyAccount Sessions API provides operations to manage sessions. See [sessions](/openapi/okta-management/management/tag/Session/) for more information.…
- [Okta Identity Provider API](https://apis.io/apis/okta/okta-identity-provider-api/) — The Identity Provider API from Okta — 16 operation(s) for identity provider.
- [Okta User Type API](https://apis.io/apis/okta/okta-user-type-api/) — The User Type API from Okta — 2 operation(s) for user type.

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### Arazzo workflows (21)

Multi-step workflows that chain operations into a business task.

- [Okta Assign Admin Role to Group](https://apis.io/arazzos/okta/okta-assign-admin-role-to-group-workflow/)
- [Okta Assign Admin Role to User](https://apis.io/arazzos/okta/okta-assign-admin-role-to-user-workflow/)
- [Okta Assign Group to Application](https://apis.io/arazzos/okta/okta-assign-group-to-application-workflow/)
- [Okta Create and Activate Application](https://apis.io/arazzos/okta/okta-create-and-activate-application-workflow/)
- [Okta Create and Activate Authenticator](https://apis.io/arazzos/okta/okta-create-and-activate-authenticator-workflow/)
- [Okta Create and Activate Group Rule](https://apis.io/arazzos/okta/okta-create-and-activate-group-rule-workflow/)
- [Okta Create and Refresh Session](https://apis.io/arazzos/okta/okta-create-and-refresh-session-workflow/)
- [Okta Create Application, Assign User, and Activate](https://apis.io/arazzos/okta/okta-create-app-assign-user-and-activate-workflow/)
- [Okta Create Group and Add Members](https://apis.io/arazzos/okta/okta-create-group-and-add-members-workflow/)
- [Okta Create Policy with Rule and Activate](https://apis.io/arazzos/okta/okta-create-policy-with-rule-workflow/)
- [Okta Create User with Group Membership](https://apis.io/arazzos/okta/okta-create-user-with-group-membership-workflow/)
- [Okta Deactivate and Delete User](https://apis.io/arazzos/okta/okta-deactivate-and-delete-user-workflow/)
- [Okta Enroll and Activate User Factor](https://apis.io/arazzos/okta/okta-enroll-and-activate-factor-workflow/)
- [Okta Find User and Assign Application](https://apis.io/arazzos/okta/okta-find-user-and-assign-app-workflow/)
- [Okta Find User and Suspend](https://apis.io/arazzos/okta/okta-find-user-and-suspend-workflow/)
- [Okta Offboard User and Clear Sessions](https://apis.io/arazzos/okta/okta-offboard-user-clear-sessions-workflow/)
- [Okta Onboard User to Group and Application](https://apis.io/arazzos/okta/okta-onboard-user-to-group-and-app-workflow/)
- [Okta Provision Admin User](https://apis.io/arazzos/okta/okta-provision-admin-user-workflow/)
- [Okta Restore Suspended User](https://apis.io/arazzos/okta/okta-reactivate-suspended-user-workflow/)
- [Okta Reset and Re-enroll User Factor](https://apis.io/arazzos/okta/okta-reset-and-reenroll-factor-workflow/)
- [Okta Update Group Rule Expression](https://apis.io/arazzos/okta/okta-update-group-rule-expression-workflow/)

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Okta Event Hooks and Log Streams](https://apis.io/asyncapis/okta/okta-events-asyncapi/)

#### Event channels (4)

Individual events and webhooks the provider emits.

- [okta/event-hook/delivery](https://apis.io/channels/okta/okta-event-hook-delivery/)
- [okta/event-hook/verification](https://apis.io/channels/okta/okta-event-hook-verification/)
- [okta/log-stream/aws-eventbridge](https://apis.io/channels/okta/okta-log-stream-aws-eventbridge/)
- [okta/log-stream/splunk-cloud](https://apis.io/channels/okta/okta-log-stream-splunk-cloud/)

#### GraphQL (1)

GraphQL schemas.

- [Moved to /providers/okta/](https://apis.io/graphqls/okta/okta-graphql/)

#### MCP servers (2)

Model Context Protocol servers that expose the API to agents.

- [okta](https://apis.io/servers/okta/okta/)
- [Okta MCP Server](https://apis.io/servers/okta/okta-mcp-server/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Okta Agentic Access](https://apis.io/agentic-access/okta/okta-agentic-access/)

#### JSON Schema (462)

The data models the API reads and writes.

- [AccessPolicy](https://apis.io/schemas/okta/okta-accesspolicy/)
- [AccessPolicyConstraint](https://apis.io/schemas/okta/okta-accesspolicyconstraint/)
- [AccessPolicyConstraints](https://apis.io/schemas/okta/okta-accesspolicyconstraints/)
- [AccessPolicyRule](https://apis.io/schemas/okta/okta-accesspolicyrule/)
- [AccessPolicyRuleActions](https://apis.io/schemas/okta/okta-accesspolicyruleactions/)
- [AccessPolicyRuleApplicationSignOn](https://apis.io/schemas/okta/okta-accesspolicyruleapplicationsignon/)
- [AccessPolicyRuleConditions](https://apis.io/schemas/okta/okta-accesspolicyruleconditions/)
- [AccessPolicyRuleCustomCondition](https://apis.io/schemas/okta/okta-accesspolicyrulecustomcondition/)
- [AcsEndpoint](https://apis.io/schemas/okta/okta-acsendpoint/)
- [ActivateFactorRequest](https://apis.io/schemas/okta/okta-activatefactorrequest/)
- [AllowedForEnum](https://apis.io/schemas/okta/okta-allowedforenum/)
- [AppAndInstanceConditionEvaluatorAppOrInstance](https://apis.io/schemas/okta/okta-appandinstanceconditionevaluatorapporinstance/)
- [AppAndInstancePolicyRuleCondition](https://apis.io/schemas/okta/okta-appandinstancepolicyrulecondition/)
- [AppInstancePolicyRuleCondition](https://apis.io/schemas/okta/okta-appinstancepolicyrulecondition/)
- [Okta Application](https://apis.io/schemas/okta/okta-application/)
- [ApplicationAccessibility](https://apis.io/schemas/okta/okta-applicationaccessibility/)
- [ApplicationCredentials](https://apis.io/schemas/okta/okta-applicationcredentials/)
- [ApplicationCredentialsOAuthClient](https://apis.io/schemas/okta/okta-applicationcredentialsoauthclient/)
- [ApplicationCredentialsScheme](https://apis.io/schemas/okta/okta-applicationcredentialsscheme/)
- [ApplicationCredentialsSigning](https://apis.io/schemas/okta/okta-applicationcredentialssigning/)
- [ApplicationCredentialsSigningUse](https://apis.io/schemas/okta/okta-applicationcredentialssigninguse/)
- [ApplicationCredentialsUsernameTemplate](https://apis.io/schemas/okta/okta-applicationcredentialsusernametemplate/)
- [ApplicationFeature](https://apis.io/schemas/okta/okta-applicationfeature/)
- [ApplicationGroupAssignment](https://apis.io/schemas/okta/okta-applicationgroupassignment/)
- [ApplicationLicensing](https://apis.io/schemas/okta/okta-applicationlicensing/)
- …and 437 more published on APIs.io

#### JSON Structure (2)

Data models expressed in JSON Structure.

- [Okta Structure](https://apis.io/json-structure/okta/okta-structure/)
- [Okta User Structure](https://apis.io/json-structure/okta/okta-user-structure/)

#### JSON-LD (1)

Linked-data contexts for the provider's vocabulary.

- [Okta Context](https://apis.io/jsonld/okta/okta-context/)

#### Vocabulary (1)

The provider's domain terms, defined.

- [Okta Vocabulary](https://apis.io/vocabularies/okta/okta-vocabulary/)

#### Examples (4)

Example requests and responses.

- [Okta Application Oidc Example](https://apis.io/examples/okta/okta-application-oidc-example/)
- [Okta Cross App Access Grant Example](https://apis.io/examples/okta/okta-cross-app-access-grant-example/)
- [Okta Group Example](https://apis.io/examples/okta/okta-group-example/)
- [Okta User Active Example](https://apis.io/examples/okta/okta-user-active-example/)

#### Collections (27)

Postman and Open Collection request collections.

- [Okta Application API](https://apis.io/collections/okta/open-okta-application-api/)
- [Okta Application Authenticator API](https://apis.io/collections/okta/open-okta-authenticator-api/)
- [Okta Application AuthorizationServer API](https://apis.io/collections/okta/open-okta-authorizationserver-api/)
- [Okta Application Brand API](https://apis.io/collections/okta/open-okta-brand-api/)
- [Okta Application Domain API](https://apis.io/collections/okta/open-okta-domain-api/)
- [Okta Application EventHook API](https://apis.io/collections/okta/open-okta-eventhook-api/)
- [Okta Application Feature API](https://apis.io/collections/okta/open-okta-feature-api/)
- [Okta Application Group API](https://apis.io/collections/okta/open-okta-group-api/)
- [Okta Application GroupSchema API](https://apis.io/collections/okta/open-okta-groupschema-api/)
- [Okta Application IdentityProvider API](https://apis.io/collections/okta/open-okta-identityprovider-api/)
- [Okta Application InlineHook API](https://apis.io/collections/okta/open-okta-inlinehook-api/)
- [Okta Application LinkedObject API](https://apis.io/collections/okta/open-okta-linkedobject-api/)
- [Okta Application Log API](https://apis.io/collections/okta/open-okta-log-api/)
- [Okta Application NetworkZone API](https://apis.io/collections/okta/open-okta-networkzone-api/)
- [Okta Application Org API](https://apis.io/collections/okta/open-okta-org-api/)
- [Okta Application Policy API](https://apis.io/collections/okta/open-okta-policy-api/)
- [Okta Application ProfileMapping API](https://apis.io/collections/okta/open-okta-profilemapping-api/)
- [Okta Application Session API](https://apis.io/collections/okta/open-okta-session-api/)
- [Okta Application Subscription API](https://apis.io/collections/okta/open-okta-subscription-api/)
- [Okta Application Template API](https://apis.io/collections/okta/open-okta-template-api/)
- [Okta Application ThreatInsight API](https://apis.io/collections/okta/open-okta-threatinsight-api/)
- [Okta Application TrustedOrigin API](https://apis.io/collections/okta/open-okta-trustedorigin-api/)
- [Okta Application User API](https://apis.io/collections/okta/open-okta-user-api/)
- [Okta Application UserFactor API](https://apis.io/collections/okta/open-okta-userfactor-api/)
- [Okta Application UserSchema API](https://apis.io/collections/okta/open-okta-userschema-api/)
- …and 2 more published on APIs.io

#### Governance rules (3)

Spectral rulesets for the provider's contracts.

- [Okta — asyncapi rules](https://apis.io/rules/okta/okta-asyncapi-spectral-rules/)
- [Okta — jsonschema rules](https://apis.io/rules/okta/okta-jsonschema-spectral-rules/)
- [Okta API Rules](https://apis.io/rules/okta/okta-rules/)

#### Security (4)

Authentication, trust centre, and vulnerability disclosure.

- [Okta Authentication](https://apis.io/security/okta/okta-authentication/)
- [Okta Domain Security](https://apis.io/security/okta/okta-domain-security/)
- [Okta Trust Center](https://apis.io/security/okta/okta-trust-center/)
- [Okta Vulnerability Disclosure](https://apis.io/security/okta/okta-vulnerability-disclosure/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Okta Scopes](https://apis.io/scopes/okta/okta-scopes/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Okta Plans Pricing](https://apis.io/plans/okta/okta-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Okta Rate Limits](https://apis.io/rate-limits/okta/okta-rate-limits/)

#### FinOps (1)

What the API costs to run, by operation.

- [Okta Finops](https://apis.io/finops/okta/okta-finops/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.okta.com/)
- [Documentation](https://developer.okta.com/docs/reference/)
- [Getting started](https://www.okta.com/identity-101/digital-onboarding-definition-benefits-how-it-works)
- [Support](https://support.okta.com/)
- [Changelog](https://developer.okta.com/docs/release-notes/)
- [Blog](https://developer.okta.com/blog/)
- [Security](https://bugcrowd.com/auth0-okta)
- [Compliance](https://security.okta.com/)
- [Terms of service](https://developer.okta.com/terms/)
- [Privacy policy](https://www.okta.com/privacy-policy/)
- [GitHub](https://github.com/okta)
- [Status](https://status.okta.com/)

## API Evangelist rating

**Kin Score 73.8** — exemplar · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 70.0 |
| Contract quality | 75.3 |
| Contract governance | 45.5 |
| Operational transparency | 52.6 |
| Developer ergonomics | 83.9 |
| Access clarity | 61.8 |

**Agent readiness 51.1** — agent-ready (gated down from agent-native)

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | verified |
| Auth Clarity | served (partial credit) |
| Idempotency | — |
| Error Semantics | verified |
| OpenAPI Examples | partial |
| Rate Limit Signal | derived (partial credit) |
| Event Surface Described | derived (partial credit) |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | served (partial credit) |
| Protected Resource Metadata | verified |
| Dynamic Client Registration | yes |
| Agentic Commerce | — |
| Pagination Ratio | — |
| Operation Distinctiveness | mostly |

**Regulatory alignment 45.1** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/okta/](https://apis.io/providers/okta/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Okta: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/okta/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
