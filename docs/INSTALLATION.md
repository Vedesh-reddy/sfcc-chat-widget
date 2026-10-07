# Installation

[← README](../README.md) · [Merchant guide](MERCHANT-GUIDE.md) · [Troubleshooting](TROUBLESHOOTING.md)

## Requirements

- An SFCC sandbox with an SFRA storefront and permission to upload code, import site data, edit the cartridge path, and run jobs.
- Node.js 22 or newer for local tooling.
- SFRA 8 was tested. `app_storefront_base` and `modules` must stay on the path: the plugin uses the SFRA `server` module, `csrf` middleware, product factory, cart and order models, checkout helpers, account helpers and the layouts it overlays.
- For in-widget payment: the `CREDIT_CARD` method on the `BASIC_CREDIT` processor and shoppers with saved cards. Other processors hand over to secure checkout.

## 1. Install and build

```sh
git clone https://github.com/Vedesh-reddy/sfcc-chat-widget.git
cd sfcc-chat-widget
npm ci
npm run validate
```

Output: `cartridges/plugin_chatwidget/cartridge/static/default/js/chatWidget.js` and `.../css/chatWidget.css`. Build output is ignored by Git; deploy it with the cartridge.

## 2. Deploy the cartridge

Upload `cartridges/plugin_chatwidget` into the active code version (WebDAV, `sgmf-scripts`, the b2c CLI, or an IDE). Copy `dw.example.json` to `dw.json` for tools that read it.

## 3. Configure the cartridge path

**Administration → Sites → Manage Sites → your site → Settings**

```text
plugin_chatwidget:app_storefront_base:modules
```

Keep the site's other cartridges. Another cartridge that also overrides `common/layout/page.isml`, `checkout.isml`, `pdStorePage.isml` or `pdComponentPage.isml` must include `components/chatwidget/widget` itself, or sit behind this plugin.

## 4. Import the metadata

Set `<context site-id="…"/>` in `metadata/chat-widget/jobs.xml` to your site ID, then:

```sh
npm run package:metadata
```

Import `dist/chat-widget-metadata.zip` in **Administration → Site Development → Site Import & Export**:

```text
chat-widget/
├── jobs.xml
└── meta/
    └── custom-objecttype-definitions.xml
```

| Definition | Detail |
| --- | --- |
| `ChatWidgetJourney` | Site-scoped, no staging. Key: journey UUID. Site, locale, customer number, status, timestamps, order number, review fields |
| `ChatWidgetActivity` | Site-scoped, no staging. Key: activity UUID. Journey key, action, result, entity ID, context, customer number, time |
| Job `ChatWidget-PurgeJourneyData` | Step `custom.ChatWidget.PurgeJourneyData`, `ActivityRetentionDays` 90, `JourneyRetentionDays` 365 |

Import after step 3, so the step type from `steptypes.json` is registered. If `site-id` is wrong, the job runs against another site and removes nothing.

## 5. Schedule retention

**Administration → Operations → Jobs → ChatWidget-PurgeJourneyData → Schedule and History**: add a daily trigger. Sandboxes disable scheduled custom jobs; use **Run Now**.

## 6. Verify

1. Open the storefront in a new session and accept tracking: the widget opens after a moment.
2. Browse, add a product, open the cart.
3. Sign in, check out with a saved card, place the order, and submit a review.
4. In **Manage Custom Objects → ChatWidgetJourney**, the journey shows status `reviewed` with the rating.
5. Run the job: status `OK`, log `Journey retention removed N objects.`

## Add it to an existing RefArch workspace

Copy `cartridges/plugin_chatwidget` and `metadata/chat-widget`. Add `sgmf-scripts --compile js --cartridgeName plugin_chatwidget` to `compile:js`, `sgmf-scripts --compile css --cartridgeName plugin_chatwidget` to `compile:scss`, and `sgmf-scripts --uploadCartridge plugin_chatwidget` to `uploadCartridge`. [SFCC-RefArch](https://github.com/Vedesh-reddy/SFCC-RefArch) shows this setup.
