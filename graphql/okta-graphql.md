# Okta GraphQL Schema

## Overview

Okta does not currently expose a public GraphQL API endpoint. The platform's primary developer surface is the Okta Management REST API, supplemented by the Events API (AsyncAPI) and the MCP server for LLM-agent access. This document describes a conceptual GraphQL schema derived from Okta's published REST API surface, data models, and JSON Schemas. It reflects the types, relationships, and operations that a GraphQL layer over the Okta Management API would expose.

- **Provider**: Okta
- **Category**: Identity and Access Management (IAM)
- **REST Reference**: https://developer.okta.com/docs/reference/
- **OpenAPI Source**: https://github.com/okta/okta-management-openapi-spec
- **Schema File**: okta-schema.graphql
- **Schema Source**: conceptual — derived from REST API surface and JSON Schemas
- **Types Count**: 72

## Domain Coverage

### User and Directory

Core identity objects. `User` is the central entity in the Universal Directory, carrying a `UserProfile` with standard and custom attributes governed by `UserSchema`. `UserType` determines which schema a user conforms to. `LinkedObject` represents directional peer relationships between users (e.g., manager/subordinate).

### Groups and Rules

`Group` aggregates users and drives application assignment, policy targeting, and role delegation. `GroupRule` automates membership via expression-based conditions. `GroupMembership` is the explicit join between a user and a group. `GroupSchema` extends the group profile with custom attributes.

### Applications and Assignment

`Application` represents an integrated app registered in the Okta Integration Network or a custom connector. `ApplicationCredentials` holds signing keys and client secrets. `AppUser` is the per-app user assignment record including app-specific profile attributes. `AppGroup` is the per-app group assignment.

### Sessions and Authentication

`Session` is a browser session created after primary authentication. `AuthTransaction` tracks a multi-step authentication flow. `Factor` (also `MFAFactor`) is an enrolled authenticator for a user. `Challenge` is the in-progress verification of a factor. `SignOnPolicy` and `SignOnPolicyRule` express step-up and re-authentication requirements.

### Authorization Servers and OAuth

`AuthServer` is an OAuth 2.0 / OIDC authorization server managed by Okta (API Access Management, Enterprise SKU). `Scope` and `Claim` are the OAuth primitives it exposes. `OAuthClient` is a registered OIDC/OAuth application. `AccessToken`, `RefreshToken`, and `IntrospectionResult` represent token lifecycle objects.

### Policies and Rules

`Policy` is the generic policy container (sign-on, password, MFA enrollment, lifecycle). `PolicyRule` is an ordered condition-action pair within a policy. `PolicyCondition` expresses the matching predicate (network zone, group membership, risk level, etc.).

### Identity Threat and Network Security

`ThreatInsight` is Okta's IP reputation and anomaly detection configuration. `NetworkZone` defines IP-range or ASN-based network perimeters used in policy conditions. `BehaviorRule` captures anomalous authentication behavior definitions. `BehaviorEvent` is an instance of a triggered behavioral signal.

### Hooks and Automation

`EventHook` delivers Okta system events to external HTTPS endpoints. `InlineHook` intercepts Okta flows (import, SAML assertion, token, registration) for external enrichment. `InlineHookResponse` carries the commands returned by an inline hook handler.

### Governance and Administration

`AdminRole` is a standard or custom administrator role. `AdminRoleTarget` scopes a role to specific groups, apps, or app instances. `TrustedOrigin` whitelists CORS and redirect origins for embedded widget use.

### Branding and Customization

`Brand` holds the visual identity for an Okta org or custom domain. `Theme` carries logo, favicon, color, and background-image settings per brand. `Domain` is a verified custom DNS domain. `EmailTemplate` and `EmailTemplateContent` allow per-locale overrides of system-generated emails.

### Schemas and Devices

`UserSchema` and `GroupSchema` define base and custom attribute definitions with type, mutability, and scope. `Device` represents an endpoint registered in Okta Device Access.

### System Logs and Auditing

`SystemLog` is a single immutable audit event from the Okta System Log, carrying actor, target, outcome, and debug context. Used for SIEM integration, compliance, and incident investigation.

### Rate Limits

`RateLimitAdmin` is a conceptual type surfacing per-endpoint rate-limit metadata (limit, remaining, reset). Actual rate-limit headers are returned on every REST response; this type would allow querying configured thresholds.

## Query Patterns

```graphql
# Fetch a user with their group memberships and enrolled factors
query UserDetail($userId: ID!) {
  user(id: $userId) {
    id
    status
    profile { login email firstName lastName }
    type { id name }
    groups { id profile { name } }
    factors { id factorType provider status }
    sessions { id login expiresAt }
  }
}

# List applications with their group assignments
query AppGroups($appId: ID!) {
  application(id: $appId) {
    id
    label
    status
    groups { id priority profile { name } }
    credentials { signing { kid } }
  }
}

# Query system log events
query AuditLog($filter: SystemLogFilter!) {
  systemLogs(filter: $filter) {
    edges {
      node {
        uuid
        eventType
        severity
        published
        actor { id type displayName }
        target { id type displayName }
        outcome { result reason }
      }
    }
    pageInfo { hasNextPage endCursor }
  }
}
```

## Mutation Patterns

```graphql
# Create and activate a user
mutation ProvisionUser($input: CreateUserInput!) {
  createUser(input: $input) {
    user { id status profile { login email } }
  }
}

# Assign a group to an application
mutation AssignGroup($appId: ID!, $groupId: ID!, $priority: Int) {
  assignGroupToApp(appId: $appId, groupId: $groupId, priority: $priority) {
    appGroup { id priority lastUpdated }
  }
}

# Activate an inline hook
mutation ActivateHook($hookId: ID!) {
  activateInlineHook(id: $hookId) {
    inlineHook { id name status }
  }
}
```

## Notes

- Okta enforces rate limits per org and SKU. A GraphQL layer would need to respect per-field resolver budgets and apply query depth / complexity limits.
- Authentication uses scoped OAuth 2.0 tokens (`okta.users.manage`, `okta.groups.manage`, etc.) or legacy API keys. A GraphQL gateway would reuse the same token model.
- Pagination follows cursor-based patterns aligned with Okta's REST `after` / `limit` link-header pagination.
- Custom attributes in `UserProfile` and `GroupProfile` use dynamic keys (namespace-prefixed) that would require a `JSON` scalar or a generated union in a real schema.
