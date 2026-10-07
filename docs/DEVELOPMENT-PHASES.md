# Development phases

[← README](../README.md)

The working implementation is organized into five reviewable delivery phases. These branches describe the repository's packaging and review structure; they do not claim to reproduce the original chronological development history.

Each phase has a dedicated feature branch and PR targeting `main`, merged in order.

| Phase | Feature branch | Scope | Pull request |
| --- | --- | --- | --- |
| 01 · Foundation | `feature/phase-01-foundation` | Cartridge identity, custom object types, retention step type and job, attribution and terms | [PR #1](https://github.com/Vedesh-reddy/sfcc-chat-widget/pull/1) |
| 02 · Journey and data | `feature/phase-02-journey-data` | Display-safe projections, capabilities, payment support, journey trail, retention job | [PR #2](https://github.com/Vedesh-reddy/sfcc-chat-widget/pull/2) |
| 03 · Storefront | `feature/phase-03-storefront` | JSON routes, route observers, widget markup, client module, styles, layout overlays | [PR #3](https://github.com/Vedesh-reddy/sfcc-chat-widget/pull/3) |
| 04 · Tooling and quality | `feature/phase-04-tooling-quality` | npm dependencies, build, JS/SCSS/ISML/docs checks, metadata ZIP, GitHub Actions, PR template, contributing guide | [PR #4](https://github.com/Vedesh-reddy/sfcc-chat-widget/pull/4) |
| 05 · Documentation | `feature/phase-05-documentation` | 51 screenshots, shopper and merchant guides, installation, architecture, code reference, troubleshooting | [PR #5](https://github.com/Vedesh-reddy/sfcc-chat-widget/pull/5) |

The same feature is integrated in [SFCC-RefArch](https://github.com/Vedesh-reddy/SFCC-RefArch) through PRs #8–#11.
