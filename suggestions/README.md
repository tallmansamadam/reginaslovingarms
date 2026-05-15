# Suggestions

A drop-in folder of proposed changes, written as **new files** that don't touch the live site. Pick whatever's useful; ignore the rest.

Everything in here is a suggestion, not a commitment — these are ideas worth considering, ordered loosely by impact. Concrete HTML snippets live under `examples/` so you can preview proposed rewrites before adopting them.

## Files

| File | What it covers |
|---|---|
| `design.md` | Layout, IA, redundancy, visual hierarchy. Page-by-page. |
| `copywriting.md` | Headline + body rewrites with before/after pairs. |
| `technical.md` | SEO, social-share, performance, code-organization. |
| `accessibility.md` | A11y issues and quick fixes. |
| `examples/index-hero.html` | Proposed homepage hero block (drop-in section). |
| `examples/index-stats-bar.html` | Replacement stats bar with 4 metrics, not 1. |
| `examples/about-core-values.html` | Trimmed values section (6 → 4 sharper values). |
| `examples/index-services-consolidated.html` | Single services section replacing the two duplicates. |
| `examples/og-image-spec.md` | Spec for a proper 1200×630 social-share image. |

## How to use

1. Read `design.md` and `copywriting.md` together — they reference each other.
2. Each suggestion is tagged `[Quick]`, `[Medium]`, or `[Bigger]` based on effort.
3. Examples are **partial snippets** — class names and CSS variables already exist in `resources/styles.css`. Most should drop in cleanly.
4. Nothing here modifies existing pages. To adopt a suggestion, copy the snippet into the target file and bump `styles.css?v=N` if any new CSS is involved.

## Priority shortlist

If you only do five things, do these:

1. **Replace social-share image** (`technical.md` § OG image) — broken on most platforms right now.
2. **Merge the two duplicate services sections on index.html** (`design.md` § index.html) — page is 30% shorter, no information lost.
3. **Rewrite the homepage hero** (`copywriting.md` § Hero) — talks about what the visitor gets, not what we do.
4. **Replace the single-stat stats bar with 4 stats** (`examples/index-stats-bar.html`) — same space, much stronger trust.
5. **Host hero/section images locally** (`technical.md` § Image hosting) — Unsplash/Pexels hotlinking is fragile and slow.
