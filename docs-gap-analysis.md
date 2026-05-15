# Yuno 3DS Docs — Gap Analysis for Zuora s2s Flow (Scenario B)

**Date:** 15 May 2026
**Author:** Isabella Ponce de León
**Companion to:** `index.html` (3DS2 Server API — Final Flow for Zuora, Option 1)

---

## What Yuno already documents today

### [docs.y.uno/docs/basic-concepts/3ds-1](https://docs.y.uno/docs/basic-concepts/3ds-1)

**Covered:**
- 3DS2 support, Frictionless + Challenge flows
- **3DS Standalone** — decouples auth from authorization, activated via metadata `{ "key": "3DS_ONLY", "value": "1" }`, ECI/CAVV delivered via webhook
- SDK + native mobile integration mentioned

**Missing:**
- API specs
- Field-level documentation

---

### [docs.y.uno/docs/security-and-compliance/3d-secure](https://docs.y.uno/docs/security-and-compliance/3d-secure)

**Covered:**
- Dashboard configuration: Acquirer BIN, Merchant ID, MCC, Merchant Name, Merchant URL, Country Code
- Three integration methods: Checkout (SDK), External (PCI-compliant submits own ECI/cryptogram), **Direct** (PCI-compliant, no SDK)
- Direct workflow: single call → `PENDING / WAITING_ADDITIONAL_STEP`, `sdk_action_required: true`, `redirect_url` in `payment.payment_method.payment_method_detail.card`
- Response `three_d_secure` object fields: `version`, `electronic_commerce_indicator`, `cryptogram`, `transaction_id`, `directory_server_transaction_id`, `pares_status`, `acs_id`
- Status flow: `CREATED → PENDING → IN_PROCESS → SUCCEEDED / DECLINED / ERROR`
- Webhook + `GET /v1/payments/{id}` for status retrieval

**Missing:**
- Request-side `three_d_secure` object schema
- `browser_data` object specification
- `device_fingerprint` object specification
- `cardholder_info` object specification
- Frictionless vs Challenge response examples (full JSON)
- Callback URL handling details

---

### [docs.y.uno/docs/direct-integration-use-cases/3ds-configuration-and-testing](https://docs.y.uno/docs/direct-integration-use-cases/3ds-configuration-and-testing)

**Covered:**
- References to Create Customer + Create Payment endpoints
- Test cards (Visa, Mastercard, Amex, Diners, JCB)
- OTP codes for 3DS2 + 3DS1 scenarios

**Missing:**
- Full request JSON structures
- Full response field specifications
- Browser data object structure
- Device fingerprint object specification
- `three_d_secure` request object field definitions
- Challenge submission mechanism
- ECI / CAVV / dsTransID field mapping examples
- Direct integration flow diagrams

---

## What must be modified to support Scenario B (s2s without redirect URL)

### Priority 1 — Security & compliance / 3D Secure → Direct Integration section

Largest expansion. Today this section is one paragraph. It needs the full s2s request and response surface.

| Item | Detail | Owner |
|---|---|---|
| Request-side `three_d_secure` object schema | Full field-level spec | Andre (spike, deadline Mon 18 May EOD) |
| `browser_data` sub-object | `ip`, `user_agent`, `accept_header`, `screen_width`, `screen_height`, `color_depth`, `time_zone`, `language`, `java_enabled`, `javascript_enabled` | Andre |
| `device_fingerprint` sub-object | Conditional per gateway. Separate step for Adyen legacy + Orbital; inline for all others. Clarify which form Yuno exposes. | Andre |
| `cardholder_info` sub-object | `name`, `email`, `billing_address`, `phone` — required by Cardinal-Commerce-style providers | Andre |
| Authentication hints | `frictionless_preferred`, `challenge_mandate`, `low_value_exemption` if Yuno supports them — confirm with Andre | Andre |
| Full request JSON example | Frictionless + Challenge variants side by side | Andre + docs |
| Full response JSON example | Both cases with populated fields | Andre + docs |
| Scenario A vs Scenario B distinction | Today the doc assumes `redirect_url` always comes from the gateway. For s2s (Cardinal Commerce / CyberSource), Yuno drives the authentication via Netcetera and may return either an inline auth result (frictionless) or an ACS redirect (challenge). Clarify explicitly. | Andre + docs |

### Priority 2 — Direct integration use cases / 3DS configuration & testing

| Item | Detail |
|---|---|
| Full request JSON examples for Direct workflow | Replaces today's high-level references |
| Test scenarios for s2s flow | Frictionless and Challenge, distinct from SDK scenarios |
| Dedicated test cards for s2s | Today only SDK cards are listed |
| Expected response samples per test case | One per scenario |

### Priority 3 — Basic concepts / 3DS

| Item | Detail |
|---|---|
| Decision tree | When to use SDK vs External vs Direct vs Standalone |
| Direct + Standalone combination | Document whether they can be combined, or why not |

### Priority 4 — New section: Gateway compatibility matrix

Add a per-gateway table covering the parameter-mapping work Paloma mentioned.

| Column | Values |
|---|---|
| Gateway | Stripe, Adyen, CyberSource, Worldpay, Braintree, BlueSnap, Checkout, … |
| Returns redirect URL? | Yes / No |
| Requires separate `device_fingerprint` step? | Yes (Adyen legacy, Orbital) / No |
| Required `cardholder_info` fields | Subset of `name`, `email`, `billing_address`, `phone` |

This matrix is the public-facing version of the parameter-mapping exercise that Checkout / Cards Integration owns. It also doubles as the "list of providers" Guoqing offered to share but that Jairo and Bernabe agreed is no longer scope-blocking.

---

## What NOT to change

These remain as-is. Reused without schema modification.

- Response object `payment_method.detail.card.card_data.three_d_secure` with all its fields
- `redirect_url` location in `payment.payment_method.payment_method_detail.card`
- Status flow `CREATED → PENDING → IN_PROCESS → SUCCEEDED / DECLINED / ERROR`
- Webhook + `GET /v1/payments/{id}` mechanism
- 3DS Standalone activation via `3DS_ONLY` metadata
- Dashboard configuration page (Acquirer BIN, MCC, etc.)

---

## Open items before Tuesday 19 May (Zuora CTO / CPO meeting)

| Item | Owner | Deadline |
|---|---|---|
| Request-side `three_d_secure` shape | Andre (Integrations) | Mon 18 May EOD |
| Confirmation that webhook + `GET /v1/payments/{id}` suffices for Scenario B resume (vs a dedicated continue endpoint) | Andre (Integrations) | Mon 18 May EOD |
| Per-provider extras not yet supported by Yuno | Checkout / Cards Integration | Rolling, post-Tuesday |
| De-obfuscation commitment for the 7 affected PSPs | Zuora (escalated in daily) | Tracked separately |
