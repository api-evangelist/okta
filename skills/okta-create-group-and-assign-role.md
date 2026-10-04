---
name: okta-create-group-and-assign-role
description: Create a new group, assign a role to it, and add an application target to that role.
api: openapi/okta-group-api-openapi.yml
operations:
- createGroup
- assignRoleToGroup
- addApplicationTargetToAdminRoleGivenToGroup
generated: '2026-10-03'
method: generated
generator: extract-docs-artifacts.py skills (local)
source: openapi/okta-group-api-openapi.yml ; every operationId checked against the contract
---

# okta-create-group-and-assign-role

Create a new group, assign a role to it, and add an application target to that role.

## Steps

1. 1. Use `createGroup` with the required request body fields for the new group and the `Authorization` header.
2. 2. Use `assignRoleToGroup` with the `groupId` path parameter, the role definition in the request body, and the `Authorization` header.
3. 3. Use `addApplicationTargetToAdminRoleGivenToGroup` with `groupId`, `roleId`, `appName`, and the `Authorization` header.

## Rules

- Authentication: Provide an API token in the `Authorization` header (scheme: `api_token`).
- All mutating operations (`createGroup`, `assignRoleToGroup`, `addApplicationTargetToAdminRoleGivenToGroup`) are idempotent only when the same request payload is sent repeatedly.
- Errors: The API returns standard HTTP error codes (e.g., 400 for bad request, 401 for unauthorized, 404 for not found, 409 for conflict).
