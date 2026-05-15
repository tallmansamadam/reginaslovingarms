# Technical / SEO / performance

## OG image (social sharing) is broken `[Quick — high impact]`

`index.html` declares:
```html
<meta property="og:image" content="https://reginaslovingarms.com/resources/Logo.svg">
```

**Problem:** Facebook, LinkedIn, iMessage, WhatsApp, Slack, Discord, Twitter/X — none of them render SVG OG images reliably. Most will show no preview at all. This means links shared to the site preview as plain URLs.

**Fix:** Create a real 1200×630 PNG and link it. See `examples/og-image-spec.md` for art direction. Two-minute version:
- Brand teal background (`#1F5F5F`)
- Centered: the white wordmark + tagline "Home care your family can trust · Chicagoland"
- Save as `resources/og-image.png`
- Update the meta tag in every page's `<head>`.

While you're in there, also fix `twitter:image` (same SVG bug).

---

## External image hosting (Unsplash / Pexels) `[Medium — high impact]`

Nearly every photo on the site is hot-linked from Unsplash or Pexels CDNs:
```
https://images.unsplash.com/photo-1573497019940-...
https://images.pexels.com/photos/7551618/...
```

**Problems:**
1. **Performance.** Third-party CDNs add DNS/TLS handshake latency for each hostname. The first hero image is the LCP element — its load time directly affects SEO.
2. **Reliability.** Unsplash/Pexels URLs can break when photos are removed by uploaders. The page renders with broken `<img>` tags.
3. **Licensing.** Both services allow free use, but hotlinking is not the recommended method and can violate their attribution requirements depending on use.
4. **Caching.** Browsers can't cache across origins as efficiently as same-origin assets, and the cache-busting `?w=600&q=80` query string means each visit can refetch.

**Fix:**
- Download the chosen photos once.
- Run through an optimizer (e.g., Squoosh, ImageMagick, or `tinypng`).
- Save under `resources/images/` with descriptive filenames (e.g., `home-caregiver-elderly-couple.jpg`).
- Replace the `<img src>` attributes.
- Keep attribution notes (where required) in a `resources/images/CREDITS.md`.

This typically halves homepage weight and removes ~6 third-party DNS lookups.

---

## Cache-busting (`styles.css?v=N`) is a manual chore `[Medium]`

Today, updating `styles.css` requires bumping the `?v=N` query across 8 HTML files. Easy to forget. Options:
- **Switch to filename versioning** (e.g., `styles.5.css`) and let GitHub Pages serve with normal caching. Mismatched filenames force fresh loads automatically. Rename when you actually want a cache bust.
- **Use file-hash** for content-based busting. Requires a tiny build step (Node, Python, or even a shell script).
- **Add a content meta tag** with the file mtime baked in at deploy time.

For a site of this size, filename versioning is the simplest path.

---

## JSON-LD inconsistency `[Quick]`

`index.html` declares the business as `@type: "HomeAndConstructionBusiness"`. That's incorrect — it's a home **healthcare** business. Google has specific schema types:
- **`MedicalBusiness`** (parent type) or
- **`MedicalOrganization`** for licensed providers.

```jsonc
{
  "@context": "https://schema.org",
  "@type": "MedicalOrganization",  // change from HomeAndConstructionBusiness
  "@id": "https://reginaslovingarms.com/#business",
  "name": "Regina's Loving Arms Home Health Care, LLC",
  "medicalSpecialty": ["Geriatric", "HomeCare"],
  ...
}
```

Also worth adding per-page schema:
- `services.html` → array of `Service` items.
- `careers.html` → `JobPosting` for each open role (huge SEO win — Google Jobs ingests these directly).
- `contact.html` → confirm `address`, `openingHoursSpecification`, `telephone`.

---

## `<title>` length `[Quick]`

Most page titles are 60–80 characters, which Google truncates around 60. Tighten:

| Page | Current | Proposed |
|---|---|---|
| index.html | Regina's Loving Arms Home Health Care, LLC \| Chicago, IL | Regina's Loving Arms — Chicago Home Care |
| services.html | Our Services \| Regina's Loving Arms Home Health Care, LLC | Home Care Services in Chicago — Regina's Loving Arms |
| careers.html | Careers \| Regina's Loving Arms Home Health Care, LLC | Careers in Chicago Home Care — Regina's Loving Arms |
| contact.html | Contact Us \| Regina's Loving Arms Home Health Care, LLC | Contact Regina's Loving Arms — Chicago Home Care |

Keeps the brand line shorter and frees room for keywords Google ranks on.

---

## Inline scripts and styles `[Bigger]`

Each page has a copy-pasted `<style>` and `<script>` block. Drift already exists — e.g., `index.html` and `contact.html` both wire the form-submission handler with slightly different selectors. Recommend:
- Move all CSS to `resources/styles.css` (it already exists — this just means deleting the inline blocks).
- Move all JS to a new `resources/scripts.js`.
- Page-specific overrides become CSS classes on the `<body>` (e.g., `<body class="page-services">`).

Expected savings: ~80 KB total payload, easier maintenance, single source of truth for the contact form.

---

## Performance quick wins `[Quick]`

1. **Preload the hero image.** Currently the LCP image isn't preloaded.
   ```html
   <link rel="preload" as="image" href="resources/images/hero-1.jpg" fetchpriority="high">
   ```
2. **Self-host fonts.** Google Fonts pulls Playfair Display and Inter from `fonts.gstatic.com`. Self-host with `font-display: swap` and you drop two preconnects + a render-blocking CSS request.
3. **Drop unused Font Awesome icons.** Current setup loads the full 6.5.1 CSS (~85 KB). The site uses ~20 icons. Either use the [icon-by-icon subset endpoint](https://docs.fontawesome.com/web/setup/upgrade/whats-changed) or inline the specific SVGs.
4. **`loading="lazy"`** is on most images already — confirm it's on every below-the-fold image.

---

## `robots.txt` is overly permissive `[Quick]`

```
User-agent: *
Allow: /
```

This is the default; the explicit `Allow: /` is redundant. Consider adding `Disallow:` for any internal files (e.g., the brochure print files, business cards, the `__MACOSX/` and `.DS_Store` artifacts if any get deployed). Also explicitly disallow common scraper UAs if SEO bot traffic isn't desired.

Also worth adding:
```
Sitemap: https://reginaslovingarms.com/sitemap.xml
```
(Currently present — good.)

---

## Sitemap is missing entries `[Quick]`

`sitemap.xml` lists 7 pages. It's missing `careers.html` is included, but `business-cards.html`, `brochure.html`, `brochure-trifold_1.html`, and `404.html` are correctly excluded. **Add** `careers.html` if it's not already there — re-check; the list has it. The current sitemap looks correct, but verify `<lastmod>` dates are populated (they're absent right now) — Google uses these for crawl prioritization.

```xml
<url>
  <loc>https://reginaslovingarms.com/</loc>
  <lastmod>2026-05-15</lastmod>
  <changefreq>weekly</changefreq>
  <priority>1.0</priority>
</url>
```

---

## Repo hygiene `[Quick]`

- `__MACOSX/` and `.DS_Store` are macOS zip artifacts. They shouldn't be in the repo. Add to `.gitignore` and remove from history when convenient.
- `.claude/settings.local.json` references a different user's path (`C:\Users\frank\…`) — historical, not load-bearing. Safe to leave but worth a cleanup pass.
- `resources/FullSizeRender.JPG` is 2.4 MB and unused (grep finds no references). Either start using it or delete it from the repo.
