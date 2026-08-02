---
generated: '2026-07-21'
method: generated
name: Configure and verify webhooks
description: Register webhook callbacks for payment, invoice, and account events and verify their HMAC signatures.
api: openapi/veem-api-openapi.yml
operations: [createWebhookUsingPOST_2, getWebhooksUsingGET_2, updateWebhookUsingPATCH_2, deleteWebhookUsingDELETE_2]
source: operationIds verified in openapi/veem-api-openapi.yml
---

# Configure and verify webhooks

Push notifications for payment / invoice / account / VBA / virtual-card events.

## Auth
- OAuth2 bearer belonging to the owner account; headers `Authorization`, unique `X-Request-Id` (UUID), `Content-Type: application/json` are all required.

## Steps
1. **Register** — `createWebhookUsingPOST_2` (`POST /veem/v1.2/webhooks`) with `{event, callbackURL}`; events include `OUTBOUND_PAYMENT_STATUS_UPDATED`, `INBOUND_PAYMENT_STATUS_UPDATED`, `OUTBOUND_INVOICE_STATUS_UPDATED`, `INBOUND_INVOICE_STATUS_UPDATED`, `ACCOUNT_STATUS_UPDATED` (full catalog in `asyncapi/veem-webhooks.yml`).
2. **Audit** — `getWebhooksUsingGET_2` (`GET /veem/v1.2/webhooks`) lists active configurations.
3. **Rotate a URL** — `updateWebhookUsingPATCH_2` (`PATCH /veem/v1.2/webhooks/{webhookId}`).
4. **Remove** — `deleteWebhookUsingDELETE_2` (`DELETE /veem/v1.2/webhooks/{webhookId}`) sets the configuration Inactive.

## Verifying deliveries
- Each delivery carries an `ACCESS-SIGNATURE` header: HMAC-SHA256 of the payload keyed with your `clientId`. Recompute and compare before trusting the event.
- The `data` field arrives as stringified JSON — parse it separately.
- Deliveries retry up to 3 times at 5-second intervals; make your handler idempotent (payloads include `originalRequestId`).
