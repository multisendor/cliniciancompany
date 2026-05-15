# Alternating Image+Text Section — `alternating-row`

**Date:** 2026-05-15
**Scope:** New reusable Shopify section for editorial image+text rows. First use: 2 rows above the `subscription` strip on the Spike Detox PDP, modeled after `try.resilia.shop/rsl` rows 4–5.
**Status:** Spec — pending user review.

---

## 1. Purpose

Add a problem → solution editorial pair high on the Spike Detox PDP, immediately under the buybox and above the "Auto-refill every 30 days" subscription strip. Pattern reference: Resilia's two alternating image+text rows ("Modern Immunity May Be Falling Short" → "The Ancient Herb Backed by Centuries of Use").

The section is **schema-driven, single-row, reusable**. To get N alternating rows on a page, instantiate the section N times in the template with opposite `image_side` settings. This matches how `origin-story.liquid` is built and gives each row independent reordering / visibility control in the theme editor.

## 2. What it is NOT

- Not a multi-row block-based section (rejected for breaking Shopify's "one section = one visual unit" idiom and losing per-row template `order`)
- Not an extension of `origin-story.liquid` (rejected for mixing pull-quote and bullet-list rhetorical patterns into one schema)
- Not Spike-Detox-specific (the schema is generic — reusable on Complete / v2 PDPs)

## 3. Files

**New:**
- `tcc-theme/sections/alternating-row.liquid` — the section

**Modified:**
- `tcc-theme/templates/product.spike-detox.json` — add two section instances (`cardio_problem`, `cardio_solution`) and slot them into the `order` array between `buybox` and `subscription`

## 4. Section anatomy

Two-column grid. One side is an image (rounded corners, no caption). The other side is a text stack:

1. Eyebrow (optional, small uppercase label)
2. Heading (h2)
3. One or two body paragraphs
4. Optional bullet block: an intro line + 1–4 checkmark items (green dot)
5. Optional closing paragraph

Stacks to a single column at ≤768px, image first then text (mirrors `origin-story` responsive behavior).

## 5. Liquid schema

```jsonc
{
  "name": "Alternating Row",
  "tag": "section",
  "class": "section",
  "disabled_on": { "groups": ["header", "footer"] },
  "settings": [
    { "type": "text",      "id": "eyebrow",           "label": "Eyebrow (optional)" },
    { "type": "text",      "id": "heading",           "label": "Heading" },
    { "type": "textarea",  "id": "body_paragraph_1",  "label": "Body paragraph 1" },
    { "type": "textarea",  "id": "body_paragraph_2",  "label": "Body paragraph 2 (optional)" },
    { "type": "text",      "id": "bullets_intro",     "label": "Bullet list intro (optional)" },
    { "type": "text",      "id": "bullet_1",          "label": "Bullet 1 (optional)" },
    { "type": "text",      "id": "bullet_2",          "label": "Bullet 2 (optional)" },
    { "type": "text",      "id": "bullet_3",          "label": "Bullet 3 (optional)" },
    { "type": "text",      "id": "bullet_4",          "label": "Bullet 4 (optional)" },
    { "type": "textarea",  "id": "closing_paragraph", "label": "Closing paragraph (optional)" },
    { "type": "image_picker", "id": "image",                "label": "Image (preferred)" },
    { "type": "text",         "id": "image_asset_filename", "label": "Image — theme asset filename (fallback, e.g. benefit-heart.jpg)" },
    { "type": "text",         "id": "image_external_url",   "label": "Image — external URL (fallback)" },
    { "type": "text",         "id": "image_alt",            "label": "Image alt text" },
    { "type": "select", "id": "image_side", "label": "Image side",
      "options": [
        { "value": "image_left",  "label": "Image on left" },
        { "value": "image_right", "label": "Image on right" }
      ], "default": "image_right" },
    { "type": "select", "id": "background_style", "label": "Background",
      "options": [
        { "value": "cream", "label": "Cream (#f4f3ed)" },
        { "value": "white", "label": "White" },
        { "value": "bg",    "label": "Theme off-white (--tcc-bg)" }
      ], "default": "cream" }
  ],
  "presets": [ { "name": "Alternating Row" } ]
}
```

Rendering rules:
- Eyebrow renders only if non-blank.
- Each `bullet_N` renders only if non-blank; the whole bullet block (intro + UL) is hidden if all four are blank.
- `closing_paragraph` renders only if non-blank.
- **Image resolution priority** (first non-blank wins):
  1. `image` (Shopify image_picker) — uses `image | image_url: width: 1200 | image_tag`
  2. `image_asset_filename` — rendered as `<img src="{{ value | asset_url }}" alt="{{ image_alt }}">`
  3. `image_external_url` — rendered as `<img src="{{ value }}" alt="{{ image_alt }}">`
  4. Fallback to a soft neutral cream block (no broken-image icon) — mirrors `origin-story` fallback behavior.
- This three-tier fallback is necessary because `image_picker` settings in JSON templates need explicit Shopify media-library references that the CLI can't pre-seed from theme assets. The text-based `image_asset_filename` lets the template default to images in `/assets/` directly, which is what CONTRACT-1's "use existing product/lifestyle images" decision requires.

## 6. Visual design / CSS

Reuses existing tokens from `assets/tcc-tokens.css`:

| Element | Style |
|---|---|
| Section padding | `.tcc-section-padding` utility |
| Background — cream | `#f4f3ed` (local literal — matches Resilia) |
| Background — white | `#ffffff` |
| Background — bg | `var(--tcc-bg)` |
| Grid | `grid-template-columns: 1fr 1fr; gap: var(--tcc-s-6)` desktop; single column ≤768px |
| Image radius | `var(--tcc-radius)` (6px) — same as cards elsewhere |
| Image aspect | preserve intrinsic; `object-fit: cover; width: 100%` |
| Eyebrow | `var(--tcc-fs-eyebrow)`, `letter-spacing: var(--tcc-ls-eyebrow)`, uppercase, `var(--tcc-ink-soft)` |
| Heading | `var(--tcc-font-sans)`, `var(--tcc-fw-display)`, `var(--tcc-fs-h2)`, `letter-spacing: var(--tcc-ls-display)`, `var(--tcc-ink)` |
| Body text | `var(--tcc-fs-body)`, `line-height: var(--tcc-lh-body)`, `var(--tcc-ink)` |
| Bullet dot | `var(--tcc-positive)` (#00a86b), 8px circle, vertically aligned with first line of bullet text |
| Bullet intro | `var(--tcc-fs-body)`, slightly heavier weight (`var(--tcc-fw-medium)`) |
| Vertical rhythm | paragraph gap `var(--tcc-s-3)`; heading-to-body gap `var(--tcc-s-3)`; bullets-to-closing gap `var(--tcc-s-3)` |
| Class hooks | `.alternating-row`, `.alternating-row--image_left`, `.alternating-row--image_right`, `.alternating-row--bg-cream` |

Mobile breakpoint matches the rest of the theme (`@media (max-width: 768px)`): grid collapses to one column, image first, text second.

Accessibility:
- Heading uses semantic `<h2>`
- Bullets are a real `<ul>` with `<li>` items
- Image gets the alt text from the picker; if blank, `alt=""` (decorative)

## 7. Template wiring

Two section instances are added to `tcc-theme/templates/product.spike-detox.json`:

```jsonc
"cardio_problem": {
  "type": "alternating-row",
  "settings": {
    "eyebrow": "The shift",
    "heading": "Modern Life Is Hard on Your Cardiovascular System",
    "body_paragraph_1": "Today's habits aren't built for how our bodies were designed to move. Long sitting hours, processed-food convenience, chronic stress, and environmental load all stack against the natural processes that keep blood flow steady.",
    "body_paragraph_2": "As we age, the body's own fibrinolytic activity — the natural enzymatic process that helps maintain healthy blood flow — can taper. Many people report feeling sluggish, foggy, or \"off\" without a clear reason.",
    "closing_paragraph": "Supporting that system gently, with a single-ingredient enzyme that has been studied for decades, is one option more cardiovascular-conscious adults are exploring.",
    "image_asset_filename": "benefit-heart.jpg",
    "image_alt": "Anatomical illustration emphasizing the heart and cardiovascular system",
    "image_side": "image_right",
    "background_style": "cream"
  }
},
"cardio_solution": {
  "type": "alternating-row",
  "settings": {
    "eyebrow": "The enzyme",
    "heading": "Nattokinase: A Centuries-Old Enzyme, Dosed to Your Protocol",
    "body_paragraph_1": "Nattokinase is the active enzyme in natto, a traditional Japanese fermented soybean dish consumed for over a thousand years. It was isolated in 1980 by Dr. Hiroyuki Sumi after screening 173 foods for fibrinolytic activity.",
    "body_paragraph_2": "Modern peer-reviewed research has built on that work — including a 12-month, 1,062-adult trial at 10,800 FU/day showing meaningful cardiovascular endpoints.",
    "bullets_intro": "Our nattokinase is built around four non-negotiables:",
    "bullet_1": "Supports natural fibrinolytic activity",
    "bullet_2": "4,000 FU per capsule — dose to your protocol (1, 2, or 3 capsules)",
    "bullet_3": "K2 removed for compatibility with vitamin-K-antagonist blood thinners",
    "bullet_4": "Third-party tested every batch, COA published",
    "closing_paragraph": "One ingredient. Transparent label. The same enzyme adults have used for centuries — measured to a clinically active range.",
    "image_asset_filename": "hero-product-main.jpg",
    "image_alt": "Bottle of Spike Detox nattokinase capsules",
    "image_side": "image_left",
    "background_style": "cream"
  }
}
```

The `order` array is updated to slot the pair between `buybox` and `subscription`:

```jsonc
"order": [
  "media",
  "buybox",
  "cardio_problem",
  "cardio_solution",
  "subscription",
  "guarantee",
  // ... unchanged ...
]
```

Images are wired via `image_asset_filename` pointing to files already in `tcc-theme/assets/`. The section's Liquid resolves these to live URLs via the `asset_url` filter at render time, so no media-library pre-population is needed and the push succeeds with just the JSON edit. The store admin can later re-pick the same images via the `image` field if they prefer the image-picker UX.

## 8. Audit-rule compliance check

Drafted copy was screened against the CONTRACT-5 rules in `CLAUDE.md`:

- ❌ "pharmaceutical-grade" — not used. "Clinical" appears only in "clinically active range" (description of dosing, not credential).
- ❌ "below 3,600 FU is placebo" wedge — not used. Copy uses "dose to your protocol" framing.
- ❌ FLCCC / McCullough / IMA authority — none cited.
- ❌ RN/MD credentials in testimonials — n/a; not a testimonial section.
- ❌ Urgency tactics — none.
- ❌ Diagnostic claims — none. Only structure-function language ("supports", "helps maintain", "support that system").
- ✓ FDA + blood-thinner disclaimers — already in the FAQ footer; this section doesn't introduce health claims that require new disclaimers, but the K2-blood-thinner bullet is framed as a compatibility statement (which is true and audit-safe).

## 9. Build sequence

1. Pull from Shopify (CONTRACT-1) — already done at session start. Re-pull if any time has passed.
2. Create `sections/alternating-row.liquid`.
3. Local CSS smoke check (file parses, schema valid) by running `shopify theme check` if available.
4. Edit `templates/product.spike-detox.json` — add the two section instances and update the `order` array.
5. Push to live theme `161862353132` (CONTRACT-2 — user has not blanket-authorized live pushes; ask before pushing).
6. Visual QA via Playwright at the preview URL: `https://gmmehe-01.myshopify.com/products/spike-detox?preview_theme_id=161862353132` — verify desktop and mobile layouts.
7. Commit and push to GitHub (CONTRACT-3).

## 10. Out of scope

- Adding more than 2 rows (the section supports it via more instances; only 2 are wired now)
- Adding the section to `product.spike-detox-complete.json` or `product.spike-detox-v2.json` — separate task
- Updating tests (no theme test harness exists in this repo)
- Real photography (placeholder uses existing `benefit-heart.jpg` and `hero-product-main.jpg`; CLAUDE.md "open questions" already tracks real photography as a pending item)

## 11. Open questions / decisions to confirm

None — all major decisions resolved during brainstorming:
- Architecture: new single-row section (A)
- Rows: 2, alternating
- Topic: problem → solution (cardio)
- Images: `benefit-heart.jpg` + `hero-product-main.jpg`
- Configurable: yes, schema-driven
- Background: cream (Resilia-style)
