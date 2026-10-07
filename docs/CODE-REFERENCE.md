# Code reference

[← README](../README.md) · [Architecture](ARCHITECTURE.md)

Paths are relative to `cartridges/plugin_chatwidget`.

## ChatWidget controller

`cartridge/controllers/ChatWidget.js`. All responses are JSON. Failures use `{ error: true, message }` with an HTTP status.

| Route | Method, middleware | Auth | Behavior |
| --- | --- | --- | --- |
| `Capabilities` | GET | – | Start-menu actions, first name, cart count, reviewable order |
| `AuthToken` | GET, https, csrf token | – | `{ tokenName, token }` |
| `Activity` | POST, csrf | – | Allow-listed client events; `400` otherwise |
| `Categories` | GET | – | Online subcategories of `parent` (root when empty) |
| `Products` | GET | – | 8 products per page for `cgid` or `q` (≤100 chars), `start` offset, `hasMore`, `total` |
| `Product` | GET | – | Variation attributes and `readyToOrder` for `pid` |
| `Cart` | GET | – | Recalculated basket: lines, totals, shipping methods, promotions, approaching discounts |
| `Promotions` | GET | – | Applied adjustments, coupon states, up to 20 active promotions |
| `PasswordReset` | POST, https, csrf | – | Sends the SFRA reset email if the account exists; same response either way |
| `Logout` | POST, csrf | – | Closes the journey, signs out |
| `Account` | GET | yes | Profile and counts (addresses, cards, orders, orders in 180 days) |
| `Addresses` | GET | yes | Saved addresses (default first) and country/state options |
| `SetDefaultAddress` | POST, csrf | yes | Sets an own address as preferred; `404` if unknown |
| `Payments` | GET | yes | Masked saved cards with expiry |
| `Orders` | GET | yes | Last 10 orders, excluding replaced |
| `Order` | GET | yes | Details of an own order; `404` otherwise |
| `Checkout` | GET, https | yes | Checkout preparation; `409` for empty or invalid carts |
| `Confirmation` | GET, https | session customer | Order state, `canReview`, `reviewed` |
| `Review` | POST, csrf | yes | Validates and stores the review |

## Route observers

| Controller | Routes | Action recorded |
| --- | --- | --- |
| `Cart.js` | AddProduct, UpdateQuantity, RemoveProductLineItem, AddCoupon, RemoveCouponLineItem, SelectShippingMethod | `add_to_cart`, `cart_quantity_updated`, `cart_item_removed`, `coupon_added`, `coupon_removed`, `shipping_method_selected` |
| `Account.js` | Login, SubmitRegistration, SaveProfile | `login`, `register`, `profile_updated` |
| `Address.js` | SaveAddress, DeleteAddress | `address_saved`, `address_deleted` |
| `PaymentInstruments.js` | DeletePayment | `saved_payment_deleted` |
| `CheckoutShippingServices.js` | SubmitShipping | `delivery_address_chosen` |
| `CheckoutServices.js` | SubmitPayment, PlaceOrder | `saved_payment_selected`, `order_placed` (links the order) |
| `Product.js` | Variation | `variant_selected` |
| `Order.js` | Confirm (append) | `order_confirmation_observed` (links the order) |
| `Login.js` | Logout | `logout` and closes the journey |

## Journey helper

`cartridge/scripts/helpers/journey.js`

| Export | Behavior |
| --- | --- |
| `track(req, action, details)` | Appends an activity and touches the journey; skipped without tracking consent |
| `observe(action, describe, onSuccess)` | Prepend middleware that records route outcomes for widget requests |
| `recordOrder(req, orderNo)` | Links an order and sets status `ordered` unless already reviewed |
| `pendingReviewOrder(req)` | Order of this journey still awaiting a review |
| `hasReview(orderNo)` | Whether any journey holds a review for the order |
| `saveReview(req, review)` | Stores the review (new journey if this one already has one) and clears the session key |
| `endJourney(req)` | Sets `closed` unless reviewed and clears the session key |
| `journeyKey(req, create)`, `isWidgetRequest(req)` | Session key and header check |

## Data helpers

`cartridge/scripts/helpers/chatWidgetHelpers.js`: `capabilities`, `categoriesFor`, `cartData`, `promotionsData`, `addressesFor`, `addressOptions`, `paymentsFor`, `paymentSupport`, `recentOrderCount`, `orderSummary`, `orderDetails`, `formatMoney`, `formatProductPrice`. Each returns plain data built from SFRA models. `IN_WIDGET_CARD_PROCESSORS` lists processors that can pay inside the widget.

## Retention job

`cartridge/scripts/jobs/purgeJourneyData.js`, step type `custom.ChatWidget.PurgeJourneyData` in `steptypes.json`. `execute(parameters)` returns `ERROR` for non-positive retention days or a failure, otherwise `OK` with `Removed N objects.`

## Client module

`cartridge/client/default/js/chatWidget.js` builds every screen with DOM APIs (no HTML strings). Screens: menu, categories, products (lazy carousel), search, variations, cart, promotions, sign-in, registration, password reset, account, profile, addresses, address form, saved cards, orders, order, checkout delivery, shipping, payment, review, confirmation and review form. Navigation keeps a Back stack and a Menu button; status messages use an `aria-live` region; headings receive focus; Escape closes the panel. It opens once per session automatically, and on the order confirmation page with the review form.

## Templates and styles

| File | Purpose |
| --- | --- |
| `components/chatwidget/widget.isml` | Route URLs, launcher and dialog panel |
| `common/layout/{page,checkout,pdStorePage,pdComponentPage}.isml` | SFRA layouts plus the widget, its CSS and script |
| `client/default/scss/chatWidget.scss` | Widget styles |

## Tooling

| File | Purpose |
| --- | --- |
| `scripts/build.js` | Webpack JS build and Sass CSS build |
| `scripts/check-templates.js` | isml-linter over all five templates |
| `scripts/check-docs.js` | Verifies local Markdown links and images |
| `scripts/package-metadata.js` | `dist/chat-widget-metadata.zip` |
