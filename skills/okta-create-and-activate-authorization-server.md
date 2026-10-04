---
name: okta-create-and-activate-authorization-server
description: Create a new authorization server and activate it.
api: openapi/okta-authorizationserver-api-openapi.yml
operations:
- createAuthorizationServer
- activateAuthorizationServer
generated: '2026-10-03'
method: generated
generator: extract-docs-artifacts.py skills (local)
source: openapi/okta-authorizationserver-api-openapi.yml ; every operationId checked against the contract
---

# okta-create-and-activate-authorization-server

Create a new authorization server and activate it.

## Steps

1. 1. Use `createAuthorizationServer` with the request body fields required to define the server (e.g., name, description, audiences). Include the `Authorization` header with an API token or OAuth2 token.
2. 2. Use `activateAuthorizationServer` with path parameter `authServerId` returned from the previous step. Include the `Authorization` header.

## Rules

- Authentication: Provide an `Authorization` header using either the `api_token` (API key) or `oauth2` scheme.
- Idempotency: Not applicable; the operations are not idempotent.
- Pagination: Not applicable for these operations.
