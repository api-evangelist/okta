# Programmatic API Onboarding — Okta

A single-file, zero-dependency Node.js (18+) CLI that reproduces SoundCloud's
`sc-api-auth.mjs` pattern for Okta: register an application / obtain credentials
programmatically instead of clicking through a dashboard, so agents and developers
can onboard at the command line.

- Script: [`okta-api-auth.mjs`](okta-api-auth.mjs)
- Run `node okta-api-auth.mjs --help` for usage and the required environment variables.
- Story / rationale: https://apievangelist.com/2026/07/24/okta-has-the-endpoint-hides-the-door/

Part of the API Evangelist "Programmatic API Onboarding for the Agentic Moment" series.
