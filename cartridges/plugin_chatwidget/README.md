# plugin_chatwidget

SFRA overlay for a shopping and account assistant with checkout, order reviews and a privacy-safe journey trail.

Use the [repository README](../../README.md) for screenshots and a quick start.
The [installation guide](../../docs/INSTALLATION.md) covers deployment, metadata import, and cartridge-path configuration.
The [code reference](../../docs/CODE-REFERENCE.md) documents every route, helper and template.

The plugin must precede `app_storefront_base` in the site's cartridge path.
Build from the repository root using `npm run build`; no nested npm project or `dw.json` is needed here.

Business Manager locations:

- **Merchant Tools → Custom Objects → Manage Custom Objects → ChatWidgetJourney / ChatWidgetActivity**
- **Administration → Operations → Jobs → ChatWidget-PurgeJourneyData**
