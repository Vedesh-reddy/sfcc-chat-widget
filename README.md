<div align="center">

# SFCC Chat Widget

### The whole store in one corner of the page.

A shopping and account assistant for SFRA: browse, search, manage the cart and account, check out with a saved card, and review the order without leaving the page.

[![Validate cartridge](https://github.com/Vedesh-reddy/sfcc-chat-widget/actions/workflows/ci.yml/badge.svg)](https://github.com/Vedesh-reddy/sfcc-chat-widget/actions/workflows/ci.yml)
![Platform: Salesforce B2C Commerce](https://img.shields.io/badge/Platform-Salesforce_B2C_Commerce-00A1E0)
![SFRA 8](https://img.shields.io/badge/Tested-SFRA_8-164194)
![42 storefront states](https://img.shields.io/badge/Documented_states-42-2e7d32)

[Get started](docs/INSTALLATION.md) · [Shopper guide](docs/SHOPPER-GUIDE.md) · [Merchant guide](docs/MERCHANT-GUIDE.md) · [Code reference](docs/CODE-REFERENCE.md) · [Architecture](docs/ARCHITECTURE.md) · [Phase PRs](docs/DEVELOPMENT-PHASES.md)

</div>

![Shopping assistant open on the storefront home page](docs/images/storefront-widget-open.png)

> Working sandbox screenshots. The implementation was verified on an SFRA 8 storefront with Bootstrap 5, site `RefArch_Practice`, including an order placed and reviewed entirely inside the widget.

## What it does

| For shoppers | For merchants | For developers |
| --- | --- | --- |
| Browse categories, search, choose variations | Journey trail per session in custom objects | A separate `plugin_chatwidget` overlay |
| Cart: quantities, removal, promo codes, shipping method | Order reviews (1–5 stars, consented comments) | Reuses SFRA JSON routes for every write SFRA supports |
| Sign in, register, reset password | Retention job with per-customer erasure | Route observers that change no storefront behavior |
| Account, addresses, saved cards, orders | Consent-aware: nothing stored without tracking consent | No card data, CVV, tokens, addresses or search phrases stored |
| Checkout with a saved address and card | Works on storefront, checkout and Page Designer pages | Lint, build, metadata ZIP and CI |

## Start in five steps

```sh
git clone https://github.com/Vedesh-reddy/sfcc-chat-widget.git
cd sfcc-chat-widget
npm ci
npm run validate
npm run package:metadata
```

1. **Build:** `npm run validate` runs JavaScript, SCSS, ISML and documentation checks and builds `chatWidget.js` and `chatWidget.css`.
2. **Deploy:** upload `cartridges/plugin_chatwidget`, including its generated `cartridge/static` assets, into your active code version.
3. **Activate:** place `plugin_chatwidget` before `app_storefront_base` in the site's cartridge path.
4. **Import:** set your site ID in `metadata/chat-widget/jobs.xml`, rebuild the ZIP, and import `dist/chat-widget-metadata.zip` through **Administration → Site Development → Site Import & Export**.
5. **Schedule:** run `ChatWidget-PurgeJourneyData` daily in **Administration → Operations → Jobs**.

```text
plugin_chatwidget:app_storefront_base:modules
```

## See it work

| Start menu | Product options | Cart |
| --- | --- | --- |
| ![Signed-in start menu](docs/images/signed-in-menu.png) | ![Variation chooser](docs/images/variation-ready.png) | ![Cart](docs/images/cart-quantity-updated.png) |

| Checkout payment | Review and place | Placed, with review |
| --- | --- | --- |
| ![Saved card and CVV](docs/images/checkout-payment.png) | ![Review and place order](docs/images/checkout-review.png) | ![Order placed](docs/images/order-confirmation.png) |

The [shopper guide](docs/SHOPPER-GUIDE.md) shows all 42 states: browsing, search with and without results, coupon errors, sign-in and registration errors, password reset, the address book, saved cards, orders, checkout, review consent, sign-out and mobile.

### In Business Manager

![Journey for order 00000102 with a 5-star review](docs/images/bm-journey-review.png)

The [merchant guide](docs/MERCHANT-GUIDE.md) covers the custom object types, journey and activity records, and the retention job.

## How it fits together

```text
Any page ─ widget.isml (layout overlays) ─ chatWidget.js
   │  every request: X-Chat-Widget: 1, POSTs with a fresh CSRF token
   ├─ ChatWidget-* JSON routes ──── reads, set default address, logout, password reset, reviews
   └─ SFRA JSON routes ─────────── cart, coupons, login, registration, profile, addresses,
                                    cards, SubmitShipping, SubmitPayment, PlaceOrder
                                        │ server.prepend observers (widget requests only)
                                        ▼
                     journey.js ─ ChatWidgetJourney + ChatWidgetActivity (consent-aware)
                                        ▲
              Job ChatWidget-PurgeJourneyData ─ retention and per-customer erasure
```

[Read the architecture](docs/ARCHITECTURE.md).

## Repository guide

| Path | Purpose |
| --- | --- |
| [`cartridges/plugin_chatwidget`](cartridges/plugin_chatwidget) | Deployable cartridge: controllers, helpers, job, templates, client code, styles |
| [`metadata/chat-widget`](metadata/chat-widget) | Custom object types and the retention job |
| [`scripts`](scripts) | Build, template check, documentation check, metadata packaging |
| [`docs`](docs) | Installation, shopper and merchant guides, architecture, code reference, troubleshooting |
| [`.github/workflows/ci.yml`](.github/workflows/ci.yml) | Validation and artifact packaging on Node 22 and 24 |

## Development commands

| Command | Result |
| --- | --- |
| `npm run build` | Compile `chatWidget.js` and `chatWidget.css` into `cartridge/static/default` |
| `npm run lint` | Check JavaScript, SCSS, ISML, and local documentation links |
| `npm run validate` | Run lint and build |
| `npm run package:metadata` | Produce the Business Manager import ZIP in `dist` |

The project is presented through five focused feature branches and PRs, merged in order. See [development phases](docs/DEVELOPMENT-PHASES.md).

## Scope and attribution

In-widget payment supports saved cards on synchronous processors (`BASIC_CREDIT`). Hosted-field, 3-D Secure and redirect processors, guest checkout and new cards hand over to the secure checkout page with the basket, address and shipping method already set. There is no free-text chat or AI; the assistant is a guided menu over live store data.

This is an independent extension, not an official Salesforce product. SFRA-derived layout overlays and terms are described in [NOTICE.md](NOTICE.md).
