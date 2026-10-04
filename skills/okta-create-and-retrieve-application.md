---
name: okta-create-and-retrieve-application
description: Create a new Okta application and then retrieve its details.
api: openapi/okta-application-api-openapi.yml
operations:
- createApplication
- getApplication
generated: '2026-10-03'
method: generated
generator: extract-docs-artifacts.py skills (local)
source: openapi/okta-application-api-openapi.yml ; every operationId checked against the contract
---

# okta-create-and-retrieve-application

Create a new Okta application and then retrieve its details.

## Steps

1. 1. Call `createApplication` with the required application payload in the request body and include the `Authorization` header (api_token or oauth2).
2. 2. Call `getApplication` using the `appId` returned from the previous step and include the `Authorization` header.

## Rules

- Authentication: Provide an `Authorization` header using either the `api_token` (API key) or `oauth2` scheme.
- Idempotency: The `createApplication` operation is not idempotent; repeat calls will create duplicate applications.
