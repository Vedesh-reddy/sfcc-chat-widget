# Troubleshooting

[← README](../README.md) · [Installation](INSTALLATION.md)

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| No launcher on the page | Cartridge not on the path, page cached, or another cartridge ahead overrides the layout | Check the path, clear the page cache, include `components/chatwidget/widget` in that layout |
| Widget never opens automatically | Already greeted in this browser session | Expected; it opens once per session. Use the launcher |
| "Something went wrong" on every screen | Static assets not deployed, or routes blocked | Deploy `cartridge/static`; check `ChatWidget-Capabilities` returns JSON |
| No journeys in Business Manager | Shopper has not accepted tracking, or metadata not imported | Accept the consent banner; import the custom object types |
| Review "could not be saved" | `ChatWidgetJourney` type missing | Import the metadata |
| Checkout says to finish on the secure checkout page | Processor is not `BASIC_CREDIT`, or no usable saved card | Expected; add the processor to `IN_WIDGET_CARD_PROCESSORS` only if its saved-card hooks run without redirects |
| Promo code error | Unknown, expired or inapplicable code | The message comes from SFRA `Cart-AddCoupon` |
| Promotions screen is empty | No active customer promotions | Expected |
| Retention job removes 0 objects | Nothing old enough, or `site-id` points to another site | Check the job's Execution Scope matches the storefront site |
| Step type missing | Cartridge not on the site's path when importing | Set the path, then import `jobs.xml` again |

## Diagnosis order

1. `ChatWidget-Capabilities` returns JSON for the site.
2. Browser network tab: widget requests carry `X-Chat-Widget: 1`.
3. Logs: file prefix `chat-widget`, categories `journey-tracking` and `journey-retention`.
4. Business Manager: journeys appear after accepting tracking and using the widget.
