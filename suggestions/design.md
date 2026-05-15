# Design suggestions

Effort tags: **[Quick]** ≤ 30 min · **[Medium]** ≤ a few hours · **[Bigger]** half-day or more.

## Site-wide

### Stats bar with one stat — make it earn its space `[Quick]`
`index.html` shows a full-width teal "Stats Bar" containing a single 24/7 chip. The pattern visually demands a row of trust signals. Either retire the band or fill it. See `examples/index-stats-bar.html` for a 4-stat drop-in (24/7 Care · Insurance Plans Accepted · Years Serving Chicagoland · Free In-Home Assessments). Numbers in `careers.html` ("96% retention", "15+ years leadership") could move to the homepage instead.

### Logo header crops with `margin: -22% 0` — fragile `[Medium]`
`.logo img { max-height: 270px; margin: -22% 0; ... }` only works because the current `Logo.svg` has a specific amount of vertical whitespace. If the logo is ever re-exported, the crop breaks silently. Two cleaner options:
- Size the header around the actual 480×140 (3.4:1) artwork — no negative margins.
- Use a square version (the new `favicon.svg` mark, or a horizontal lockup with text trimmed) and let CSS sit it next to a text wordmark.

### "Trust" / "Compassion" / "Love" dilution `[Medium]`
"Compassion" and "compassionate" appear 14+ times across pages; "trusted" similarly. When everything is "compassionate," nothing is. Reserve the word for the hero and the values section; rewrite other appearances with concrete language. See `copywriting.md` for replacements.

### CTA proliferation `[Quick]`
On the homepage there are 6+ different CTAs in different shapes/colors competing: "Request Service", "Our Services", "Verify My Coverage Free", "Learn More About Us", "Schedule Free Assessment", "Schedule a Free Consultation". Pick **one** primary action (probably "Schedule a Free In-Home Assessment") and route all "primary" buttons to it. Keep secondary actions textual (links, not buttons).

### Inline `<style>` and `<script>` per page `[Bigger]`
Each page has its own inline `<style>` block (redeclaring CSS variables that already live in `resources/styles.css`) and an inline `<script>` block with nearly-identical JS. This causes drift — e.g., the contact-form `fetch` handler exists on `index.html` and on `contact.html` separately. Consolidate to `resources/styles.css` (already shared) and a new `resources/scripts.js`. Removes ~150 KB of duplicated payload across the site.

---

## index.html

### Two services sections present the same information `[Quick → Medium]`
- **Services Preview** (h2 "Our Home Care Services", 3 cards: Caregiver / IDoA / *Additional*)
- **Full Services Section** (h2 "Comprehensive Home Care Services", 3 cards: Caregiver / IDoA / Additional)

Same services, different photos, different paragraphs. Visitors scroll past the same idea twice. **Keep one.** The "Preview" pattern works well above the fold; the deep dive belongs on `services.html`. See `examples/index-services-consolidated.html` for the merged single section.

### "Approach" cards link to `#about` on the same page `[Quick]`
The three "Customized / Consistent / Transparent" cards have "More Info →" links pointing to `#about` lower on the same page. Clicking three different links and landing in the same place is frustrating. Either remove the links (the cards stand on their own) or route them to specific anchors on `services.html` or `about.html`.

### Hero slider — purpose unclear `[Medium]`
Background slides rotate every 5 s under a 70% teal overlay, so most visitors won't notice they're slides at all. If the slides aren't reinforcing something the headline doesn't say (e.g., showing different care contexts: home / community / hospital discharge), drop the rotation and use one strong still image. Saves layout shift risk and a setInterval.

### Resources grid on homepage is a dead-end list `[Quick]`
"Helpful Health Resources" lists 6 organizations (Medicaid, HCAOA, AMA, Red Cross, Meals on Wheels, IDoA) but the cards have no visible affordance for clicking. Either:
- Add real external links with `target="_blank" rel="noopener"`, or
- Move this block to `resources.html` where it belongs and replace the homepage section with something action-oriented (e.g., a testimonial strip).

---

## about.html

### Six core values is too many `[Quick]`
Compassion, Integrity, Excellence, Dignity, Community, Innovation — six values is a list, not a brand. The strongest healthcare brands pick 3–4. "Innovation" and "Community" feel especially generic for a home-care agency. Recommended cut to 4: **Compassion · Dignity · Reliability · Family**. See `examples/about-core-values.html` for the trimmed block.

### "Trusted Affiliations & Compliance" lives at the bottom `[Quick]`
This block has the strongest credibility signal on the page (IDoA partnership, licensing) but it's buried below the team section. Move it directly under the founding story (or into the page hero as a row of badges).

---

## services.html

### Hero h1 uses two `<br>` with white-space:nowrap spans `[Quick]`
```html
<h1><span style="white-space: nowrap">Comprehensive Care,</span><br><span style="white-space: nowrap">Delivered at Home</span></h1>
```
This locks the line break and prevents text wrapping at narrow widths, causing overflow on mobile. Use one `<br class="lg-only">` (CSS-controlled) or just let it wrap naturally.

### Service "Request This Service" buttons all go to `/contact.html` with no context `[Medium]`
Three "Request This Service" buttons send all visitors to the same generic form. Pre-fill the form via query string (`contact.html?service=caregiver`) and have a small JS snippet read the param and select the right `<option>` in the dropdown.

### Payment cards have no link to verify `[Quick]`
Medicaid / Medicare / Private Insurance / Private Pay cards explain coverage but offer no next step. Add "Check your coverage →" linking to contact.html with the relevant pre-fill.

---

## careers.html

### Apply Now buttons all anchor to `#apply` with no role context `[Medium]`
Same problem as services.html: six different role cards, six "Apply Now" buttons pointing at the same form anchor. Pre-fill the form's role field, or use distinct anchors (`#apply-rn`, `#apply-lpn`).

### Stats inside careers (96% retention, etc.) are buried `[Quick]`
These are strong public-facing trust signals. Mirror them onto `index.html` (see Stats Bar suggestion above) so candidates and care-seekers both benefit.

---

## contact.html

### Phone, email, office address need a clearer hierarchy `[Quick]`
All three contact channels appear as equal cards. For most visitors, phone is faster than form; for care-seekers researching at midnight, the form is better. Make phone visually dominant on the page (large, gold-accented) and demote address to a footer line.

### Form duplicates the form on index.html `[Medium]`
Two `<form>` definitions, two Formspree endpoints (same URL today). When the endpoint changes, both must be updated. Either drop the homepage form and link to `contact.html#form`, or move the form markup to a shared include via build step.

---

## privacy.html

### Two long policies on one page with no in-page nav `[Quick]`
"Privacy Policy & Notice of Privacy Practices" (sections 1–11) is immediately followed by "Terms of Service" (sections 1–10) — a 22-heading wall. Add a sticky in-page TOC on the left, or two tabs at the top to switch between Privacy and Terms.

---

## 404.html

### Helpful, but no search or suggested links `[Quick]`
The page apologizes and offers a "Go Home" button. Add three contextual link suggestions: Services, Contact, Wellness Resources. Costs nothing and recovers visits that arrived from broken external links.

---

## brochure.html / brochure-trifold_1.html

### Two brochure files diverging `[Medium]`
There are two near-identical brochure templates. Pick one canonical version and delete or archive the other to avoid drift when copy is updated.
