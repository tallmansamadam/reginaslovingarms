# Accessibility

Most of the site already gets the basics right (semantic headings, `alt` attributes, `<label for>` on form inputs, color contrast roughly meets WCAG AA). These are concrete gaps and improvements.

---

## Mobile menu button — missing aria-expanded `[Quick]`

```html
<button class="mobile-menu-btn" aria-label="Menu">
  <i class="fas fa-bars"></i>
</button>
```

`aria-label` is static even though the button toggles between open/closed. Screen readers announce "Menu" both when the menu is hidden and when it's open. Fix:

```html
<button class="mobile-menu-btn"
        aria-label="Open menu"
        aria-expanded="false"
        aria-controls="primary-nav">
  <i class="fas fa-bars" aria-hidden="true"></i>
</button>
```

Update `aria-expanded` and `aria-label` in the click handler:
```js
const expanded = mobileNav.classList.toggle('nav--open');
menuBtn.setAttribute('aria-expanded', expanded);
menuBtn.setAttribute('aria-label', expanded ? 'Close menu' : 'Open menu');
```

---

## Skip link `[Quick]`

Keyboard and screen-reader users currently tab through the entire header before reaching content. Add a skip link as the first focusable element in `<body>`:

```html
<a href="#main" class="skip-link">Skip to main content</a>
```

```css
.skip-link {
  position: absolute;
  left: -9999px;
  top: 0;
  background: var(--primary);
  color: var(--white);
  padding: 12px 20px;
  z-index: 9999;
}
.skip-link:focus { left: 0; }
```

And wrap the first content section in `<main id="main">…</main>`.

---

## FontAwesome icons — decorative vs informative `[Quick]`

Many icons are inside meaningful elements (buttons, labels) and don't add information beyond the adjacent text. They should be hidden from assistive tech. Audit `<i class="fas fa-...">` and add `aria-hidden="true"` where the icon is decorative (most cases):

```html
<a href="tel:3127735553" class="header-phone">
  <i class="fas fa-phone-alt" aria-hidden="true"></i>
  <span>312-773-5553</span>
</a>
```

The site uses `aria-hidden="true"` in a few places already but inconsistently — pick the standard and apply everywhere.

---

## Contact form — error states `[Medium]`

The current form has `required` attributes (good — triggers the browser's native validation). It does not have:
- An accessible error summary at the top after a failed submit.
- `aria-invalid` set on fields that fail validation.
- A live region for the JS-driven success message (`<div class="form-success">`).

Suggested success container:
```html
<div class="form-success" role="status" aria-live="polite">…</div>
```

So screen readers announce the submission outcome.

---

## Color contrast spot-checks `[Medium]`

Quick reads from `styles.css`:
- **Body text** `#2C2C2C` on `#FFFFFF` — 12.6:1 (✓ AAA).
- **Light body text** `#666666` on `#FFFFFF` — 5.7:1 (✓ AA, ✗ AAA for body). Acceptable for non-headline copy.
- **Gold accent** `#C9A227` on white — 3.5:1. Fails AA for body text (needs 4.5:1). Currently used mostly for headings and icons, where 3:1 large-text minimum is fine. **Don't use gold for body paragraphs.**
- **Gold accent on teal `#1F5F5F`** — 3.4:1. Borderline; OK for buttons/headings, not for body.

The `.cf-monogram` gold `R` on the business-card front (now replaced with the favicon) and the section-label gold-on-cream pattern are decorative — contrast isn't a blocker there, but worth keeping in mind for future copy.

---

## Form labels visible vs placeholder `[Quick]`

Form inputs use proper `<label for>` (good). Verify on a small screen that the labels are visually above each input (not hidden) — if any are visually hidden in favor of placeholders, restore them. Placeholder-as-label is a common a11y regression.

---

## Image alt text — review for redundancy `[Quick]`

Most `alt` attributes are descriptive — good. A few are over-detailed and read awkwardly:

> alt="Two caregivers attentively helping elderly client at home"

vs.

> alt="Two Regina's Loving Arms caregivers helping an elderly woman at home"

Names + setting + action is the formula. Stock photos: don't include "photo of" or "image of" — screen readers already announce "image."

Decorative images (background overlays, the careers-bg image, the page-hero-bg) should have `alt=""` to be skipped — already done in a couple of places, worth confirming everywhere.

---

## Focus styles `[Quick]`

The current CSS uses `transition: all 0.3s ease` on links via `:root` `--transition`. Browser default `:focus` outlines may be removed by `transition`-related visual quirks. Verify with keyboard-only nav (Tab through the homepage). If outlines are missing, add:

```css
:focus-visible {
  outline: 2px solid var(--secondary);
  outline-offset: 2px;
}
```

This shows focus rings only when keyboard navigating (not on mouse click) — meets WCAG without visually noisy click states.

---

## Hero slides — pause control `[Medium]`

The hero `setInterval(nextSlide, 5000)` runs without a pause button. Users with cognitive disabilities (and WCAG 2.2.2 in general) require auto-rotating content to be pausable. Options:
- Add a pause button (preferred).
- Pause on hover/focus inside the hero.
- Switch to a single static hero image (simpler, see `design.md`).

---

## Print styles `[Quick]`

Pages don't define print styles, so printing the homepage produces a page with header, nav, all the chrome, etc. Add minimal print rules to `styles.css`:

```css
@media print {
  .topbar, .header .nav, .header-cta, .mobile-menu-btn,
  .mobile-call-bar, .back-to-top, .footer { display: none; }
  .hero { min-height: auto; }
  a[href]::after { content: " (" attr(href) ")"; font-size: 0.85em; }
}
```

Patients/families do sometimes print web pages for elderly relatives — worth the small effort.
