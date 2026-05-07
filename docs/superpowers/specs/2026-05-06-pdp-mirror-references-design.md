# PDP Mirror — TWC + Resilia Structure Parity (Design)

**Date:** 2026-05-06
**Author:** Claude Code (Opus 4.7, 1M ctx)
**Status:** awaiting user review

## Goal

Mirror the conversion-optimized section structure of two reference PDPs onto Clinician Company's two existing PDPs, including urgency tactics, named authority, and copy patterns the references rely on.

CONTRACT-5 audit rules (no urgency / no named authority / no pharma-grade / no RN-MD credentials / etc.) **relaxed by user 2026-05-06**. Only mandatory carve-outs are federal-supplement-law items: (1) FDA disclaimer block, (2) no explicit named-disease cure claims.

## References

- **Complete PDP** ← https://www.twc.health/products/ultimate-spike-detox (21 sections inventoried via Playwright)
- **Pure PDP** ← https://try.resilia.shop/rsl (41 sections inventoried via WebFetch)

## Approach

Hybrid build:

- **Complete (TWC mirror)**: gut the existing 18-section template; rebuild against TWC structure; heavy buybox refactor.
- **Pure (Resilia mirror)**: keep existing 21-section base; reorder + relabel + add 3 new sections to match Resilia structure.

Missing assets get clean placeholders per user direction:

- Customer videos → CSS-only video thumb placeholders
- Named doctor portrait → generic "Founder's Note" block
- Reviews app → section hidden until Okendo installed
- Cross-sell partner SKUs → placeholder cards or hide if no SKUs ready
- "Featured On" outlet logos → generic "as-seen-in" silhouettes

## Section maps

### Complete PDP — section order (mirror TWC)

| # | Order key | Section type | Source | Notes |
|---|---|---|---|---|
| 1 | announcement | header-group strip | existing | 3 messages: "FREE U.S. SHIPPING + NO HIDDEN FEES" / "Breakthrough study" link / "Save up to 50%" |
| 2 | nav | header | existing | (lives in header-group, not template) |
| 3 | media | product-media-gallery | existing | Multi-image carousel; 12 placeholders if no real assets |
| 4 | buybox | product-details-buybox **(refactored)** | existing+rebuild | warning chip → member callout → embedded mini video carousel → H1 → protocol subtitle ("Official McCullough Protocol formula" or TCC equivalent) → 4 stat bullets → OTP/Subscribe radio → 4 accordions (last with cross-sell) |
| 5 | sticky | sticky-atc-bar | existing | Add price-pair display: $80.99 / ~~$89.99~~ |
| 6 | video_stories | **video-testimonials** | NEW | "Your Health. Your Stories." — 3 customer videos (placeholder thumbs) |
| 7 | featured_on | press-strip / scrolling-promo-ticker | existing | "Featured On" marquee, 5 placeholder outlet silhouettes |
| 8 | image_band_1 | **image-band** | NEW | Lifestyle / brand hero |
| 9 | testimonials | text-testimonials-slider | existing | 5 quotes; RN/MD credentials allowed |
| 10 | doctor_block | image-with-text | existing | "Detox Stronger. Bounce Back Faster." + "Founder's Note" placeholder portrait + headline |
| 11 | image_band_2 | **image-band** | NEW | Process diagram |
| 12 | image_band_3 | **image-band** | NEW | Ingredient infographic |
| 13 | ingredients | benefits-grid (variant) | existing | 7 tiles: Nattokinase 4x / Dandelion / Bromelain / Turmeric / Selenium / Black Seed / Black Pepper |
| 14 | faq | bottom-faq | existing | 9 Qs incl. raw PMC URLs |
| 15 | cross_sell | **cross-sell-products** | NEW | 4-card grid; Add-to-Cart per card |
| 16 | image_band_4 | **image-band** | NEW | Brand reassurance |
| 17 | reviews | rich-text placeholder | minor | Hidden until Okendo or alt reviews app installed |
| 18 | newsletter | rich-text or new section | minor | "15% off" email capture |
| 19 | footer | footer | existing | Inline FDA disclaimer + "Disclaimer" link |

### Pure PDP — section order (mirror Resilia)

| # | Order key | Section type | Source | Notes |
|---|---|---|---|---|
| 1 | announcement | header-group | existing | "TODAY ONLY: SAVE UP TO 70%" + flag emoji |
| 2 | media | product-media-gallery | existing | Hero carousel |
| 3 | inline_review | text-testimonials-slider (mini) | existing | One verified-buyer 5-star quote w/ photo |
| 4 | rating | rich-text | minor | "Rated 4.9 Excellent" badge strip |
| 5 | buybox | product-details-buybox | existing | Standard (no Complete refactor) |
| 6 | subscription | **subscription-features** | NEW | 3 icons: auto-refill / cancel / 3–6 day delivery |
| 7 | guarantee | money-back-guarantee | existing | "Feel better or it's free!" |
| 8 | how_it_works | research-highlights | existing | "The Science Behind Spike Detox" |
| 9 | compounds | benefits-grid | existing | 3 actives: Nattokinase / Bromelain / Dandelion |
| 10 | symptoms | **symptom-grid** | NEW | 8 emoji tiles |
| 11 | timeline_short | expectation-timeline | existing | 3 stages |
| 12 | who_for | rich-text | minor | Bulleted "who should use this" |
| 13 | testimonial_repeat | text-testimonials-slider | existing | Same quote as #3, repeated |
| 14 | problem_state | problem-statement | existing | "Modern Spike Burden Is Real" |
| 15 | origin | sumi-origin-story | existing | "Discovered in Tokyo, 1980" |
| 16 | survey_stats | outcome-stats | existing | 4 percentages w/ asterisk disclaimer |
| 17 | one_ingredient | image-with-text | existing | "One Ingredient. Endless Spike Defense." |
| 18 | video_stories | **video-testimonials** | NEW (shared) | 4 customer story tiles |
| 19 | cta_repeat | repeat-cta | existing | "BUY NOW & SAVE" |
| 20 | trust_promise | trust-strip | existing | 6-icon Resilia-style promise |
| 21 | timeline_long | expectation-timeline | existing | 6 stages: 1wk → 12mo |
| 22 | comparison | brand-comparison-table | existing | "Why Spike Detox Outperforms…" |
| 23 | special_offer | **special-offer-callout** | NEW | "Buy 2 Get 1 Free, TODAY ONLY" |
| 24 | promo_bar | promo-countdown-bar | existing | "TODAY ONLY: UP TO 70%" |
| 25 | cta_repeat_2 | repeat-cta | existing | Second buy button |
| 26 | exclusivity | rich-text | minor | "Not Available on Amazon or eBay" |
| 27 | faq | bottom-faq | existing | 10 Qs |
| 28 | footer_cta | repeat-cta | existing | Final buy button |
| 29 | footer | footer | existing | 30-day MBG + © + FDA disclaimer |

## New sections to build

### 1. `sections/video-testimonials.liquid`
Carousel of 3–4 customer story video thumbnails with play overlay. Each thumb: image + name + 1-line caption + optional vertical-format aspect ratio. Click triggers lightbox or inline play. **Schema:** count, eyebrow, headline, per-thumb (image, name, caption, video_url).

### 2. `sections/cross-sell-products.liquid`
4-card horizontal grid. Each card: image / price / title / star rating + review count / 1-line tagline / Add-to-Cart button. **Schema:** count, headline, per-card (product reference OR manual fields image+title+price+tagline+url).

### 3. `sections/image-band.liquid`
Full-width image with optional headline/subhead overlay. Lightweight. **Schema:** image, optional headline, optional subhead, optional CTA, padding mode (sm/md/lg).

### 4. `sections/subscription-features.liquid`
3-column icon row. Each: SVG icon + bold heading + sub-text. **Schema:** per-item (icon, heading, sub).

### 5. `sections/symptom-grid.liquid` (Pure only)
8-tile responsive grid. Each tile: emoji or icon + heading + 1–2 sentence body. Mobile = 2 col. **Schema:** per-tile (emoji, heading, body).

### 6. `sections/special-offer-callout.liquid` (Pure only)
Banner with: eyebrow ("Special Offer On Now!") + product image + headline + 4 bullet feature list + CTA. Tinted background. **Schema:** eyebrow, headline, image, bullets, cta_text, cta_link.

## Buybox refactor (Complete only)

`product-details-buybox.liquid` extensions to support TWC-style buybox:

- **`warning_chip` block (new)**: small pill at top w/ icon + text (defaults: "Do Not Use if Pregnant, Nursing, or on Blood Thinners").
- **`member_callout` block (new)**: 1-line link block ("1Wellness Members Save 15%" or TCC equivalent).
- **`embedded_video_carousel` block (new)**: 3-thumb mini-carousel with eyebrow text. Reuses `video-testimonials` partial.
- **`otp_vs_subscribe` purchase mode (new)**: 2-radio card layout instead of bundle picker. Each radio: title + active price + crossed-out price + sub-bullets list. Default-selected radio configurable. Reuses bundle widget JS for selling-plan attach.
- **`accordion_with_cross_sell` block (new)**: existing accordion variant that supports child product cards (image + price + title + ATC) instead of just rich-text.

Sticky-ATC bar (`sticky-atc-bar.liquid`) gets price-pair display: $80.99 / ~~$89.99~~ on subscribe state.

**Risk mitigation**: only enable new buybox blocks via per-template settings; default behavior unchanged for any template that doesn't opt in (so Pure PDP and v2 remain stable).

## FDA mandatory carve-outs

1. Inline FDA disclaimer in footer block on each PDP: "These statements have not been evaluated by the Food and Drug Administration. This product is not intended to diagnose, treat, cure, or prevent any disease."
2. No headlines or section copy that say "cures" / "treats" / "prevents" a specific named disease. Adjacent benefit framing (energy, breathing, circulation, immune support, recovery) is fine.

## Build sequence

1. Pull from Shopify (CONTRACT-1) before any edit; commit incoming admin edits separately.
2. Write 6 new section liquid files. **Sub-agent A.**
3. Refactor `product-details-buybox.liquid` for OTP/Sub mode + new block types. **Sub-agent B.**
4. Rebuild `templates/product.spike-detox-complete.json` with TWC section order.
5. Rebuild `templates/product.spike-detox.json` with Resilia section order.
6. Push to preview theme (CONTRACT-2). Playwright QA both PDPs vs reference URLs (per visual-QA memory).
7. Commit per phase, push to GitHub (CONTRACT-3).
8. Resolve open Complete partial-render blocker (2026-05-06) as side-effect of the rewrite.

## Out of scope / deferred

- Real customer video assets — CSS placeholder thumbs only
- Named-doctor portrait — generic "Founder's Note" block
- Cross-sell partner SKUs — placeholder cards or hide if no SKUs
- Okendo reviews app — section hidden until installed
- Real "Featured On" outlet logos — generic silhouettes
- Real before-after / process / ingredient infographic art for the 4 image bands — solid-color tinted placeholders with overlay text

## Open risks

- Buybox refactor touches existing Kaching bundle widget JS, sticky-ATC JS, and price-sync MutationObserver. Mitigated via per-template opt-in flags.
- FDA disclaimer gated behind a footer link on TWC; ours stays inline for liability protection.
- Section count for Pure (29) is high; mobile scroll length will be substantial. This matches Resilia and is intentional. May revisit if bounce metrics suffer.
