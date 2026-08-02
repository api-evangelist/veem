---
generated: '2026-07-21'
method: generated
name: Send money with a claimless payment
description: Quote, send, and track a direct (claimless) payment to a payee's bank account.
api: openapi/veem-api-openapi.yml
operations: [getQuoteEnhancedUsingPOST, createClaimLessPaymentUsingPOST, getPaymentUsingGET_2, approvePaymentUsingPOST_2]
source: operationIds verified in openapi/veem-api-openapi.yml
---

# Send money with a claimless payment

Send funds straight to a payee's bank account — no claim step for the receiver.

## Auth
- OAuth2 bearer token via client_credentials (`POST /oauth/token`, `grant_type=client_credentials&scope=all`, Basic `client_id:client_secret`). See `authentication/veem-authentication.yml`.
- Sandbox base URL `https://sandbox-api.veem.com`; production `https://api.veem.com`.

## Idempotency
- Send a unique `X-Request-Id` (UUID) header on every request; reusing one returns `409 Conflict`. See `conventions/veem-conventions.yml`.

## Steps
1. **Quote it** — `getQuoteEnhancedUsingPOST` (`POST /veem/v1.2/exchangerates/quote/enhanced`) with payer/payee currency and one of `fromAmount` or `toAmount` (never both — error `SPECIFIED_BOTH_FROM_AND_TO_AMOUNT`, 50001402). Fixed fee is included in the enhanced quote.
2. **Create the payment** — `createClaimLessPaymentUsingPOST` (`POST /veem/v1.2/claimless-payments`) with payee bank details, amount, and `purposeOfPayment` (missing → `MISSING_PURPOSE_OF_PAYMENT`, 50001404).
3. **Approve if required** — `approvePaymentUsingPOST_2` (`POST /veem/v1.2/payments/{paymentId}/approve`) when the account uses payment-approval tiers.
4. **Track status** — `getPaymentUsingGET_2` (`GET /veem/v1.2/payments/{paymentId}`); statuses run Drafted → Sent → PendingAuth → Authorized → InProgress → Complete (see `asyncapi/veem-webhooks.yml`). Prefer registering an `OUTBOUND_PAYMENT_STATUS_UPDATED` webhook over polling.

## Errors
- Numeric Veem error codes in the `ErrorResponse` envelope; the payment-specific registry is in `errors/veem-error-codes.yml` (e.g. `INVALID_AMOUNT` 50001401, `UNSUPPORTED_CURRENCY_FOR_PAYMENT` 50001011).
- In sandbox, use Test Mode to drive the payment through statuses (`sandbox/veem-sandbox.yml`).
