# Contributing

Start from `main` and create a focused `feature/`, `fix/`, or `docs/` branch. Keep behavior changes and documentation accurate together.

```sh
npm ci
npm run validate
npm run package:metadata
```

Exercise changed flows on an SFCC sandbox: lint and build cannot validate SFRA route behavior, payment processors, consent, or metadata installation.

Keep the journey trail's privacy rules: never store passwords, card data, security codes, CSRF tokens, addresses, email addresses or search phrases, and store review comments only with consent. New client activities must be added to the controller's allow-list. Reconcile the layout overlays when updating the host storefront.

Keep credentials and generated output out of Git. See [NOTICE.md](NOTICE.md) for attribution and terms.
