# Yuno 3DS Docs — One-thing-missing Note

**Date:** 15 May 2026
**Companion to:** `index.html`

## The single addition

When the merchant uses their own checkout (Zuora HPM, a Zuora iframe, or the merchant's own front-end), Yuno cannot collect the device fingerprint directly. The public docs need to expose a new inbound field on `POST /v1/payments` where the merchant passes the fingerprint they collected. Field name and exact location owned by the Core team's spike.

## URL that changes

| URL | Change |
|---|---|
| [security-and-compliance/3d-secure](https://docs.y.uno/docs/security-and-compliance/3d-secure) → Direct Integration section | **Required.** Add the new inbound field with example. |
| [direct-integration-use-cases/3ds-configuration-and-testing](https://docs.y.uno/docs/direct-integration-use-cases/3ds-configuration-and-testing) | Post-ship. Add test scenario + full JSON example. |
| [basic-concepts/3ds-1](https://docs.y.uno/docs/basic-concepts/3ds-1) | No change needed. |

## What stays the same

- Response object `payment_method.detail.card.card_data.three_d_secure`
- `redirect_url` location in `payment.payment_method.payment_method_detail.card`
- Status flow `CREATED → PENDING → IN_PROCESS → SUCCEEDED / DECLINED / ERROR`
- Webhook + `GET /v1/payments/{id}`

## Open items before Tuesday 19 May

| Item | Owner | Deadline |
|---|---|---|
| Field name + exact location for the inbound device fingerprint on `POST /v1/payments` | Core team | Mon 18 May EOD |
| Gateway-details markdowns from Zuora for the s2s gateways without redirect (CyberSource, BlueSnap, PayPalPayflow) | Guoqing (Zuora) | Pre-Tuesday |
