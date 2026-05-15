# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Static marketing site for **Regina's Loving Arms Home Health Care, LLC** (Chicago, IL), served at `reginaslovingarms.com`. Plain HTML/CSS/vanilla JS — no build step, no package manager, no tests, no framework. Deployment is GitHub Pages via the `CNAME` file (custom domain). Upstream repo is referenced as `github.com/tallmansamadam/reginaslovingarms` in the JSON-LD on `index.html`.

## Running locally

There is no dev server. Open the HTML files directly in a browser, or serve the directory with any static server, e.g.:

```powershell
python -m http.server 8000
```

Then visit `http://localhost:8000/`.

## Architecture

### Page set
Top-level `.html` files are the entire site: `index`, `about`, `services`, `careers`, `resources`, `contact`, `privacy`, `404`, plus standalone print assets `business-cards.html`, `brochure.html`, `brochure-trifold_1.html`. `sitemap.xml` and `robots.txt` reflect the public set.

### Styles — shared file + per-page overrides
- **`resources/styles.css`** is the single shared stylesheet, loaded by every page. It defines the design system: CSS custom properties on `:root` (brand palette `--primary`/`--secondary`/`--accent`, shadows, transitions), the layout primitives (`.container`, `.btn*`, `.section-header`), and shared chrome (`.site-top`, `.topbar`, `.header`, `.nav`, `.hero`, `.page-hero`, footer).
- **Each page also has an inline `<style>` block in its `<head>`** containing page-specific overrides. The index file redeclares the `:root` variables inside its inline `<style>` — when changing brand tokens, update both `styles.css` and that inline block.
- Stylesheet is loaded with a cache-busting query string: `resources/styles.css?v=N`. **When you change `styles.css`, bump `?v=N` across every HTML file** (currently `v=5`). A previous sync was done with a single `sed` over all HTML files (see `.claude/settings.local.json`).

### Shared chrome must stay in sync
Header (top bar + nav + logo + phone + CTA), footer, mobile call bar, and back-to-top button are duplicated as inline markup in every page — there is no template/include system. When editing the nav, phone number, hours, etc., apply the change to **every** page that contains the block, not just the one you opened.

### JavaScript
There is no separate JS file. Each page has an inline `<script>` near the end of `<body>` that wires up: hero slide rotation (where present), smooth-scroll for `#` anchors, sticky-header scroll state, mobile nav toggle, back-to-top button, and contact-form submission (POSTs the form to whatever URL is set on the `<form action>` — currently a Formspree-style endpoint — and swaps in a success block on 2xx). New behavior should follow the same inline pattern unless we deliberately introduce a shared script.

### SEO / structured data
`index.html` (and other pages) embed JSON-LD (`<script type="application/ld+json">`) describing the business as `HomeAndConstructionBusiness` with `serviceType`, `address`, `telephone`, and `sameAs`. When the business phone, address, services, or domain change, update the JSON-LD blocks in addition to the visible markup.

### Phone number, brand strings
The phone number `312-773-5553` appears in the top bar, header CTA, hero mobile-call button, footer, mobile call bar, contact page, and JSON-LD (`+13127735553`). Treat this — and the legal business name "Regina's Loving Arms Home Health Care, LLC" — as repository-wide constants when updating.

## Repo hygiene

- `__MACOSX/` and `.DS_Store` are macOS zip artifacts. Don't edit, don't ship; safe to ignore or delete.
- `.claude/settings.local.json` contains an allowlist that includes a Windows path under a different user (`C:\Users\frank\…`). That's historical — the project was relocated. Don't rely on those paths.
