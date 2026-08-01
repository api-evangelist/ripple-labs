---
name: Approve a flexible RLUSD redemption
description: Choose the destination type for a pending RLUSD redemption transaction.
api: openapi/ripple-labs-rlusd-openapi-original.yml
operations: [getAccessToken, getTransaction, updateTransactionDestination]
---

# Approve a flexible RLUSD redemption

Use this skill to approve a pending RLUSD redemption by selecting its destination type.

## Auth
1. `getAccessToken` with `client_credentials` (see the list-transactions skill). Requires the
   `rlusd_customers:write` scope. Use the JWT as `Authorization: Bearer <token>`.

## Steps
1. `getTransaction` — `GET /v1/stablecoin/transactions/{id}` and confirm `status` is `PENDING`.
   Only `PENDING` transactions can be approved.
2. `updateTransactionDestination` — `PUT /v1/stablecoin/transactions/{id}/destination` with body
   `{ "type": "FIAT" }` or `{ "type": "CRYPTO" }`. For `CRYPTO`, include `walletId`; do NOT send
   `walletId` for `FIAT` (either mistake returns `400 INVALID_REQUEST_BODY`).

## Idempotency
Re-submitting the **same** `type` returns `200 OK` (idempotent). Submitting a **different** type
after one is chosen returns `409 TRANSACTION_DESTINATION_TYPE_IMMUTABLE`. Approving a transaction
that is not `PENDING` returns `409 TRANSACTION_INVALID_STATUS_TRANSITION`. See
`conventions/ripple-labs-conventions.yml` and `errors/ripple-labs-problem-types.yml`.

## Note
The approve body field was renamed `destination` -> `type` in API v1.4 — send `type`.

## Testing
In UAT (`https://api.test.ripple.com`), create sample transactions with the UAT-only
`POST /v1/stablecoin/transactions`; they auto-advance PROCESSING -> COMPLETED in ~2s.
