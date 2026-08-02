---
generated: '2026-07-21'
method: generated
name: Request money with an invoice
description: Create, track, approve, or cancel an invoice to collect a payment.
api: openapi/veem-api-openapi.yml
operations: [createInvoiceUsingPOST_2, getInvoiceUsingGET_2, approveInvoiceUsingPOST_2, cancelInvoiceUsingPOST_2]
source: operationIds verified in openapi/veem-api-openapi.yml
---

# Request money with an invoice

Bill a customer: the invoice is sent to the payer, who claims/pays it through Veem.

## Auth
- OAuth2 bearer (`scope=all`), unique `X-Request-Id` UUID per request. See `authentication/veem-authentication.yml` and `conventions/veem-conventions.yml`.

## Steps
1. **Create the invoice** — `createInvoiceUsingPOST_2` (`POST /veem/v1.2/invoices`) with payer contact, amount `{number, currency}`, and due date; the invoice is sent to the payer.
2. **Track it** — `getInvoiceUsingGET_2` (`GET /veem/v1.2/invoices/{invoiceId}`); statuses: Sent → Claimed → MarkAsPaid (or Cancelled / Updated / Closed). Register `OUTBOUND_INVOICE_STATUS_UPDATED` for pushes (`asyncapi/veem-webhooks.yml`).
3. **Approve an inbound invoice** — when you are the payer, `approveInvoiceUsingPOST_2` (`POST /veem/v1.2/invoices/{invoiceId}/approve`) accepts it and kicks off the linked payment.
4. **Cancel if needed** — `cancelInvoiceUsingPOST_2` (`POST /veem/v1.2/invoices/{invoiceId}/cancel`) while un-paid.

## Errors
- `ErrorResponse` envelope with numeric codes — see `errors/veem-error-codes.yml`; account-side validation codes (e.g. `INVALID_EMAIL_ADDRESS` 50001202) apply to the payer contact.
