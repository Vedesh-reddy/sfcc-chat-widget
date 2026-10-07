# Architecture and data model

[← README](../README.md) · [Code reference](CODE-REFERENCE.md)

## Request flow

```text
Layout overlays (page, checkout, pdStorePage, pdComponentPage)
  └─ components/chatwidget/widget.isml   route URLs in data-urls, launcher, dialog panel
       └─ chatWidget.js
            ├─ GET  ChatWidget-Capabilities … ChatWidget-Confirmation   (reads)
            ├─ GET  ChatWidget-AuthToken  → fresh CSRF token before every POST
            ├─ POST ChatWidget-Activity / SetDefaultAddress / Logout / PasswordReset / Review
            └─ POST/GET SFRA JSON routes (Cart-*, Account-*, Address-*, PaymentInstruments-*,
                        CheckoutShippingServices-SubmitShipping, CheckoutServices-*)
                  every request carries header X-Chat-Widget: 1
```

Overlay controllers extend the SFRA ones with `server.prepend` (or `append` for `Order-Confirm`). For widget requests they subscribe to `route:Complete` and `route:Redirect` and record the final outcome after every handler ran. Requests without the header are not touched.

## Data model

Both types are site-scoped with `no-staging`.

`ChatWidgetJourney`, key = UUID kept in `session.custom.chatWidgetJourneyKey`. It survives sign-in, so the guest-to-customer path stays in one journey.

| Attribute | Notes |
| --- | --- |
| `siteID`, `locale` | Set on creation |
| `customerNo` | Set once the shopper is signed in |
| `status` | `active` → `ordered` → `reviewed`; `closed` on sign-out |
| `startedAt`, `lastActivityAt` | `lastActivityAt` drives journey retention |
| `orderNo` | Linked at place-order or the confirmation page |
| `reviewRating`, `reviewComment`, `reviewCommentConsent`, `reviewSubmittedAt` | One review per journey; the comment only with consent |

`ChatWidgetActivity`, key = UUID, append-only: `journeyKey`, `action` (≤100), `result` (≤20), `entityID` (≤256), `context` (≤1000), `customerNo`, `occurredAt`. Values are flattened to one line and truncated before storage.

## Privacy rules

- Nothing is written without `session.trackingAllowed`, except an explicitly submitted review.
- Never stored: passwords, card numbers, security codes, CSRF tokens, addresses, email addresses, address nicknames, search phrases (only the result count).
- Client events are allow-listed: `widget_opened`, `widget_closed`, `checkout_step_entered` with step `payment` or `review`.
- Every write runs in `Transaction.wrap` inside `try/catch`; failures are logged and never block shopping.

## Trust boundaries

- Account, address, card, order, checkout and review routes require an authenticated, registered customer (`401` otherwise).
- Orders and reviews require the order's `customerNo` to match the signed-in profile. Confirmation requires the order's customer to be the session customer, like `Order-Confirm`.
- Reviews: rating 1–5, comment ≤1000 characters, consent required for a comment, one review per order (`409`).
- Password reset answers identically whether or not the account exists.
- All POSTs use `csrf.validateAjaxRequest`. Writes SFRA already exposes go through SFRA's own routes and validation.
- Payloads are display-safe projections: masked card numbers only, no raw API objects.

## Checkout

`ChatWidget-Checkout` mirrors `Checkout-Begin` preparation for a registered shopper (empty shipments, customer email, currency, recalculation) and returns saved addresses, the selected shipping method, and payment support:

| Condition | Result |
| --- | --- |
| No active `CREDIT_CARD` method | Unavailable, explained |
| Processor not in `IN_WIDGET_CARD_PROCESSORS` or without its hook | Hand over to secure checkout at the payment stage |
| No usable, unexpired, applicable saved card | Hand over |
| Otherwise | Saved-card radio list and security code field |

The widget then calls `SubmitShipping`, `SubmitPayment` (security code sent only here) and `PlaceOrder`, and shows the confirmation with the review form.

## Retention

`purgeJourneyData.js` deletes in batches of 200 per transaction, always closing iterators: activities older than `ActivityRetentionDays`, journeys inactive longer than `JourneyRetentionDays`, and, when `CustomerNo` is set, that customer's journeys, every activity in those journeys (including guest activity before sign-in), and activities tagged with the number.

## Logging

`Logger.getLogger('chat-widget', …)` with categories `journey-tracking` and `journey-retention`.
