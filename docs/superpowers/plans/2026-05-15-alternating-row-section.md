# Alternating Row Section — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ship a reusable `alternating-row` Shopify section and wire two instances ("cardio_problem" → "cardio_solution") into `templates/product.spike-detox.json` between the buybox and the subscription strip, modeled after `try.resilia.shop/rsl` rows 4–5.

**Architecture:** A single-row schema-driven Liquid section instantiated twice in the template with opposite `image_side` values. Reuses existing CSS tokens from `assets/tcc-tokens.css`. Image resolution priority: image_picker → asset filename → external URL → neutral fallback block — necessary because JSON-template-defaulted image_picker fields can't be pre-seeded from theme assets via the CLI.

**Tech Stack:** Liquid, JSON section/template schema, Shopify CLI 3.91, vanilla CSS using existing TCC design tokens.

**Spec:** `docs/superpowers/specs/2026-05-15-alternating-row-section-design.md`

---

## File Structure

**Created:**
- `tcc-theme/sections/alternating-row.liquid` — section markup, scoped CSS, schema

**Modified:**
- `tcc-theme/templates/product.spike-detox.json` — adds `cardio_problem` and `cardio_solution` section entries; inserts both into `order` array between `buybox` and `subscription`

**Read-only / reference:**
- `tcc-theme/sections/origin-story.liquid` — sibling alternating-layout pattern
- `tcc-theme/assets/tcc-tokens.css` — design tokens

---

## Pre-flight (CONTRACT-1)

Always pull from Shopify before editing the theme. Do this first; if anything came down, commit it as a `sync(theme):` commit before starting Task 1.

- [ ] **Pull latest from Shopify**

```bash
cd "/Users/jonas/Documents/IDE/TCC/tcc-theme"
shopify theme pull --theme=161862353132 --nodelete
```

- [ ] **Check for incoming changes**

```bash
cd "/Users/jonas/Documents/IDE/TCC" && git status
```

- [ ] **If incoming changes exist, commit them BEFORE editing**

```bash
cd "/Users/jonas/Documents/IDE/TCC"
git add tcc-theme/
git commit -m "sync(theme): pull from Shopify admin"
```

If nothing came down, skip the commit.

---

## Task 1: Create `alternating-row.liquid` section

**Files:**
- Create: `tcc-theme/sections/alternating-row.liquid`

- [ ] **Step 1: Create the section file**

Write `tcc-theme/sections/alternating-row.liquid` with the following exact content:

```liquid
{%- comment -%}
  Alternating Row — single-row image+text section. Instantiate multiple
  times per template with opposite `image_side` values for alternating
  layouts. See origin-story.liquid for the sibling single-row pattern.
{%- endcomment -%}

<section
  class="alternating-row tcc-section-padding alternating-row--{{ section.settings.image_side }} alternating-row--bg-{{ section.settings.background_style }}"
  data-section-id="{{ section.id }}"
>
  <div class="alternating-row__grid">
    <div class="alternating-row__visual">
      {%- if section.settings.image != blank -%}
        {{ section.settings.image
          | image_url: width: 1200
          | image_tag:
              loading: 'lazy',
              widths: '400,600,800,1000,1200',
              sizes: '(max-width: 768px) 100vw, 50vw',
              alt: section.settings.image_alt,
              class: 'alternating-row__image'
        }}
      {%- elsif section.settings.image_asset_filename != blank -%}
        <img
          src="{{ section.settings.image_asset_filename | asset_url }}"
          alt="{{ section.settings.image_alt | escape }}"
          class="alternating-row__image"
          loading="lazy"
        >
      {%- elsif section.settings.image_external_url != blank -%}
        <img
          src="{{ section.settings.image_external_url }}"
          alt="{{ section.settings.image_alt | escape }}"
          class="alternating-row__image"
          loading="lazy"
        >
      {%- else -%}
        <div class="alternating-row__image-fallback" aria-hidden="true"></div>
      {%- endif -%}
    </div>

    <div class="alternating-row__content">
      {%- if section.settings.eyebrow != blank -%}
        <p class="alternating-row__eyebrow">{{ section.settings.eyebrow }}</p>
      {%- endif -%}

      <h2 class="alternating-row__heading tcc-heading">{{ section.settings.heading }}</h2>

      {%- if section.settings.body_paragraph_1 != blank -%}
        <p class="alternating-row__body">{{ section.settings.body_paragraph_1 }}</p>
      {%- endif -%}

      {%- if section.settings.body_paragraph_2 != blank -%}
        <p class="alternating-row__body">{{ section.settings.body_paragraph_2 }}</p>
      {%- endif -%}

      {%- assign has_bullets = false -%}
      {%- if section.settings.bullet_1 != blank or section.settings.bullet_2 != blank or section.settings.bullet_3 != blank or section.settings.bullet_4 != blank -%}
        {%- assign has_bullets = true -%}
      {%- endif -%}

      {%- if has_bullets -%}
        {%- if section.settings.bullets_intro != blank -%}
          <p class="alternating-row__bullets-intro">{{ section.settings.bullets_intro }}</p>
        {%- endif -%}
        <ul class="alternating-row__bullets">
          {%- if section.settings.bullet_1 != blank -%}<li>{{ section.settings.bullet_1 }}</li>{%- endif -%}
          {%- if section.settings.bullet_2 != blank -%}<li>{{ section.settings.bullet_2 }}</li>{%- endif -%}
          {%- if section.settings.bullet_3 != blank -%}<li>{{ section.settings.bullet_3 }}</li>{%- endif -%}
          {%- if section.settings.bullet_4 != blank -%}<li>{{ section.settings.bullet_4 }}</li>{%- endif -%}
        </ul>
      {%- endif -%}

      {%- if section.settings.closing_paragraph != blank -%}
        <p class="alternating-row__body alternating-row__body--closing">{{ section.settings.closing_paragraph }}</p>
      {%- endif -%}
    </div>
  </div>
</section>

<style>
  .alternating-row {
    overflow: hidden;
  }
  .alternating-row--bg-cream { background-color: #f4f3ed; }
  .alternating-row--bg-white { background-color: #ffffff; }
  .alternating-row--bg-bg    { background-color: var(--tcc-bg); }

  .alternating-row__grid {
    display: grid;
    grid-template-columns: minmax(0, 1fr) minmax(0, 1fr);
    gap: var(--tcc-s-6);
    max-width: 1200px;
    margin: 0 auto;
    align-items: center;
  }

  .alternating-row--image_left .alternating-row__visual { order: 1; }
  .alternating-row--image_left .alternating-row__content { order: 2; }
  .alternating-row--image_right .alternating-row__visual { order: 2; }
  .alternating-row--image_right .alternating-row__content { order: 1; }

  .alternating-row__image,
  .alternating-row__image-fallback {
    width: 100%;
    height: auto;
    border-radius: var(--tcc-radius);
    display: block;
  }
  .alternating-row__image-fallback {
    aspect-ratio: 4 / 3;
    background: linear-gradient(135deg, #efeee8, #e6e5df);
  }

  .alternating-row__eyebrow {
    font-family: var(--tcc-font-sans);
    font-size: var(--tcc-fs-eyebrow);
    letter-spacing: var(--tcc-ls-eyebrow);
    text-transform: uppercase;
    font-weight: var(--tcc-fw-bold);
    color: var(--tcc-ink-soft);
    margin: 0 0 var(--tcc-s-2);
  }

  .alternating-row__heading {
    font-family: var(--tcc-font-sans);
    font-weight: var(--tcc-fw-display);
    font-size: var(--tcc-fs-h2);
    line-height: var(--tcc-lh-tight);
    letter-spacing: var(--tcc-ls-display);
    color: var(--tcc-ink);
    margin: 0 0 var(--tcc-s-3);
  }

  .alternating-row__body {
    font-family: var(--tcc-font-sans);
    font-size: var(--tcc-fs-body);
    line-height: var(--tcc-lh-body);
    color: var(--tcc-ink);
    margin: 0 0 var(--tcc-s-3);
  }
  .alternating-row__body:last-child {
    margin-bottom: 0;
  }

  .alternating-row__bullets-intro {
    font-family: var(--tcc-font-sans);
    font-size: var(--tcc-fs-body);
    line-height: var(--tcc-lh-body);
    font-weight: var(--tcc-fw-medium);
    color: var(--tcc-ink);
    margin: 0 0 var(--tcc-s-2);
  }

  .alternating-row__bullets {
    list-style: none;
    padding: 0;
    margin: 0 0 var(--tcc-s-3);
    display: flex;
    flex-direction: column;
    gap: 12px;
  }
  .alternating-row__bullets li {
    position: relative;
    padding-left: 22px;
    font-family: var(--tcc-font-sans);
    font-size: var(--tcc-fs-body);
    line-height: var(--tcc-lh-body);
    color: var(--tcc-ink);
  }
  .alternating-row__bullets li::before {
    content: "";
    position: absolute;
    left: 0;
    top: 0.55em;
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background-color: var(--tcc-positive);
  }

  @media (max-width: 768px) {
    .alternating-row__grid {
      grid-template-columns: 1fr;
      gap: var(--tcc-s-4);
    }
    .alternating-row--image_left .alternating-row__visual,
    .alternating-row--image_right .alternating-row__visual {
      order: 1;
    }
    .alternating-row--image_left .alternating-row__content,
    .alternating-row--image_right .alternating-row__content {
      order: 2;
    }
  }
</style>

{% schema %}
{
  "name": "Alternating Row",
  "tag": "section",
  "class": "section",
  "disabled_on": {
    "groups": ["header", "footer"]
  },
  "settings": [
    { "type": "text",         "id": "eyebrow",              "label": "Eyebrow (optional)" },
    { "type": "text",         "id": "heading",              "label": "Heading", "default": "Heading goes here" },
    { "type": "textarea",     "id": "body_paragraph_1",     "label": "Body paragraph 1" },
    { "type": "textarea",     "id": "body_paragraph_2",     "label": "Body paragraph 2 (optional)" },
    { "type": "text",         "id": "bullets_intro",        "label": "Bullet list intro (optional)" },
    { "type": "text",         "id": "bullet_1",             "label": "Bullet 1 (optional)" },
    { "type": "text",         "id": "bullet_2",             "label": "Bullet 2 (optional)" },
    { "type": "text",         "id": "bullet_3",             "label": "Bullet 3 (optional)" },
    { "type": "text",         "id": "bullet_4",             "label": "Bullet 4 (optional)" },
    { "type": "textarea",     "id": "closing_paragraph",    "label": "Closing paragraph (optional)" },
    { "type": "image_picker", "id": "image",                "label": "Image (preferred)" },
    { "type": "text",         "id": "image_asset_filename", "label": "Image — theme asset filename (fallback, e.g. benefit-heart.jpg)" },
    { "type": "text",         "id": "image_external_url",   "label": "Image — external URL (fallback)" },
    { "type": "text",         "id": "image_alt",            "label": "Image alt text" },
    {
      "type": "select",
      "id": "image_side",
      "label": "Image side",
      "options": [
        { "value": "image_left",  "label": "Image on left" },
        { "value": "image_right", "label": "Image on right" }
      ],
      "default": "image_right"
    },
    {
      "type": "select",
      "id": "background_style",
      "label": "Background",
      "options": [
        { "value": "cream", "label": "Cream (#f4f3ed)" },
        { "value": "white", "label": "White" },
        { "value": "bg",    "label": "Theme off-white (--tcc-bg)" }
      ],
      "default": "cream"
    }
  ],
  "presets": [
    { "name": "Alternating Row" }
  ]
}
{% endschema %}
```

- [ ] **Step 2: Theme-check the new file**

Run from `/Users/jonas/Documents/IDE/TCC/tcc-theme`:

```bash
shopify theme check --path . 2>&1 | grep -E "alternating-row|^[0-9]+ errors?" | head -20
```

Expected: no errors specifically mentioning `alternating-row.liquid`. Warnings about other unrelated files are fine. If you see a parse error in `alternating-row.liquid`, fix it before continuing.

If `shopify theme check` isn't available in the environment, validate JSON-in-schema with:

```bash
python3 -c "import json,re,sys; f=open('sections/alternating-row.liquid').read(); m=re.search(r'\\{% schema %\\}(.*?)\\{% endschema %\\}', f, re.S); json.loads(m.group(1)); print('schema-json: ok')"
```

Expected: `schema-json: ok`

- [ ] **Step 3: Commit the section file alone**

```bash
cd "/Users/jonas/Documents/IDE/TCC"
git add tcc-theme/sections/alternating-row.liquid
git commit -m "feat(theme): add alternating-row section for image+text rows"
```

---

## Task 2: Wire two instances into `product.spike-detox.json`

**Files:**
- Modify: `tcc-theme/templates/product.spike-detox.json`

- [ ] **Step 1: Verify current `order` array position**

```bash
cd "/Users/jonas/Documents/IDE/TCC/tcc-theme"
grep -n "^\s*\"\(media\|buybox\|subscription\|guarantee\)\"" templates/product.spike-detox.json
```

Expected (lines may differ slightly): the `order` array contains the entries `"media"`, `"buybox"`, `"subscription"`, `"guarantee"` consecutively. If those four lines do NOT appear in that order in the bottom `order` array, STOP — the template has drifted from the spec and the insertion point needs re-validation.

- [ ] **Step 2: Add the two section entries**

In `tcc-theme/templates/product.spike-detox.json`, find the `"subscription":` section entry (around line 111) and insert two new sibling entries **before** it. The new entries must be JSON-comma-separated siblings of existing entries inside the top-level `"sections":` object.

Add this JSON block immediately before the `"subscription":` key:

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
    },
```

Make sure the trailing comma is present so JSON stays valid (the next entry, `"subscription":`, follows).

- [ ] **Step 3: Update the `order` array**

In the same file, find the `"order": [` array near the bottom and insert `"cardio_problem"` and `"cardio_solution"` between `"buybox"` and `"subscription"`. The result should look like:

```jsonc
  "order": [
    "media",
    "buybox",
    "cardio_problem",
    "cardio_solution",
    "subscription",
    "guarantee",
    ...
  ]
```

- [ ] **Step 4: Validate JSON**

```bash
cd "/Users/jonas/Documents/IDE/TCC/tcc-theme"
python3 -m json.tool templates/product.spike-detox.json > /dev/null && echo "json: ok"
```

Expected: `json: ok`. If you see a parse error, fix it before continuing.

- [ ] **Step 5: Confirm `order` array contains both new keys**

```bash
cd "/Users/jonas/Documents/IDE/TCC/tcc-theme"
python3 -c "import json; d=json.load(open('templates/product.spike-detox.json')); o=d['order']; i=o.index('buybox'); print(o[i:i+5])"
```

Expected: `['buybox', 'cardio_problem', 'cardio_solution', 'subscription', 'guarantee']`

- [ ] **Step 6: Commit the template wiring**

```bash
cd "/Users/jonas/Documents/IDE/TCC"
git add tcc-theme/templates/product.spike-detox.json
git commit -m "feat(pdp): add cardio problem→solution alternating rows above subscription"
```

---

## Task 3: Push to Shopify and visually verify

**CONTRACT-2:** The target theme `161862353132` is **live**. You MUST ask the user for explicit authorization in the same conversation before pushing. If they decline, stop after Step 1 (the changes are committed locally; nothing has shipped yet).

- [ ] **Step 1: Ask for live-push authorization**

Ask the user: *"Ready to push the alternating-row section + template wiring to live theme `161862353132`. Confirm authorization for the live push?"*

Wait for their explicit yes. If they prefer to push to an unpublished preview theme first (e.g., `162026717420` "Preview 2026-05-11"), substitute that ID in the next step.

- [ ] **Step 2: Push the two files to live**

```bash
cd "/Users/jonas/Documents/IDE/TCC/tcc-theme"
shopify theme push \
  --theme=161862353132 \
  --nodelete \
  --live --allow-live \
  --only=sections/alternating-row.liquid \
  --only=templates/product.spike-detox.json
```

Expected: `The theme 'Spike Detox v1 Preview' (#161862353132) was pushed successfully.`

- [ ] **Step 3: Open the PDP in a browser**

Open: `https://gmmehe-01.myshopify.com/products/spike-detox?preview_theme_id=161862353132`

Manually verify:
- Two new sections appear immediately after the buybox and before the "Auto-refill every 30 days" subscription strip.
- Row 1 ("Modern Life Is Hard…") has text on the **left**, image (heart illustration) on the **right**.
- Row 2 ("Nattokinase: A Centuries-Old Enzyme…") has image (product bottle) on the **left**, text on the **right**, with 4 green-dot bullet items.
- Both rows have a cream `#f4f3ed` background.
- Resize the browser to <768px — both rows should stack to single-column, image on top, text below.

- [ ] **Step 4: Capture verification screenshots**

If Playwright MCP is available, capture and save full-page screenshots at desktop (1280×800) and mobile (390×844) viewports to:
- `/Users/jonas/Documents/IDE/TCC/alternating-row-desktop-2026-05-15.png`
- `/Users/jonas/Documents/IDE/TCC/alternating-row-mobile-2026-05-15.png`

If anything looks broken (overflowing text, image not loading, wrong background, wrong order, broken mobile stack), STOP and report what's wrong before continuing. Common fixes:
- Image not loading → check that `tcc-theme/assets/benefit-heart.jpg` and `tcc-theme/assets/hero-product-main.jpg` exist; if missing, swap `image_asset_filename` in the template to an existing filename from `ls tcc-theme/assets/*.jpg`.
- Wrong side after push → confirm `image_side` values match the spec (Row 1 = `image_right`, Row 2 = `image_left`).
- Mobile not stacking → check `.alternating-row__grid` media query at 768px in the section file.

- [ ] **Step 5: Push to GitHub (CONTRACT-3)**

```bash
cd "/Users/jonas/Documents/IDE/TCC"
git push origin main
```

Expected: `main -> main` with the two new commits from Tasks 1 and 2 (and any sync commit from pre-flight).

---

## Done When

- [ ] `sections/alternating-row.liquid` exists, parses, and renders.
- [ ] `templates/product.spike-detox.json` includes `cardio_problem` and `cardio_solution`, slotted between `buybox` and `subscription` in `order`.
- [ ] Pushed to live theme `161862353132` (with explicit user authorization).
- [ ] Visually verified at the preview URL: two rows appear above the subscription strip, alternate sides, stack on mobile, use cream background.
- [ ] Verification screenshots committed (optional — only if Playwright was used).
- [ ] GitHub `origin/main` is up to date.
