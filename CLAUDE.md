# CLAUDE.md — Cloudhouse Hub

This file provides guidance to Claude Code when working with this repository.

## Project Overview

**Cloudhouse Hub** — single-file HTML prototype for the Cloudhouse access portal +
Organisational Control Panel. All CSS/HTML/JS live in `index.html`. No build step,
no dependencies beyond Google Fonts.

Hub is a **net-new product, independent of Guardian**. It has its **own design language** —
do NOT import Guardian's emerald/teal palette. Hub's identity is indigo/blue on slate.
(Methodology — token scaffold, dark-mode mechanism, `showView` routing — was carried over
from the Guardian prototype; visual values were not.)

Source of truth: the three client docs (Bambuk Work Package, Hub Initial Definition,
Hub Strategy & Vision).

## Scope (June)
- **Access Portal** — eligibility-driven tile grid (multi-app / admin-only / single-app
  bypass / no-entitlement / pending-invite states).
- **Control Panel · Identity & Access** — users (search/pagination/last-activity, invite/
  suspend/reactivate), roles (product-prefixed), entitlements toggles, read-only audit log.
- **Control Panel · Token visibility (read-only)** — balance widget, statement v1
  (running balance + line items), filters + CSV/JSON export, low-balance banner.
- **OUT of scope:** token purchasing / payments (layout reserves a "payments-shaped hole").

## Architecture

### `index.html` structure (in order)
1. `<style>` — reset → `:root` design tokens (`--p-*` primitives → semantic → aliases)
   → component styles → `body.dark {}` overrides.
2. `<body>` — top bar, sidebar (Control Panel only), all `div.view[id="v-*"]` (hidden by default).
3. `<script>` — `showView()` routing, table filters, dark-mode sync.

### View routing
`showView(name)` is the single entry point. Calls `showOnly(name)` (hides all, shows
`#v-{name}`), updates sidebar active state and the top-bar label via `LABELS[name]`.
Portal has no sidebar; Control Panel views do.

Views: `portal`, `cp-users`, `cp-roles`, `cp-entitlements`, `cp-audit`, `cp-tokens`, `cp-statement`.

### Eligibility demo
`setScenario(name)` flips the Portal between demo eligibility states:
`multi` (multi-app + admin), `admin` (admin-only), `single` (single-app bypass notice),
`none` (no entitlements), `pending` (pending invites).

### Dark mode
Two-layer (same mechanism as Guardian): CSS `body.dark` overrides token values +
JS `_syncInlineTheme(isDark)` regex-swaps known light hex/rgb in inline styles.
Theme persisted to localStorage key **`hub-theme`**.
GOTCHA: never write `el.style.X` unconditionally — guard with `if (el.style.X !== val)`
to avoid inline-style re-serialization breaking the sync. Prefer CSS tokens over inline hex.

## Deploy chain
`dev → merge to main → merge to public`. GitHub Pages serves `public`.
`deploy.yml` auto-deploys to Pages on push to `main`.

## Git
Conventional Commits: feat/fix/chore/refactor/test/docs. No period at end of header.
