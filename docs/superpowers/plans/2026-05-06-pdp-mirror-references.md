# PDP Mirror Implementation Plan — TWC + Resilia Structure Parity

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Mirror the conversion-optimized section architecture of www.twc.health/products/ultimate-spike-detox onto cliniciancompany.com/products/spike-detox-complete, and try.resilia.shop/rsl onto cliniciancompany.com/products/spike-detox. Includes urgency tactics, named-authority blocks, embedded video carousels, OTP/Subscribe radio purchases, and cross-sell.

**Architecture:** Hybrid build. Complete PDP gets a heavy buybox refactor + template rebuild against TWC's section order. Pure PDP keeps its existing 21-section base, reorders to Resilia's sequence, and gains 3 new sections. 6 new lightweight Liquid section files are shared across both PDPs. Default behavior of `product-details-buybox.liquid` stays unchanged for any template that does not opt into the new block types — Pure stays stable while Complete uses the new modes.

**Tech Stack:** Shopify Liquid, JSON template files, Shopify CLI 3.91+, Playwright MCP for visual QA. CSS variables `--tcc-*` per project convention. No new fonts/colors invented.

**Spec:** `docs/superpowers/specs/2026-05-06-pdp-mirror-references-design.md`

**Reference URLs (live):**
- Complete target: https://www.twc.health/products/ultimate-spike-detox
- Pure target: https://try.resilia.shop/rsl
- Current Complete: https://gmmehe-01.myshopify.com/products/spike-detox-complete?preview_theme_id=161862353132
- Current Pure: https://gmmehe-01.myshopify.com/products/spike-detox?preview_theme_id=161862353132

**CONTRACTS to honor (from CLAUDE.md):**
- CONTRACT-1: pull from Shopify before edit (Task 0)
- CONTRACT-2: push to preview theme `161862353132` after edits (Phase 4)
- CONTRACT-3: push to GitHub after every meaningful commit (each task ends with push)
- CONTRACT-5: **relaxed by user 2026-05-06** — only FDA disclaimer + no explicit cure claims still binding

---

## Pre-flight

### Task 0: Pull latest from Shopify; commit any incoming admin edits

**Files:**
- Operates on: `tcc-theme/**` (sync from live Shopify)

- [ ] **Step 1: Pull latest from preview theme**

```bash
cd "/Users/jonas/Documents/IDE/Health Store/tcc-theme"
shopify theme pull --theme=161862353132 --nodelete
```

- [ ] **Step 2: Check what came down**

```bash
cd "/Users/jonas/Documents/IDE/Health Store"
git status --short
```

Expected: either clean (nothing came down) OR a list of modified files.

- [ ] **Step 3: If any files changed, commit them as a separate sync commit**

```bash
git add tcc-theme/
git commit -m "sync(theme): pull from Shopify admin before PDP mirror build

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
git push origin main
```

If clean, skip the commit and move on.

- [ ] **Step 4: Mark task complete**

---

## Phase 1 — Build 6 new section liquids (parallelizable)

All Phase 1 tasks are independent and can be dispatched to parallel sub-agents. Each new section follows the convention pattern of `tcc-theme/sections/trust-strip.liquid` and `tcc-theme/sections/hsa-fsa-strip.liquid`:

- Outer `<div>` with `tcc-section-padding`, `data-section-type`, `data-section-id`
- Inner `<div class="tcc-page-width">` for max-width
- BEM class naming: `section-name__element`
- Inline `<style>` block (scoped via section ID where reasonable)
- `{% schema %}` at end with `tag: "section"`, `class: "section"`, `disabled_on: { groups: ["header","footer"] }`, presets array
- CSS variables: `--tcc-bg`, `--tcc-ink`, `--tcc-ink-soft`, `--tcc-card`, `--tcc-line`, `--tcc-font-sans`, `--tcc-fs-eyebrow` (11px), `--tcc-fs-body` (16px), `--tcc-fs-small` (13px), spacing scale `--tcc-s-2/3/4/5` (16/24/32/40px)
- Mobile breakpoints at 989px, 749px, 599px
- **No blank-string `default` values on `text` settings, no defaults on `url` settings** — Shopify CLI rejects these (per Phase 4 fixes in STATUS-pdp.md)
- Single `<style>` per section; no global CSS leaks

After each section is built, the sub-agent must push to the preview theme and screenshot the section in isolation via Playwright before committing.

---

### Task 1: `sections/video-testimonials.liquid`

**Files:**
- Create: `tcc-theme/sections/video-testimonials.liquid`

**Section purpose:** "Your Health. Your Stories." carousel of 3-4 customer story video thumbnails with play overlay. Mirrors TWC's `video-carousel-section`. Used standalone on Complete PDP and (later) referenced as a partial inside the buybox.

**Schema (full):**

```json
{
  "name": "Video Testimonials",
  "tag": "section",
  "class": "section",
  "disabled_on": { "groups": ["header", "footer"] },
  "settings": [
    { "type": "text", "id": "eyebrow", "label": "Eyebrow", "default": "YOUR HEALTH. YOUR STORIES." },
    { "type": "text", "id": "heading", "label": "Heading", "default": "Real customers, real recovery." },
    { "type": "select", "id": "layout", "label": "Layout", "options": [
        { "value": "horizontal", "label": "Horizontal carousel" },
        { "value": "grid", "label": "Grid (3-up)" }
      ], "default": "horizontal"
    }
  ],
  "blocks": [
    {
      "type": "video_card",
      "name": "Video card",
      "limit": 6,
      "settings": [
        { "type": "image_picker", "id": "thumbnail", "label": "Thumbnail image" },
        { "type": "text", "id": "name", "label": "Name", "default": "Customer name" },
        { "type": "text", "id": "caption", "label": "Caption" },
        { "type": "url", "id": "video_url", "label": "Video URL (YouTube/Vimeo/direct mp4)" },
        { "type": "text", "id": "duration", "label": "Duration text", "default": "1:24" }
      ]
    }
  ],
  "presets": [
    {
      "name": "Video Testimonials",
      "blocks": [
        { "type": "video_card", "settings": { "name": "Shaun", "caption": "His story.", "duration": "1:24" } },
        { "type": "video_card", "settings": { "name": "Gina", "caption": "Her story.", "duration": "0:56" } },
        { "type": "video_card", "settings": { "name": "Taylor", "caption": "Their story.", "duration": "1:12" } }
      ]
    }
  ]
}
```

**Liquid behavior:**
- Render eyebrow + heading at top, centered.
- Loop `section.blocks` filtered by type `video_card`.
- Each card renders a 9:16 vertical aspect-ratio container with the thumbnail (or CSS-only gradient placeholder if `block.settings.thumbnail == blank`).
- Centered play-button SVG overlay on top.
- Name + caption + duration below thumbnail.
- Click → opens lightbox or new tab to `video_url`. If `video_url == blank`, click is a no-op (cursor: default).
- Horizontal layout: flex/scroll-snap horizontal at all widths, 3-up on desktop / 2-up on tablet / horizontal scroll on mobile.
- Grid layout: 3-col desktop, 1-col mobile.

**CSS placeholder (when no thumbnail):**

```css
.video-testimonials__thumb--placeholder {
  background: linear-gradient(135deg, var(--tcc-ink) 0%, #1a2840 100%);
}
.video-testimonials__thumb--placeholder::after {
  content: "";
  position: absolute; inset: 0;
  background-image: radial-gradient(circle at 30% 70%, rgba(255,255,255,0.06) 0%, transparent 60%);
}
```

**Verification:**

- [ ] **Step 1: Write file with schema + Liquid + style block**
- [ ] **Step 2: Validate locally**

```bash
cd "/Users/jonas/Documents/IDE/Health Store/tcc-theme"
shopify theme push --theme=161862353132 --nodelete --only=sections/video-testimonials.liquid --json
```

Expected: success, no schema errors. If schema errors, fix and re-push.

- [ ] **Step 3: Add a temporary instance to Pure PDP template, push, screenshot via Playwright**

Add an entry to `templates/product.spike-detox.json` `sections` block + `order` array:

```json
"video_test": { "type": "video-testimonials", "blocks": {} }
```

Push, then snapshot via Playwright at `https://gmmehe-01.myshopify.com/products/spike-detox?preview_theme_id=161862353132`.

- [ ] **Step 4: Visual verify (3 placeholder thumbs render correctly, mobile stacks correctly)**

If layout broken: fix CSS, re-push, re-screenshot.

- [ ] **Step 5: Remove temp Pure entry; the section will be added properly during Phase 3**

- [ ] **Step 6: Commit + push**

```bash
git add tcc-theme/sections/video-testimonials.liquid
git commit -m "feat(pdp): video-testimonials section — 3-up customer story carousel

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
git push origin main
```

---

### Task 2: `sections/cross-sell-products.liquid`

**Files:**
- Create: `tcc-theme/sections/cross-sell-products.liquid`

**Section purpose:** "You May Also Like" 4-card horizontal grid. Each card: image + title + price + star rating + 1-line tagline + Add-to-Cart button. Mirrors TWC's `section-products`.

**Schema (full):**

```json
{
  "name": "Cross-sell Products",
  "tag": "section",
  "class": "section",
  "disabled_on": { "groups": ["header", "footer"] },
  "settings": [
    { "type": "text", "id": "heading", "label": "Heading", "default": "You May Also Like" },
    { "type": "text", "id": "sub_heading", "label": "Sub-heading" }
  ],
  "blocks": [
    {
      "type": "product_card",
      "name": "Product card",
      "limit": 6,
      "settings": [
        { "type": "product", "id": "product", "label": "Product (auto-fill)" },
        { "type": "text", "id": "manual_title", "label": "Title (overrides product)" },
        { "type": "image_picker", "id": "manual_image", "label": "Image (overrides product)" },
        { "type": "text", "id": "manual_price", "label": "Price (overrides product)" },
        { "type": "text", "id": "tagline", "label": "Tagline" },
        { "type": "text", "id": "rating_value", "label": "Rating value", "default": "4.8" },
        { "type": "text", "id": "rating_count", "label": "Review count", "default": "0" },
        { "type": "checkbox", "id": "show_badge", "label": "Show badge", "default": false },
        { "type": "text", "id": "badge_text", "label": "Badge text", "default": "BEST SELLER" },
        { "type": "url", "id": "manual_url", "label": "URL (overrides product link)" }
      ]
    }
  ],
  "presets": [
    { "name": "Cross-sell Products" }
  ]
}
```

**Liquid behavior:**
- 4-col grid desktop, 2-col tablet, horizontal scroll-snap on mobile.
- Each card resolves data from `block.settings.product` if set, else from manual_* fields. This lets the section work even when partner SKUs aren't in Shopify yet.
- Rating renders as 5 SVG stars filled by `rating_value`, plus the value + parenthesized count.
- ATC button is a `<form action="/cart/add" method="post">` if a real product is set; else a link to `manual_url`.
- Optional badge in top-left corner if `show_badge` true.

**Convention notes:**
- Re-use TCC button styles via `tcc-btn` class (look at `repeat-cta.liquid` or `money-back-guarantee.liquid` for current pattern).
- Star SVG: use a simple 5-pointed path in a row.

**Verification:** same 6-step pattern as Task 1 (write → push validate → temp insert → Playwright → remove temp → commit + push).

Commit message: `feat(pdp): cross-sell-products section — 4-card grid with manual override`.

---

### Task 3: `sections/image-band.liquid`

**Files:**
- Create: `tcc-theme/sections/image-band.liquid`

**Section purpose:** Full-width image with optional headline/sub-headline overlay. Used 4× on Complete PDP (lifestyle / process diagram / ingredient infographic / brand reassurance). Lightweight; one image per instance.

**Schema (full):**

```json
{
  "name": "Image Band",
  "tag": "section",
  "class": "section",
  "disabled_on": { "groups": ["header", "footer"] },
  "settings": [
    { "type": "image_picker", "id": "image", "label": "Image" },
    { "type": "text", "id": "image_alt", "label": "Image alt text" },
    { "type": "text", "id": "headline", "label": "Headline overlay (optional)" },
    { "type": "text", "id": "sub_headline", "label": "Sub-headline (optional)" },
    { "type": "select", "id": "overlay_position", "label": "Overlay position", "options": [
        { "value": "center", "label": "Center" },
        { "value": "bottom_left", "label": "Bottom-left" },
        { "value": "bottom_right", "label": "Bottom-right" },
        { "value": "none", "label": "None (image only)" }
      ], "default": "none"
    },
    { "type": "select", "id": "padding", "label": "Vertical padding", "options": [
        { "value": "sm", "label": "Small" },
        { "value": "md", "label": "Medium" },
        { "value": "lg", "label": "Large" }
      ], "default": "md"
    },
    { "type": "select", "id": "placeholder_variant", "label": "Placeholder (when no image)", "options": [
        { "value": "navy", "label": "Navy gradient" },
        { "value": "warm", "label": "Warm gradient" },
        { "value": "muted", "label": "Muted (light)" }
      ], "default": "navy"
    },
    { "type": "url", "id": "cta_url", "label": "CTA URL (optional)" },
    { "type": "text", "id": "cta_text", "label": "CTA text" }
  ],
  "presets": [
    { "name": "Image Band" }
  ]
}
```

**Liquid behavior:**
- Full-width container (no `tcc-page-width` wrap on the image; overlay text wraps inside `tcc-page-width`).
- If `image` is set, render `<img>` with `srcset` from `image | image_url: width: 1600`, lazy-load.
- If `image` is blank, render a CSS gradient placeholder per `placeholder_variant` (see `image-with-text.liquid` Phase 4 fix for the navy gradient pattern — same convention).
- Overlay div positioned per `overlay_position` setting.
- CTA renders as `tcc-btn` link if both `cta_url` and `cta_text` are set.

**Verification:** same 6-step pattern.

Commit message: `feat(pdp): image-band section — full-width image w/ optional overlay + CSS placeholder`.

---

### Task 4: `sections/subscription-features.liquid`

**Files:**
- Create: `tcc-theme/sections/subscription-features.liquid`

**Section purpose:** 3-column icon row. Each: SVG icon + bold heading + sub-text. Used on Pure PDP under the buybox.

**Schema (full):**

```json
{
  "name": "Subscription Features",
  "tag": "section",
  "class": "section",
  "disabled_on": { "groups": ["header", "footer"] },
  "settings": [
    { "type": "checkbox", "id": "show_dividers", "label": "Show vertical dividers between cells", "default": true }
  ],
  "blocks": [
    {
      "type": "feature",
      "name": "Feature",
      "limit": 4,
      "settings": [
        { "type": "select", "id": "icon", "label": "Icon", "options": [
            { "value": "refresh", "label": "Refresh (auto-refill)" },
            { "value": "x_circle", "label": "X circle (cancel)" },
            { "value": "truck", "label": "Truck (delivery)" },
            { "value": "lock", "label": "Lock (secure)" },
            { "value": "tag", "label": "Tag (savings)" }
          ], "default": "refresh"
        },
        { "type": "text", "id": "heading", "label": "Heading", "default": "Auto-refill every 30 days" },
        { "type": "text", "id": "sub", "label": "Sub-text" }
      ]
    }
  ],
  "presets": [
    {
      "name": "Subscription Features",
      "blocks": [
        { "type": "feature", "settings": { "icon": "refresh", "heading": "Auto-refill every 30 days", "sub": "Set it and forget it" } },
        { "type": "feature", "settings": { "icon": "x_circle", "heading": "Cancel anytime", "sub": "No commitments" } },
        { "type": "feature", "settings": { "icon": "truck", "heading": "3-6 day delivery", "sub": "Ships from US warehouse" } }
      ]
    }
  ]
}
```

**Liquid behavior:**
- 3-col grid desktop (auto-fits up to limit), single-col stack on mobile.
- Each cell: icon (top, 28px) + heading (bold, body-size) + sub (small, ink-soft).
- Optional vertical 1px dividers between cells when `show_dividers` true (use `border-left` on cells 2+ desktop, hide mobile).
- Icon SVGs follow the `trust-strip.liquid` icon library pattern (case statement with inline SVG paths).

**Verification:** same 6-step pattern.

Commit message: `feat(pdp): subscription-features section — 3-icon strip for sub-and-save callouts`.

---

### Task 5: `sections/symptom-grid.liquid` (Pure only)

**Files:**
- Create: `tcc-theme/sections/symptom-grid.liquid`

**Section purpose:** 8-tile responsive grid of symptom-resolution callouts. Each: emoji or icon + heading + 1-2 sentence body. Mirrors Resilia's `symptom-solutions-grid`. Pure-only.

**Schema (full):**

```json
{
  "name": "Symptom Grid",
  "tag": "section",
  "class": "section",
  "disabled_on": { "groups": ["header", "footer"] },
  "settings": [
    { "type": "text", "id": "eyebrow", "label": "Eyebrow", "default": "WHAT SPIKE DETOX MAY HELP WITH" },
    { "type": "text", "id": "heading", "label": "Heading", "default": "If any of these sound familiar…" }
  ],
  "blocks": [
    {
      "type": "symptom",
      "name": "Symptom tile",
      "limit": 12,
      "settings": [
        { "type": "text", "id": "emoji", "label": "Emoji (or single icon char)", "default": "🩺" },
        { "type": "text", "id": "heading", "label": "Heading", "default": "Brain fog" },
        { "type": "textarea", "id": "body", "label": "Body" }
      ]
    }
  ],
  "presets": [
    {
      "name": "Symptom Grid",
      "blocks": [
        { "type": "symptom", "settings": { "emoji": "🧠", "heading": "Brain fog", "body": "Mental haze that makes simple tasks feel heavy." } },
        { "type": "symptom", "settings": { "emoji": "🫁", "heading": "Breathing tightness", "body": "Shortness of breath after mild exertion or even at rest." } },
        { "type": "symptom", "settings": { "emoji": "💓", "heading": "Heart racing", "body": "Palpitations or higher resting heart rate than your baseline." } },
        { "type": "symptom", "settings": { "emoji": "🪫", "heading": "Persistent fatigue", "body": "Tired even after a full night's sleep." } },
        { "type": "symptom", "settings": { "emoji": "👅", "heading": "Taste / smell off", "body": "Lingering changes from a past infection." } },
        { "type": "symptom", "settings": { "emoji": "💤", "heading": "Disrupted sleep", "body": "Waking up multiple times or unable to drop into deep sleep." } },
        { "type": "symptom", "settings": { "emoji": "🦴", "heading": "Joint stiffness", "body": "Aches and stiffness, especially in the morning." } },
        { "type": "symptom", "settings": { "emoji": "🛡️", "heading": "Lowered resilience", "body": "Catching every bug going around the house." } }
      ]
    }
  ]
}
```

**Liquid behavior:**
- 4-col grid desktop, 2-col tablet, 1-col mobile.
- Each tile: emoji at top (32-40px), heading bold, body in ink-soft.
- Tiles have soft 1px border + subtle hover lift (translateY -2px, shadow).

**Verification:** same 6-step pattern.

Commit message: `feat(pdp): symptom-grid section — 8-tile what-this-helps-with grid`.

---

### Task 6: `sections/special-offer-callout.liquid` (Pure only)

**Files:**
- Create: `tcc-theme/sections/special-offer-callout.liquid`

**Section purpose:** "Buy 2 Get 1 Free" promo banner. Eyebrow + headline + product image + 4 bullet feature list + CTA. Tinted background. Mirrors Resilia's `special-offer-section`.

**Schema (full):**

```json
{
  "name": "Special Offer Callout",
  "tag": "section",
  "class": "section",
  "disabled_on": { "groups": ["header", "footer"] },
  "settings": [
    { "type": "text", "id": "eyebrow", "label": "Eyebrow", "default": "SPECIAL OFFER ON NOW!" },
    { "type": "text", "id": "headline", "label": "Headline", "default": "Try Spike Detox Risk-Free with our First Time Buyer's Offer!" },
    { "type": "text", "id": "offer_text", "label": "Offer line", "default": "Buy 2 Bottles Get 1 Free" },
    { "type": "image_picker", "id": "image", "label": "Product image" },
    { "type": "text", "id": "cta_text", "label": "CTA text", "default": "Buy Now & Save" },
    { "type": "url", "id": "cta_url", "label": "CTA URL" },
    { "type": "color_background", "id": "bg", "label": "Background color", "default": "#fff7e8" }
  ],
  "blocks": [
    {
      "type": "bullet",
      "name": "Bullet",
      "limit": 6,
      "settings": [
        { "type": "text", "id": "text", "label": "Bullet text" }
      ]
    }
  ],
  "presets": [
    {
      "name": "Special Offer Callout",
      "blocks": [
        { "type": "bullet", "settings": { "text": "60-day money-back guarantee" } },
        { "type": "bullet", "settings": { "text": "Ships from US warehouse" } },
        { "type": "bullet", "settings": { "text": "Single-ingredient transparency" } },
        { "type": "bullet", "settings": { "text": "Cancel anytime, no commitment" } }
      ]
    }
  ]
}
```

**Liquid behavior:**
- 2-col layout desktop (image left, text right). Stacks on mobile.
- Background uses `section.settings.bg`.
- Eyebrow uppercase + tracked, headline bold/large, offer line accent-color tinted.
- Bullets render with checkmark prefix (re-use `trust-strip` checkmark SVG inline).
- CTA renders as primary `tcc-btn`.

**Verification:** same 6-step pattern.

Commit message: `feat(pdp): special-offer-callout section — Buy-2-Get-1 promo banner`.

---

## Phase 2 — Buybox refactor (sequential, depends on Task 1)

### Task 7: Add new block types to `product-details-buybox.liquid`

**Files:**
- Modify: `tcc-theme/sections/product-details-buybox.liquid`

**New block types to add to the section's `{% schema %}` `blocks` array** (without removing any existing block types):

1. `warning_chip` — small pill at top of buybox

   ```json
   {
     "type": "warning_chip",
     "name": "Warning chip",
     "limit": 2,
     "settings": [
       { "type": "select", "id": "icon", "label": "Icon", "options": [
           { "value": "alert", "label": "Alert" }, { "value": "info", "label": "Info" }
         ], "default": "alert"
       },
       { "type": "text", "id": "text", "label": "Text", "default": "Do Not Use if Pregnant, Nursing, or on Blood Thinners" },
       { "type": "color", "id": "tint", "label": "Tint color", "default": "#fef2e8" }
     ]
   }
   ```

2. `member_callout` — single-line link block

   ```json
   {
     "type": "member_callout",
     "name": "Member callout",
     "limit": 2,
     "settings": [
       { "type": "text", "id": "label", "label": "Label", "default": "Members Save 15%" },
       { "type": "url", "id": "url", "label": "Link URL" }
     ]
   }
   ```

3. `embedded_video_carousel` — eyebrow + 3-tile mini-carousel referencing a separate Liquid include

   ```json
   {
     "type": "embedded_video_carousel",
     "name": "Embedded video carousel",
     "limit": 1,
     "settings": [
       { "type": "text", "id": "eyebrow", "label": "Eyebrow", "default": "YOUR HEALTH. YOUR STORIES." },
       { "type": "image_picker", "id": "thumb_1", "label": "Thumbnail 1" },
       { "type": "text", "id": "name_1", "label": "Name 1", "default": "Shaun" },
       { "type": "url", "id": "video_1", "label": "Video URL 1" },
       { "type": "image_picker", "id": "thumb_2", "label": "Thumbnail 2" },
       { "type": "text", "id": "name_2", "label": "Name 2", "default": "Gina" },
       { "type": "url", "id": "video_2", "label": "Video URL 2" },
       { "type": "image_picker", "id": "thumb_3", "label": "Thumbnail 3" },
       { "type": "text", "id": "name_3", "label": "Name 3", "default": "Taylor" },
       { "type": "url", "id": "video_3", "label": "Video URL 3" }
     ]
   }
   ```

4. `accordion_with_cross_sell` — accordion item that can render child product cards

   ```json
   {
     "type": "accordion_with_cross_sell",
     "name": "Accordion w/ cross-sell",
     "limit": 4,
     "settings": [
       { "type": "text", "id": "title", "label": "Accordion title", "default": "Pairs well with" },
       { "type": "richtext", "id": "intro", "label": "Intro" },
       { "type": "product", "id": "product_1", "label": "Cross-sell product 1" },
       { "type": "product", "id": "product_2", "label": "Cross-sell product 2" },
       { "type": "product", "id": "product_3", "label": "Cross-sell product 3" }
     ]
   }
   ```

**Liquid integration order** (top-to-bottom inside buybox):

```
{%- for block in section.blocks -%}
  {%- case block.type -%}
    {%- when 'warning_chip' -%}
      {% render 'buybox-warning-chip', block: block %}
    {%- when 'member_callout' -%}
      {% render 'buybox-member-callout', block: block %}
    {%- when 'embedded_video_carousel' -%}
      {% render 'buybox-embedded-video', block: block %}
    [...existing block types unchanged...]
    {%- when 'accordion_with_cross_sell' -%}
      {% render 'buybox-accordion-cross-sell', block: block %}
  {%- endcase -%}
{%- endfor -%}
```

Add these snippet files alongside (each is a tiny Liquid file, no schema):

- `tcc-theme/snippets/buybox-warning-chip.liquid`
- `tcc-theme/snippets/buybox-member-callout.liquid`
- `tcc-theme/snippets/buybox-embedded-video.liquid`
- `tcc-theme/snippets/buybox-accordion-cross-sell.liquid`

**Verification:**

- [ ] **Step 1: Read existing `product-details-buybox.liquid` fully**
- [ ] **Step 2: Add the 4 new block types to the schema (preserve order; new ones at end of `blocks` array)**
- [ ] **Step 3: Add the 4 case branches inside the existing block loop**
- [ ] **Step 4: Create the 4 snippet files with their inner markup + scoped style block**
- [ ] **Step 5: Push to preview theme**

```bash
cd "/Users/jonas/Documents/IDE/Health Store/tcc-theme"
shopify theme push --theme=161862353132 --nodelete --json
```

Expected: success. If schema fails, fix and re-push.

- [ ] **Step 6: Verify default behavior unchanged** — load Pure PDP, screenshot, confirm visually identical to pre-refactor state (no new blocks render because the Pure template doesn't reference them).

- [ ] **Step 7: Add test instances of each new block to a sandbox copy of the Complete template** (don't commit yet) and verify each renders.

- [ ] **Step 8: Commit + push**

```bash
git add tcc-theme/sections/product-details-buybox.liquid tcc-theme/snippets/buybox-*.liquid
git commit -m "feat(buybox): add warning_chip + member_callout + embedded_video + accordion_w_cross_sell block types

Per-template opt-in; Pure PDP behavior unchanged.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
git push origin main
```

---

### Task 8: Add OTP/Subscribe purchase mode + sticky-ATC price-pair display

**Files:**
- Modify: `tcc-theme/sections/product-details-buybox.liquid` (add `purchase_mode` setting)
- Modify: `tcc-theme/sections/sticky-atc-bar.liquid` (price-pair render)
- Create: `tcc-theme/snippets/buybox-otp-vs-subscribe.liquid`

**New section setting on `product-details-buybox.liquid`:**

```json
{
  "type": "select",
  "id": "purchase_mode",
  "label": "Purchase mode",
  "options": [
    { "value": "bundle", "label": "Bundle picker (Kaching widget) — default" },
    { "value": "otp_vs_subscribe", "label": "OTP vs Subscribe radio cards" }
  ],
  "default": "bundle"
}
```

**Liquid integration:**

In the buybox section, where the bundle widget currently renders, add a conditional:

```liquid
{%- if section.settings.purchase_mode == 'otp_vs_subscribe' -%}
  {% render 'buybox-otp-vs-subscribe', product: product, section: section %}
{%- else -%}
  {%- comment -%} existing Kaching bundle widget code unchanged {%- endcomment -%}
{%- endif -%}
```

**`buybox-otp-vs-subscribe.liquid` responsibilities:**

- Render 2 radio cards stacked vertically.
- Card 1: "One-Time Purchase" + price (compare-at if set).
- Card 2: "Subscribe & Save 10%" + selected by default + crossed-out compare-at + "Save 10%" tag + sub-bullets list ("Delivery every 30 days", "Re-Stock Priority", "Exclusive Discounts").
- Use Shopify `selling_plan_groups` API: if product has a selling plan, attach selling-plan ID on subscribe radio change.
- Quantity stepper + Add-to-Cart button below cards.
- The form posts to `/cart/add` with the selected variant ID and (if subscribe) selling_plan ID.
- JS: minimal vanilla JS to handle radio change → update hidden form fields + visible price.

**Sticky ATC bar price-pair:**

In `sticky-atc-bar.liquid`, where the price currently renders, change to:

```liquid
{%- if product.compare_at_price > product.price -%}
  <span class="sticky-atc__price">${{ product.price | money_without_currency }}</span>
  <span class="sticky-atc__price--compare">${{ product.compare_at_price | money_without_currency }}</span>
{%- else -%}
  <span class="sticky-atc__price">${{ product.price | money_without_currency }}</span>
{%- endif -%}
```

Add CSS rule for `.sticky-atc__price--compare`:

```css
.sticky-atc__price--compare {
  color: var(--tcc-ink-soft);
  text-decoration: line-through;
  margin-left: 8px;
  font-weight: 400;
}
```

**Verification:**

- [ ] **Step 1: Add `purchase_mode` setting + Liquid conditional**
- [ ] **Step 2: Create `buybox-otp-vs-subscribe.liquid`**
- [ ] **Step 3: Update `sticky-atc-bar.liquid` for price-pair**
- [ ] **Step 4: Push to preview theme**
- [ ] **Step 5: Verify Pure PDP unchanged (purchase_mode defaults to bundle)**
- [ ] **Step 6: Sandbox-test the OTP/Sub mode by manually flipping the setting on Complete** — screenshot, verify 2 radio cards render, click each, confirm form attaches selling_plan ID correctly.
- [ ] **Step 7: Commit + push**

```bash
git add tcc-theme/sections/product-details-buybox.liquid \
        tcc-theme/sections/sticky-atc-bar.liquid \
        tcc-theme/snippets/buybox-otp-vs-subscribe.liquid
git commit -m "feat(buybox): OTP-vs-Subscribe radio purchase mode + sticky-ATC price-pair

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
git push origin main
```

---

## Phase 3 — Template assembly

### Task 9: Rebuild `templates/product.spike-detox-complete.json` (TWC mirror)

**Files:**
- Modify: `tcc-theme/templates/product.spike-detox-complete.json` (full rewrite)

**Section order (16 entries in template — `announcement`, `nav`, `footer` live in header-group / footer-group, not in the product template):**

```
media → buybox → sticky → video_stories → featured_on → image_band_1 → testimonials → doctor_block → image_band_2 → image_band_3 → ingredients → faq → cross_sell → image_band_4 → reviews → newsletter
```

**Per-section settings (key fields only — sub-agent fills the rest):**

- `media`: `{ "type": "product-media-gallery" }` — defaults
- `buybox`: `{ "type": "product-details-buybox", "settings": { "purchase_mode": "otp_vs_subscribe", "show_credibility_row": true }, "blocks": { "warning_chip_1": {...}, "member_callout_1": {...}, "embedded_video_1": {...}, "title_1": {...}, "stat_bullets_1": {...}, "trust_features_1": {...}, "accordion_1": {...} (×4 incl. cross-sell) }, "block_order": [...] }`
- `sticky`: `{ "type": "sticky-atc-bar" }`
- `video_stories`: `{ "type": "video-testimonials", "settings": { "eyebrow": "YOUR HEALTH. YOUR STORIES.", "heading": "Real customers, real recovery." } }` + 3 `video_card` blocks
- `featured_on`: `{ "type": "press-strip" }` (existing) — labels "FEATURED ON" + 5 placeholder logo blocks
- `image_band_1`: `{ "type": "image-band", "settings": { "padding": "lg", "placeholder_variant": "navy" } }`
- `testimonials`: `{ "type": "text-testimonials-slider" }` — 5 testimonials, RN/MD credentials allowed in copy
- `doctor_block`: `{ "type": "image-with-text", "settings": { "headline": "Detox Stronger. Bounce Back Faster.", "body": "Formulated by clinicians who looked at the data the rest of the industry won't…", "image_position": "left" } }` (use a "Founder's Note" placeholder body — generic, no real doctor name unless user provides)
- `image_band_2`: `{ "type": "image-band", "settings": { "padding": "md", "placeholder_variant": "warm" } }` (process diagram placeholder)
- `image_band_3`: `{ "type": "image-band", "settings": { "padding": "md", "placeholder_variant": "muted" } }` (ingredient infographic placeholder)
- `ingredients`: `{ "type": "benefits-grid" }` — 7 tiles: Nattokinase 4x / Dandelion / Bromelain / Turmeric / Selenium / Black Seed / Black Pepper. Use existing benefits-grid block API.
- `faq`: `{ "type": "bottom-faq" }` — 9 Qs incl. raw PMC URLs in body of each. Last FAQ block contains the FDA disclaimer text in body.
- `cross_sell`: `{ "type": "cross-sell-products" }` — 4 product_card blocks (manual mode if no real SKUs ready)
- `image_band_4`: `{ "type": "image-band", "settings": { "padding": "md" } }` (brand reassurance)
- `reviews`: `{ "type": "rich-text", "settings": { "content": "<!-- reviews module coming soon -->", "disabled": true } }` — render hidden until Okendo or alt installed
- `newsletter`: `{ "type": "rich-text", "settings": { "content": "Stay Connected — Subscribe and follow us to get **15% off**." } }`

**FDA disclaimer placement:** add a paragraph at the bottom of the last FAQ block body: "These statements have not been evaluated by the Food and Drug Administration. This product is not intended to diagnose, treat, cure, or prevent any disease."

**Verification:**

- [ ] **Step 1: Read existing `templates/product.spike-detox-complete.json` for reference (preserves any pre-existing section IDs that the buybox JS uses)**
- [ ] **Step 2: Write full new template JSON matching the order above**
- [ ] **Step 3: Push only this template + section deps**

```bash
shopify theme push --theme=161862353132 --nodelete --only=templates/product.spike-detox-complete.json --json
```

- [ ] **Step 4: Load Complete PDP via preview** — `https://gmmehe-01.myshopify.com/products/spike-detox-complete?preview_theme_id=161862353132` and confirm all 16 template sections render top-to-bottom (no missing sections, no Liquid errors). Header-group + footer-group render their own announcement / nav / footer.

- [ ] **Step 5: Commit + push**

```bash
git add tcc-theme/templates/product.spike-detox-complete.json
git commit -m "feat(pdp): Complete PDP rebuild — TWC structure mirror (16 template sections)

Resolves Complete PDP partial-render blocker by removing JSONC comment header and rebuilding section order.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
git push origin main
```

---

### Task 10: Rebuild `templates/product.spike-detox.json` (Resilia mirror — Pure)

**Files:**
- Modify: `tcc-theme/templates/product.spike-detox.json` (full rewrite)

**Section order (27 entries in template — `announcement` lives in header-group, `footer` in footer-group):**

```
media → inline_review → rating → buybox → subscription → guarantee → how_it_works → compounds → symptoms → timeline_short → who_for → testimonial_repeat → problem_state → origin → survey_stats → one_ingredient → video_stories → cta_repeat → trust_promise → timeline_long → comparison → special_offer → promo_bar → cta_repeat_2 → exclusivity → faq → footer_cta
```

**Per-section settings (key fields only):**

- `media`: `{ "type": "product-media-gallery" }`
- `inline_review`: `{ "type": "text-testimonials-slider", "settings": { "max_visible": 1, "compact": true } }` — single 5-star quote with photo
- `rating`: `{ "type": "rich-text", "settings": { "content": "⭐ Rated 4.9 Excellent — based on verified buyers" } }`
- `buybox`: `{ "type": "product-details-buybox", "settings": { "show_credibility_row": true }, "blocks": { existing Pure blocks unchanged } }` — keep current bundle picker, no new block types
- `subscription`: `{ "type": "subscription-features" }` — 3 default blocks (refresh / cancel / truck)
- `guarantee`: `{ "type": "money-back-guarantee", "settings": { "headline": "Feel better or it's free!", "days": "60" } }`
- `how_it_works`: `{ "type": "research-highlights", "settings": { "headline": "The Science Behind Spike Detox", "intro": "Why this single ingredient earns its place in your routine." } }` — existing 3-card research grid
- `compounds`: `{ "type": "benefits-grid" }` — 3 tiles: Nattokinase / Bromelain (in pure formula? confirm with v1) / BioPerine
- `symptoms`: `{ "type": "symptom-grid" }` — default 8 blocks
- `timeline_short`: `{ "type": "expectation-timeline", "settings": { "stages": "3" } }` — First few days / 2-3 weeks / Ongoing
- `who_for`: `{ "type": "rich-text", "settings": { "content": "## Who Spike Detox is for…\n- Anyone who's had COVID and never quite bounced back\n- Adults concerned about cardiovascular load post-2020\n- People who've been on multiple supplement protocols and want to consolidate\n- Health-conscious adults who read every label\n- Practitioners and their patients who want disclosed mg per active" } }`
- `testimonial_repeat`: same component as `inline_review`, different quote
- `problem_state`: `{ "type": "problem-statement", "settings": { "headline": "Modern Spike Burden Is Real" } }`
- `origin`: `{ "type": "sumi-origin-story" }` — existing
- `survey_stats`: `{ "type": "outcome-stats" }` — 4 stat blocks with percentages, asterisk disclaimer in footer block
- `one_ingredient`: `{ "type": "image-with-text", "settings": { "headline": "One Ingredient. Endless Spike Defense.", "image_position": "right" } }`
- `video_stories`: `{ "type": "video-testimonials" }` — 4 blocks (one more than Complete to match Resilia)
- `cta_repeat`: `{ "type": "repeat-cta", "settings": { "label": "BUY NOW & SAVE" } }`
- `trust_promise`: `{ "type": "trust-strip" }` — 6 default blocks (use existing Resilia-style headings)
- `timeline_long`: `{ "type": "expectation-timeline", "settings": { "stages": "6" } }` — 1wk / 1mo / 2mo / 3mo / 6mo / 12mo
- `comparison`: `{ "type": "brand-comparison-table" }` — existing
- `special_offer`: `{ "type": "special-offer-callout" }` — defaults
- `promo_bar`: `{ "type": "promo-countdown-bar", "settings": { "text": "TODAY ONLY: UP TO 70%", "show_countdown": false } }`
- `cta_repeat_2`: `{ "type": "repeat-cta" }`
- `exclusivity`: `{ "type": "rich-text", "settings": { "content": "**Note:** Not Available on Amazon or eBay." } }`
- `faq`: `{ "type": "bottom-faq" }` — 10 Qs, last block contains FDA disclaimer text
- `footer_cta`: `{ "type": "repeat-cta", "settings": { "label": "BUY NOW & SAVE" } }`

**Verification:**

- [ ] **Step 1: Read existing `templates/product.spike-detox.json` for current Pure block IDs that the buybox JS expects (especially the bundle widget blocks)**
- [ ] **Step 2: Write full new template JSON matching the 29-section order, preserving all current buybox block IDs verbatim**
- [ ] **Step 3: Push only this template**

```bash
shopify theme push --theme=161862353132 --nodelete --only=templates/product.spike-detox.json --json
```

- [ ] **Step 4: Load Pure PDP via preview** — `https://gmmehe-01.myshopify.com/products/spike-detox?preview_theme_id=161862353132` and confirm all 27 template sections render.

- [ ] **Step 5: Commit + push**

```bash
git add tcc-theme/templates/product.spike-detox.json
git commit -m "feat(pdp): Pure PDP rebuild — Resilia structure mirror (27 template sections)

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
git push origin main
```

---

## Phase 4 — Visual QA + final

### Task 11: Playwright QA Complete vs TWC reference

**Files:**
- Create: `.playwright-mcp/pdp-mirror-2026-05-06/01..NN-complete-*.png`

- [ ] **Step 1: Open both URLs in parallel Playwright tabs**
  - Tab 1: `https://www.twc.health/products/ultimate-spike-detox`
  - Tab 2: `https://gmmehe-01.myshopify.com/products/spike-detox-complete?preview_theme_id=161862353132`

- [ ] **Step 2: Take full-page screenshots of both** at desktop viewport (1440×900) and mobile viewport (390×844).

- [ ] **Step 3: Compare section-by-section**. For each section in the spec map:
  - Does the section exist on Complete? (yes/no)
  - Does the layout roughly match TWC? (yes/no)
  - Note any visual regressions vs TWC (overflow, broken images, missing copy)

- [ ] **Step 4: Fix any breakage found**, push, re-screenshot. Iterate until parity.

- [ ] **Step 5: Save artifacts under `.playwright-mcp/pdp-mirror-2026-05-06/`**

- [ ] **Step 6: Commit any fixes + push**

```bash
git add .
git commit -m "polish(pdp): Complete PDP visual QA fixes vs TWC reference

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
git push origin main
```

---

### Task 12: Playwright QA Pure vs Resilia reference

Same pattern as Task 11 against `https://try.resilia.shop/rsl` and the Pure preview URL.

- [ ] **Step 1: Open both URLs**
- [ ] **Step 2: Full-page screenshots desktop + mobile**
- [ ] **Step 3: Section-by-section compare**
- [ ] **Step 4: Fix breakage**
- [ ] **Step 5: Save artifacts**
- [ ] **Step 6: Commit + push**

```bash
git commit -m "polish(pdp): Pure PDP visual QA fixes vs Resilia reference"
git push origin main
```

---

### Task 13: Update STATUS-pdp.md + final push

**Files:**
- Modify: `STATUS-pdp.md`

- [ ] **Step 1: Append new Phase 5 section to STATUS-pdp.md**:

```markdown
## Phase 5 — Mirror references (TWC + Resilia)

**Status:** complete.
**Date:** 2026-05-06.

Reference targets:
- Complete ← www.twc.health/products/ultimate-spike-detox (16 template sections; ~19 incl. header/footer groups)
- Pure ← try.resilia.shop/rsl (27 template sections; ~29 incl. header/footer groups)

User direction (2026-05-06): CONTRACT-5 audit rules relaxed; conversion-first build
with urgency tactics, named authority, video carousels, OTP/Sub radio, cross-sell
in scope. Only mandatory carve-outs: FDA disclaimer + no explicit cure claims.

Built (6 new sections): video-testimonials, cross-sell-products, image-band,
subscription-features, symptom-grid, special-offer-callout.

Buybox refactor: warning_chip / member_callout / embedded_video_carousel /
accordion_with_cross_sell block types + OTP-vs-Subscribe purchase mode + sticky-ATC
price-pair display. Per-template opt-in; Pure PDP unchanged at runtime.

Visual QA artifacts: .playwright-mcp/pdp-mirror-2026-05-06/

Resolved blocker: Complete PDP partial-render fixed as side-effect of full template
rewrite (no JSONC comment header on the new file).

Open placeholders (deferred):
- Real customer videos for video-testimonials thumbs
- Real Founder's Note headshot/quote in doctor_block
- Real cross-sell partner SKUs in cross_sell
- Okendo (or alt) reviews module install
- Real "Featured On" outlet logos
- Real ingredient infographic art for image_band_3
```

- [ ] **Step 2: Final commit + push**

```bash
git add STATUS-pdp.md
git commit -m "docs(pdp): Phase 5 status — mirror-references build complete"
git push origin main
```

- [ ] **Step 3: Push final theme state to preview** (any pending changes)

```bash
cd "/Users/jonas/Documents/IDE/Health Store/tcc-theme"
shopify theme push --theme=161862353132 --nodelete --json
```

---

## Out of scope (deferred until user supplies assets)

These are intentionally placeholder-mode in this build. No tasks should attempt to fabricate them:

- Real customer story videos (3 for Complete, 4 for Pure) — placeholder thumbs render.
- Real Founder's Note portrait + signed quote — generic block renders with no portrait.
- Real cross-sell SKUs paired with Spike Detox / Spike Detox Complete — manual-mode placeholder cards render.
- Okendo (or BAZAARVOICE / Yotpo / Stamped) reviews app — section is hidden.
- Real "Featured On" outlet logos — generic silhouettes.
- Real ingredient/process infographic art — solid CSS gradient placeholders render.

---

## Risks & mitigations (active)

- **Buybox JS regression on Pure**: addressed by per-template opt-in. Default `purchase_mode: bundle` and missing new block types = current behavior.
- **JSONC comment header on templates re-broke Complete render last time**: Tasks 9 + 10 explicitly write fresh JSON files without `/* */` headers. The earlier `2309f1b fix(pdp): strip JSONC comment header` already cleaned the live state; we maintain it.
- **CDN edge cache** can mask successful pushes for ~60s. Visual QA tasks should `Cmd-Shift-R` after every push or wait 90s.
- **Section count for Pure (29) is high** — mobile scroll length will be ~14000px. This matches Resilia's known length and is intentional. Revisit if bounce metrics suffer post-launch.
