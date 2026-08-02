---
generated: '2026-07-21'
method: generated
name: Onboard a customer account
description: Create a Veem account for a business, link its bank account, and file beneficial-ownership information.
api: openapi/veem-api-openapi.yml
operations: [createAccountUsingPOST, getAccountUsingGET, addBankAccountUsingPOST, editBankAccountUsingPATCH, createBoiUsingPOST]
source: operationIds verified in openapi/veem-api-openapi.yml
---

# Onboard a customer account

Partner flow: create the customer's Veem account, attach funding, satisfy compliance.

## Auth
- OAuth2 bearer with your partner credentials (`scope=all`); unique `X-Request-Id` UUID per request (reuse → 409). See `conventions/veem-conventions.yml`.

## Steps
1. **Create the account** — `createAccountUsingPOST` (`POST /veem/v1.2/account`) with ISO country code, account type, email, and business details (validation codes 50001201–50001219 in `errors/veem-error-codes.yml`).
2. **Check status** — `getAccountUsingGET` (`GET /veem/v1.2/account/{accountId}`); an `ACCOUNT_UNDER_REVIEW` state means risk review is pending. Register the `ACCOUNT_STATUS_UPDATED` webhook to learn when `paymentReady`/`verified` flip (`asyncapi/veem-webhooks.yml`).
3. **Link a bank account** — `addBankAccountUsingPOST` (`POST /veem/v1.2/account/bank-account`); fix verification failures (VERIFICATION_* codes) with `editBankAccountUsingPATCH` (`PATCH /veem/v1.2/account/bank-account/{bankAccountId}`) or by uploading documents.
4. **File beneficial ownership** — `createBoiUsingPOST` (`POST /veem/v1.2/beneficial-ownership`) for the new account, or embed the BOI Web SDK plugin (`components/veem-components.yml`).

## Sandbox
- Sandbox accounts are pre-KYC-verified and bank details are auto-added — see `sandbox/veem-sandbox.yml`.
