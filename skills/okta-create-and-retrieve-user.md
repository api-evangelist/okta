---
name: okta-create-and-retrieve-user
description: Create a new Okta user and then retrieve the created user's details.
api: openapi/okta-user-api-openapi.yml
operations:
- createUser
- getUser
generated: '2026-10-03'
method: generated
generator: extract-docs-artifacts.py skills (local)
source: openapi/okta-user-api-openapi.yml ; every operationId checked against the contract
---

# okta-create-and-retrieve-user

Create a new Okta user and then retrieve the created user's details.

## Steps

1. 1. `createUser` – send a POST to `/api/v1/users` with the required user profile fields in the request body.
2. 2. `getUser` – send a GET to `/api/v1/users/{userId}` using the `id` returned from the createUser response.

## Rules

- Auth: Include an `Authorization` header with an API token (scheme `api_token`).
- Idempotency: The `createUser` operation is not idempotent; repeat calls will create duplicate users.
- Pagination: Not applicable for these operations.
