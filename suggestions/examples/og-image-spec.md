# Social-share image spec (`og-image.png`)

## Problem

Current `<meta property="og:image">` points at `Logo.svg`. Facebook, LinkedIn, iMessage, WhatsApp, Slack, Discord, and Twitter/X all reject or fail to render SVG OG images — most show no preview at all when the site is shared.

## Solution

Create a real **PNG or JPEG** at the recommended OpenGraph dimensions, host it at `resources/og-image.png`, and link it from every page's `<head>`.

## Spec

| Property | Value |
|---|---|
| Format | PNG (preferred; supports transparency for the logo) — JPEG is also fine |
| Dimensions | **1200 × 630** (standard OG / 1.91:1 ratio) |
| File size | Target under 300 KB; max 8 MB (Facebook hard limit) |
| Color space | sRGB |
| Safe zone | Keep all text/logo within the inner 1080 × 570 — outer 60px can be cropped on some platforms |

## Art direction

A simple, brand-faithful version:

```
┌──────────────────────────────────────────────────────┐
│                                                      │
│                                                      │
│           [hands-house-heart mark, 240px]            │
│                                                      │
│           REGINA'S LOVING ARMS                       │
│              Home Health Care · Chicago              │
│                                                      │
│           "Home care your family can trust"          │
│                                                      │
│                                                      │
└──────────────────────────────────────────────────────┘
   background: solid teal #1F5F5F or radial gradient
   wordmark color: white #FAF8F3
   tagline color: gold #C9A227
```

Sources for the mark: the `resources/Logo-mark.png` (just generated for the
business-card monogram) is 347 × 347 and will scale up cleanly. Drop it in
at ~240px height centered above the wordmark.

## How to generate

If you have Photoshop, Figma, Affinity Designer, or Canva: 1200×630 canvas,
brand colors, drop in the mark + wordmark + tagline.

If you'd like to script it (Pillow):

```python
from PIL import Image, ImageDraw, ImageFont

W, H = 1200, 630
TEAL = (31, 95, 95)
GOLD = (201, 162, 39)
CREAM = (250, 248, 243)

img = Image.new("RGB", (W, H), TEAL)

# Mark
mark = Image.open("resources/Logo-mark.png").convert("RGBA")
mark.thumbnail((240, 240))
mx = (W - mark.width) // 2
my = H // 2 - mark.height - 30
img.paste(mark, (mx, my), mark)

# Wordmark + tagline (requires fonts)
draw = ImageDraw.Draw(img)
playfair = ImageFont.truetype("path/to/PlayfairDisplay-Bold.ttf", 56)
inter = ImageFont.truetype("path/to/Inter-Regular.ttf", 22)
italic = ImageFont.truetype("path/to/PlayfairDisplay-Italic.ttf", 24)

draw.text((W//2, H//2 + 30), "REGINA'S LOVING ARMS",
          font=playfair, fill=CREAM, anchor="mm")
draw.text((W//2, H//2 + 80), "Home Health Care · Chicago",
          font=inter, fill=CREAM, anchor="mm")
draw.text((W//2, H//2 + 130), "\"Home care your family can trust\"",
          font=italic, fill=GOLD, anchor="mm")

img.save("resources/og-image.png", optimize=True)
```

## Where to wire it up

Replace every occurrence of:
```html
<meta property="og:image" content="https://reginaslovingarms.com/resources/Logo.svg">
<meta name="twitter:image" content="https://reginaslovingarms.com/resources/Logo.png">
```

with:
```html
<meta property="og:image" content="https://reginaslovingarms.com/resources/og-image.png">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">
<meta property="og:image:alt" content="Regina's Loving Arms — home health care in Chicago, IL">
<meta name="twitter:image" content="https://reginaslovingarms.com/resources/og-image.png">
<meta name="twitter:image:alt" content="Regina's Loving Arms — home health care in Chicago, IL">
```

## Validation

After deploying, check the result with:

- Facebook: https://developers.facebook.com/tools/debug/
- LinkedIn: https://www.linkedin.com/post-inspector/
- Twitter/X card validator: cards-dev.twitter.com/validator (deprecated; LinkedIn's inspector is the most representative)

Facebook caches OG previews aggressively — use the debugger's "Scrape Again" after the file is in place.
