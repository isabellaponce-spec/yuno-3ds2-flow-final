# Yuno 3DS Docs — Gap Analysis for Zuora s2s Flow (Scenario B)

**Date:** 15 May 2026
**Author:** Isabella Ponce de León
**Companion to:** `index.html` (3DS2 Server API — Final Flow for Zuora, Option 1)

---

## The one thing missing in the docs

For gateways that **do not return a redirect URL** (Scenario B — CyberSource / Cardinal Commerce / BlueSnap / PayPalPayflow), Yuno needs a way to **receive the 3DS fingerprint from the merchant** (Zuora). That is the only addition the public docs need.

Everything else — Direct workflow, response `three_d_secure` object, status flow, webhooks — already exists.

---

## What that means in the request

When the merchant collects the fingerprint on their own HPM (because the underlying gateway has no redirect URL for Yuno to forward), Yuno's `POST /v1/payments` needs to accept the fingerprint inline. Field shape owned by Andre's spike — the only requirement at the docs level is to document where it lives in the request.

Reference example: the Adyen-3ds2.md file Guoqing shared in [Drive › Gateway Details › 3ds2 flow example](https://drive.google.com/drive/folders/1f_soPLGeWkdPNgLQ4YAkNBWmv4kF7bAE) shows Zuora sends the fingerprint result as a base64-encoded payload (`details.threeds2.fingerprint`). Yuno's equivalent location on the V1 Payments request is the piece to define and document.

---

## Pages to touch

| Page | Change |
|---|---|
| [security-and-compliance/3d-secure](https://docs.y.uno/docs/security-and-compliance/3d-secure) → Direct Integration section | Add the request-side field where Yuno receives the 3DS fingerprint for Scenario B. Field shape per Andre's spike. |
| [direct-integration-use-cases/3ds-configuration-and-testing](https://docs.y.uno/docs/direct-integration-use-cases/3ds-configuration-and-testing) | Add a test scenario covering the fingerprint inbound case. |

Nothing else needs to change in the public docs.

---

## What's NOT changing

- Response `payment_method.detail.card.card_data.three_d_secure` object — stays as-is
- `redirect_url` location in `payment.payment_method.payment_method_detail.card` — stays as-is
- Status flow `CREATED → PENDING → IN_PROCESS → SUCCEEDED / DECLINED / ERROR` — stays as-is
- Webhook + `GET /v1/payments/{id}` mechanism — stays as-is
- 3DS Standalone activation via `3DS_ONLY` metadata — stays as-is
- Dashboard configuration page — stays as-is

---

## Outstanding before Tuesday 19 May

| Item | Owner | Deadline |
|---|---|---|
| Field name + location for the inbound 3DS fingerprint on `POST /v1/payments` | Andre (Integrations) | Mon 18 May EOD |
| Gateway-details markdowns from Guoqing for the s2s gateways without redirect (CyberSource, BlueSnap, PayPalPayflow) — confirm fingerprint shape per gateway | Guoqing (Zuora) | Pre-Tuesday |
