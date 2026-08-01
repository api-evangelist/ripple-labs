---
name: List and inspect RLUSD stablecoin transactions
description: Authenticate to the Ripple Mint API and read stablecoin transaction activity.
api: openapi/ripple-labs-rlusd-openapi-original.yml
operations: [getAccessToken, listTransactions, getTransaction]
---

# List and inspect RLUSD stablecoin transactions

Use this skill to read RLUSD (Ripple Mint) transaction data with the Ripple Mint API.

## Auth
1. Call `getAccessToken` — `POST https://api.ripple.com/v2/oauth/token` with
   `grant_type=client_credentials`, your `client_id`/`client_secret`, and
   `audience=urn:ripplexcurrent-prod:<tenant_id>`. You need the `rlusd_customers:read` scope.
2. Use the returned JWT as `Authorization: Bearer <access_token>`. Tokens expire after 1 hour
   (`expires_in: 3600`); request a new one before expiry — no refresh token is issued.

## Steps
1. `listTransactions` — `GET /v1/stablecoin/transactions`. Filter with the `statuses` query
   parameter (valid values include `PENDING`, `PROCESSING`, `COMPLETED`, `CANCELED`, `FAILED`).
2. For a single record, `getTransaction` — `GET /v1/stablecoin/transactions/{id}`. It returns the
   full `Transaction` object with embedded `source` and `destination` endpoints.
3. Always trust `updatedAt` to determine the latest state — webhook and list ordering are not guaranteed.

## Errors
Errors are RFC 9457 problem+json with a `code` field. `TRANSACTION_NOT_FOUND` (404) means the id
does not exist. See `errors/ripple-labs-problem-types.yml`.

## Testing
Run everything against the UAT sandbox first: host `https://api.test.ripple.com`, audience suffix
`-uat`. See `sandbox/ripple-labs-sandbox.yml`.
