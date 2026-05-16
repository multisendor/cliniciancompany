# One-Ingredient Split Section — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ship a new custom Shopify section `one-ingredient-split` to the Spike Detox PDP, slotted under `trial_stats`, mirroring the visual style, spacing, and topic structure of Resilia's "One Ingredient. Endless Immune Support." section on `try.resilia.shop/rsl`.

**Architecture:** Single section file (`tcc-theme/sections/one-ingredient-split.liquid`) with inline `<style>` block, matching the per-section CSS pattern used by `outcome-stats.liquid` and `alternating-row.liquid`. Two-column grid on desktop, single-column with restyled callouts on mobile. Dotted leader lines drawn via inline SVG overlay (desktop only). Section instance + prefilled content added to `templates/product.spike-detox.json`. No external CSS file, no JS.

**Tech Stack:** Shopify Liquid, vanilla CSS (using existing `--tcc-*` design tokens), inline SVG. Verified via Shopify CLI push and Playwright MCP screenshots at desktop (1280×900) and mobile (390×844) viewports.

**Spec:** `docs/superpowers/specs/2026-05-15-one-ingredient-split-section-design.md`

**Theme target:** `161862353132` ("Spike Detox v1 Preview" — currently LIVE per CLAUDE.md). All pushes go here. The user has authorized this section's work in the spec-approval conversation.

---

## Hard prerequisites (read before Task 1)

- **CONTRACT-1.** Before any edit, `cd /Users/jonas/Documents/IDE/TCC/tcc-theme && shopify theme pull --theme=161862353132 --nodelete`. If anything came down, commit it as `sync(theme): pull from Shopify admin` BEFORE your own edits.
- **CONTRACT-2.** Push to the same theme after edits.
- **CONTRACT-3.** `git push origin main` after each meaningful commit.
- **CONTRACT-5.** The PDP audit rules are bound — the copy in this plan has already been audited (spec §11). Do not regress: no "pharmaceutical-grade", no FLCCC/McCullough/IMA, no RN/MD credentials, no urgency tactics, structure-function language only.
- **Coordination.** A separate background agent may still be running for an unrelated buy-box CTA-toggle change. Before pushing in any task, run `git fetch origin && git pull --rebase origin main`. If a rebase conflict appears, resolve it (changes here are scoped to `tcc-theme/sections/one-ingredient-split.liquid` and `tcc-theme/templates/product.spike-detox.json` — these should not collide with buy-box work).

## File structure

**New file:**
- `tcc-theme/sections/one-ingredient-split.liquid` — single source for markup, schema, and styles

**Modified file:**
- `tcc-theme/templates/product.spike-detox.json` — add `one_ingredient_split` section key with full content + 12 blocks; slot into `order` after `trial_stats`

That's it. No snippet files, no asset uploads in v1 (placeholder for the card image renders via CSS).

---

## Task 1: Scaffold the section file with schema + minimal markup

**Files:**
- Create: `tcc-theme/sections/one-ingredient-split.liquid`

This task gets the section registered in Shopify with all its settings and blocks defined, but only renders bare content (no styling yet). After pushing, it should appear as plain unstyled text + an empty card placeholder on the PDP.

- [ ] **Step 1: Pull the theme (CONTRACT-1)**

```bash
cd /Users/jonas/Documents/IDE/TCC/tcc-theme
shopify theme pull --theme=161862353132 --nodelete
git status --short
```

If `git status` shows incoming changes, commit them as a sync first:

```bash
git add -A
git commit -m "sync(theme): pull from Shopify admin"
```

- [ ] **Step 2: Create the section file**

Create `tcc-theme/sections/one-ingredient-split.liquid` with this exact content:

```liquid
<div
  class="one-ingredient-split tcc-section-padding one-ingredient-split--{{ section.settings.background_style }}"
  data-section-type="one-ingredient-split"
  data-section-id="{{ section.id }}"
  style="--ois-card-bg: {{ section.settings.card_bg_color }}; --ois-pad-top: {{ section.settings.padding_top }}px; --ois-pad-bot: {{ section.settings.padding_bottom }}px;"
>
  <div class="tcc-page-width">
    <div class="one-ingredient-split__grid">
      {%- comment -%} ============ LEFT COLUMN (text) ============ {%- endcomment -%}
      <div class="one-ingredient-split__text">
        {%- if section.settings.eyebrow != blank -%}
          <p class="one-ingredient-split__eyebrow tcc-eyebrow">{{ section.settings.eyebrow }}</p>
        {%- endif -%}

        {%- if section.settings.heading != blank -%}
          <h2 class="one-ingredient-split__heading">{{ section.settings.heading }}</h2>
        {%- endif -%}

        {%- if section.settings.intro_paragraph_1 != blank -%}
          <div class="one-ingredient-split__intro rte">{{ section.settings.intro_paragraph_1 }}</div>
        {%- endif -%}

        {%- if section.settings.intro_paragraph_2 != blank -%}
          <div class="one-ingredient-split__intro rte">{{ section.settings.intro_paragraph_2 }}</div>
        {%- endif -%}

        {%- assign list_a = section.blocks | where: "type", "list_a_item" -%}
        {%- if list_a.size > 0 -%}
          <p class="one-ingredient-split__list-heading"><strong>{{ section.settings.list_a_heading }}:</strong></p>
          <ul class="one-ingredient-split__list">
            {%- for block in list_a -%}
              <li {{ block.shopify_attributes }}>{{ block.settings.text }}</li>
            {%- endfor -%}
          </ul>
        {%- endif -%}

        {%- assign list_b = section.blocks | where: "type", "list_b_item" -%}
        {%- if list_b.size > 0 -%}
          <p class="one-ingredient-split__list-heading"><strong>{{ section.settings.list_b_heading }}:</strong></p>
          <ul class="one-ingredient-split__list">
            {%- for block in list_b -%}
              <li {{ block.shopify_attributes }}>{{ block.settings.text }}</li>
            {%- endfor -%}
          </ul>
        {%- endif -%}

        {%- if section.settings.closing_paragraph != blank -%}
          <div class="one-ingredient-split__closing rte">{{ section.settings.closing_paragraph }}</div>
        {%- endif -%}
      </div>

      {%- comment -%} ============ RIGHT COLUMN (visual card) ============ {%- endcomment -%}
      <div class="one-ingredient-split__card-wrap">
        <figure class="one-ingredient-split__card">
          <div class="one-ingredient-split__figure">
            {%- if section.settings.card_image != blank -%}
              {{ section.settings.card_image | image_url: width: 800 | image_tag:
                  loading: 'lazy',
                  widths: '320, 480, 640, 800',
                  alt: section.settings.card_image_alt,
                  class: 'one-ingredient-split__figure-img' }}
            {%- else -%}
              {%- comment -%} CSS-rendered placeholder cluster (5 amber pills) {%- endcomment -%}
              <div class="one-ingredient-split__figure-placeholder" aria-label="{{ section.settings.card_image_alt }}">
                <span></span><span></span><span></span><span></span><span></span>
              </div>
            {%- endif -%}
          </div>

          {%- assign callouts = section.blocks | where: "type", "callout" -%}
          {%- if callouts.size > 0 -%}
            <div class="one-ingredient-split__callouts">
              {%- for block in callouts -%}
                <div
                  class="one-ingredient-split__callout one-ingredient-split__callout--{{ block.settings.position }}"
                  {{ block.shopify_attributes }}
                >
                  <span class="one-ingredient-split__callout-icon" data-icon="{{ block.settings.icon }}">
                    {%- render 'one-ingredient-icon', icon: block.settings.icon -%}
                  </span>
                  <span class="one-ingredient-split__callout-label">
                    <span class="one-ingredient-split__callout-line1">{{ block.settings.label_line_1 }}</span>
                    {%- if block.settings.label_line_2 != blank -%}
                      <span class="one-ingredient-split__callout-line2">{{ block.settings.label_line_2 }}</span>
                    {%- endif -%}
                  </span>
                </div>
              {%- endfor -%}
            </div>

            {%- comment -%} Dotted leader lines (desktop-only via CSS) — drawn from each callout's anchor dot to a focal point on the figure. {%- endcomment -%}
            <svg class="one-ingredient-split__leaders" viewBox="0 0 100 100" preserveAspectRatio="none" aria-hidden="true">
              {%- for block in callouts -%}
                {%- assign pos = block.settings.position -%}
                {%- case pos -%}
                  {%- when 'top_left' -%}     {%- assign x1 = 8 -%}{%- assign y1 = 18 -%}
                  {%- when 'mid_left' -%}     {%- assign x1 = 6 -%}{%- assign y1 = 52 -%}
                  {%- when 'mid_right' -%}    {%- assign x1 = 94 -%}{%- assign y1 = 52 -%}
                  {%- when 'bottom_left' -%}  {%- assign x1 = 10 -%}{%- assign y1 = 88 -%}
                  {%- when 'bottom_right' -%} {%- assign x1 = 90 -%}{%- assign y1 = 88 -%}
                {%- endcase -%}
                <line x1="{{ x1 }}" y1="{{ y1 }}" x2="50" y2="50" stroke="#ffffff" stroke-width="0.25" stroke-dasharray="0.6 1.2" opacity="0.65" />
              {%- endfor -%}
            </svg>
          {%- endif -%}
        </figure>
      </div>
    </div>
  </div>
</div>

<style>
  /* Minimal layout for Task 1 — full styling lands in Tasks 4–7. */
  [data-section-type="one-ingredient-split"] {
    padding-top: var(--ois-pad-top, 80px);
    padding-bottom: var(--ois-pad-bot, 80px);
  }
  .one-ingredient-split--cream { background: var(--tcc-bg, #f6f4ef); color: var(--tcc-ink, #0a1628); }
  .one-ingredient-split--white { background: #ffffff; color: var(--tcc-ink, #0a1628); }
  .one-ingredient-split__grid { display: block; }
</style>

{%- comment -%} ============================================================
  Inline icon snippet rendered inline — see render call above.
  Owns a single SVG glyph keyed on the `icon` argument. Kept inline (rather
  than a separate snippet file) to keep this section self-contained for v1.
  Task 6 will move icons into a real snippet if the set grows.
============================================================ {%- endcomment -%}

{% schema %}
{
  "name": "One-Ingredient Split",
  "tag": "section",
  "class": "section",
  "disabled_on": { "groups": ["header", "footer"] },
  "settings": [
    { "type": "header", "content": "Content" },
    { "type": "text",      "id": "eyebrow",            "label": "Eyebrow (optional)", "default": "" },
    { "type": "text",      "id": "heading",            "label": "Heading", "default": "One Ingredient. Centuries of Use." },
    { "type": "richtext",  "id": "intro_paragraph_1",  "label": "Intro paragraph 1" },
    { "type": "richtext",  "id": "intro_paragraph_2",  "label": "Intro paragraph 2 (optional)" },
    { "type": "text",      "id": "list_a_heading",     "label": "List A sub-head", "default": "How It Works" },
    { "type": "text",      "id": "list_b_heading",     "label": "List B sub-head", "default": "Natural Benefits" },
    { "type": "richtext",  "id": "closing_paragraph",  "label": "Closing paragraph (optional)" },

    { "type": "header", "content": "Visual card" },
    { "type": "image_picker", "id": "card_image",      "label": "Card image (capsule cluster)" },
    { "type": "text",         "id": "card_image_alt",  "label": "Card image alt text", "default": "Nattokinase capsules" },
    { "type": "color",        "id": "card_bg_color",   "label": "Card background color", "default": "#0F3F2E" },

    { "type": "header", "content": "Layout" },
    { "type": "select", "id": "background_style", "label": "Section background",
      "options": [
        { "value": "cream", "label": "Cream" },
        { "value": "white", "label": "White" }
      ], "default": "cream" },
    { "type": "range", "id": "padding_top",    "label": "Padding top (desktop)",    "min": 0, "max": 160, "step": 4, "default": 80, "unit": "px" },
    { "type": "range", "id": "padding_bottom", "label": "Padding bottom (desktop)", "min": 0, "max": 160, "step": 4, "default": 80, "unit": "px" }
  ],
  "blocks": [
    { "type": "list_a_item", "name": "List A item (How It Works)", "limit": 3,
      "settings": [ { "type": "text", "id": "text", "label": "Bullet text" } ] },
    { "type": "list_b_item", "name": "List B item (Natural Benefits)", "limit": 4,
      "settings": [ { "type": "text", "id": "text", "label": "Bullet text" } ] },
    { "type": "callout", "name": "Visual callout", "limit": 5,
      "settings": [
        { "type": "select", "id": "icon", "label": "Icon",
          "options": [
            { "value": "capsule",  "label": "Capsule" },
            { "value": "leaf",     "label": "Leaf / herb" },
            { "value": "shield",   "label": "Shield (tested)" },
            { "value": "beaker",   "label": "Beaker / lab" },
            { "value": "no_k2",    "label": "K2-free badge" },
            { "value": "sparkle",  "label": "Sparkle (single)" },
            { "value": "molecule", "label": "Molecule / enzyme" }
          ], "default": "capsule" },
        { "type": "text", "id": "label_line_1", "label": "Label line 1" },
        { "type": "text", "id": "label_line_2", "label": "Label line 2 (optional)" },
        { "type": "select", "id": "position", "label": "Position",
          "options": [
            { "value": "top_left",     "label": "Top-left" },
            { "value": "mid_left",     "label": "Middle-left" },
            { "value": "mid_right",    "label": "Middle-right" },
            { "value": "bottom_left",  "label": "Bottom-left" },
            { "value": "bottom_right", "label": "Bottom-right" }
          ], "default": "top_left" }
      ] }
  ],
  "presets": [ { "name": "One-Ingredient Split" } ]
}
{% endschema %}
```

- [ ] **Step 3: Create the icon snippet**

Create `tcc-theme/snippets/one-ingredient-icon.liquid` with this exact content:

```liquid
{%- comment -%}
  One-ingredient-split icon glyphs. Accepts `icon` (string).
  All glyphs are 24×24, stroke 1.5, currentColor.
{%- endcomment -%}
{%- case icon -%}
  {%- when 'capsule' -%}
    <svg viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M14.5 3.5l6 6a4.95 4.95 0 0 1-7 7l-6-6a4.95 4.95 0 0 1 7-7z"/><line x1="8.5" y1="8.5" x2="15.5" y2="15.5"/></svg>
  {%- when 'leaf' -%}
    <svg viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M11 20A7 7 0 0 1 4 13c0-4 4-9 9-9 1.5 0 3 .3 3 .3s.3 1.5.3 3c0 5-5 9-9 9z"/><path d="M2 22s8-2 13-7"/></svg>
  {%- when 'shield' -%}
    <svg viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M12 2l8 4v6c0 5-3.5 8.5-8 10-4.5-1.5-8-5-8-10V6l8-4z"/><polyline points="9,12 11,14 15,10"/></svg>
  {%- when 'beaker' -%}
    <svg viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M9 3h6v5l4 9a2 2 0 0 1-1.8 2.9H6.8A2 2 0 0 1 5 17l4-9V3z"/><line x1="9" y1="3" x2="15" y2="3"/></svg>
  {%- when 'no_k2' -%}
    <svg viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><circle cx="12" cy="12" r="9"/><line x1="5.6" y1="5.6" x2="18.4" y2="18.4"/><text x="12" y="15" text-anchor="middle" font-family="system-ui" font-size="9" font-weight="700" fill="currentColor" stroke="none">K2</text></svg>
  {%- when 'sparkle' -%}
    <svg viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M12 3l1.8 5.2L19 10l-5.2 1.8L12 17l-1.8-5.2L5 10l5.2-1.8L12 3z"/></svg>
  {%- when 'molecule' -%}
    <svg viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><circle cx="6" cy="6" r="2"/><circle cx="18" cy="6" r="2"/><circle cx="6" cy="18" r="2"/><circle cx="18" cy="18" r="2"/><circle cx="12" cy="12" r="2"/><line x1="7.5" y1="7.5" x2="10.5" y2="10.5"/><line x1="16.5" y1="7.5" x2="13.5" y2="10.5"/><line x1="7.5" y1="16.5" x2="10.5" y2="13.5"/><line x1="16.5" y1="16.5" x2="13.5" y2="13.5"/></svg>
{%- endcase -%}
```

- [ ] **Step 4: Commit the scaffold**

```bash
cd /Users/jonas/Documents/IDE/TCC
git add tcc-theme/sections/one-ingredient-split.liquid tcc-theme/snippets/one-ingredient-icon.liquid
git commit -m "feat(pdp): scaffold one-ingredient-split section (markup + schema, unstyled)"
```

Do NOT push yet — Task 3 pushes after the template instance is wired up.

---

## Task 2: Wire the section into `product.spike-detox.json`

**Files:**
- Modify: `tcc-theme/templates/product.spike-detox.json`

Add a `one_ingredient_split` section key with all content prefilled (heading, two intro paragraphs, list sub-heads, closing) plus 12 blocks: 3 `list_a_item`, 4 `list_b_item`, 5 `callout`. Slot into `order` immediately after `trial_stats`.

- [ ] **Step 1: Read the file**

Open `tcc-theme/templates/product.spike-detox.json` and locate (a) the `"sections"` object's closing brace before `"order"`, and (b) the `"order"` array.

- [ ] **Step 2: Add the section definition**

Inside the `"sections"` object, after the `"trial_stats"` definition and before the next section (currently `"one_ingredient"`), insert:

```json
    "one_ingredient_split": {
      "type": "one-ingredient-split",
      "blocks": {
        "la1": {
          "type": "list_a_item",
          "settings": { "text": "Serine protease activity acts directly on fibrin to support healthy blood-flow regulation" }
        },
        "la2": {
          "type": "list_a_item",
          "settings": { "text": "Fibrinolytic action helps the body maintain natural clot-clearance balance" }
        },
        "la3": {
          "type": "list_a_item",
          "settings": { "text": "Wild Bacillus subtilis fermentation delivers the same traditional natto enzyme studied since 1980" }
        },
        "lb1": {
          "type": "list_b_item",
          "settings": { "text": "Supports cardiovascular and circulatory health" }
        },
        "lb2": {
          "type": "list_b_item",
          "settings": { "text": "Helps maintain healthy fibrinolytic balance" }
        },
        "lb3": {
          "type": "list_b_item",
          "settings": { "text": "Promotes steady, sustained daily energy" }
        },
        "lb4": {
          "type": "list_b_item",
          "settings": { "text": "Built K2-free for compatibility with common blood thinners" }
        },
        "c1": {
          "type": "callout",
          "settings": { "icon": "capsule",  "label_line_1": "4,000 FU",          "label_line_2": "per capsule",                  "position": "top_left" }
        },
        "c2": {
          "type": "callout",
          "settings": { "icon": "leaf",     "label_line_1": "Wild-fermented",    "label_line_2": "Bacillus subtilis",            "position": "mid_left" }
        },
        "c3": {
          "type": "callout",
          "settings": { "icon": "shield",   "label_line_1": "Third-party tested","label_line_2": "every batch",                  "position": "mid_right" }
        },
        "c4": {
          "type": "callout",
          "settings": { "icon": "no_k2",    "label_line_1": "K2-free for",       "label_line_2": "blood-thinner users",          "position": "bottom_left" }
        },
        "c5": {
          "type": "callout",
          "settings": { "icon": "sparkle",  "label_line_1": "Single ingredient", "label_line_2": "no proprietary blend",         "position": "bottom_right" }
        }
      },
      "block_order": [ "la1","la2","la3","lb1","lb2","lb3","lb4","c1","c2","c3","c4","c5" ],
      "settings": {
        "eyebrow": "",
        "heading": "One Ingredient. Centuries of Use.",
        "intro_paragraph_1": "<p>Unlike most nattokinase supplements built around proprietary blends, undisclosed K2, or 2,000-FU regulatory minimums, Pure keeps it simple — featuring just one studied, time-tested enzyme: wild-fermented nattokinase from traditional Bacillus subtilis. From its serine-protease structure to its decades-deep cardiovascular research base, nattokinase offers a clean, single-ingredient way to support healthy circulation, fibrinolytic balance, and daily resilience.</p>",
        "intro_paragraph_2": "<p>Here's what makes Pure so powerful in every serving:</p>",
        "list_a_heading": "How It Works",
        "list_b_heading": "Natural Benefits",
        "closing_paragraph": "<p>With Pure, you're giving your body one of nature's most-studied enzymes — trusted for centuries in traditional Japanese natto and validated by 40+ years of peer-reviewed cardiovascular research.</p>",
        "card_image_alt": "Nattokinase capsules",
        "card_bg_color": "#0F3F2E",
        "background_style": "cream",
        "padding_top": 80,
        "padding_bottom": 80
      }
    },
```

Make sure the trailing comma is correct and the comma after `"trial_stats": { ... }` is preserved.

- [ ] **Step 3: Update the `order` array**

Find the `"order"` array. Locate the line `"trial_stats",` and insert `"one_ingredient_split",` immediately after it. The relevant slice should now read:

```json
    "trial_stats",
    "one_ingredient_split",
    "subscription",
```

- [ ] **Step 4: Validate the JSON**

```bash
cd /Users/jonas/Documents/IDE/TCC
python3 -c "
import re, json
src = open('tcc-theme/templates/product.spike-detox.json').read()
# Strip leading block comment only (file uses /* ... */ header, no inline // comments inside JSON proper)
src = re.sub(r'\\A/\\*.*?\\*/', '', src, count=1, flags=re.DOTALL).lstrip()
d = json.loads(src)
assert 'one_ingredient_split' in d['sections'], 'section missing'
assert d['sections']['one_ingredient_split']['type'] == 'one-ingredient-split', 'wrong type'
assert 'one_ingredient_split' in d['order'], 'order missing'
i_trial = d['order'].index('trial_stats')
i_new   = d['order'].index('one_ingredient_split')
assert i_new == i_trial + 1, f'order wrong: trial_stats={i_trial}, one_ingredient_split={i_new}'
print('OK — JSON valid, section present, order correct')
"
```

Expected output: `OK — JSON valid, section present, order correct`. If it errors, fix the JSON and re-run.

- [ ] **Step 5: Commit**

```bash
git add tcc-theme/templates/product.spike-detox.json
git commit -m "feat(pdp): instance one_ingredient_split under trial_stats with full content"
```

---

## Task 3: Push & verify the unstyled section renders

After Task 1 + 2, the section should render on the live PDP as plain unstyled text + a placeholder card. This task confirms the schema parses, the blocks render, and the section sits in the right position.

- [ ] **Step 1: Pull-rebase to fold in any parallel agent's work, then push to Shopify**

```bash
cd /Users/jonas/Documents/IDE/TCC
git fetch origin
git pull --rebase origin main
cd tcc-theme
shopify theme push --theme=161862353132 --nodelete --json
```

If `git pull --rebase` reports conflicts, resolve them (this task only touches `sections/one-ingredient-split.liquid`, `snippets/one-ingredient-icon.liquid`, and `templates/product.spike-detox.json` — conflicts in any other file are not yours; favor the incoming change).

- [ ] **Step 2: Screenshot the section in place**

Use Playwright MCP to navigate to:
```
https://gmmehe-01.myshopify.com/products/spike-detox?preview_theme_id=161862353132
```
At viewport 1280×900, scroll until the "Real Outcomes from the Largest Nattokinase Trial" stat strip is visible, then scroll one viewport further. Take a screenshot and save to `/Users/jonas/Documents/IDE/TCC/task-ois-task3-unstyled-desktop.png`.

- [ ] **Step 3: Verify the section appears**

Open the screenshot. Confirm:
- The heading "One Ingredient. Centuries of Use." is visible directly under the trial_stats footnote
- Two paragraphs of intro copy render below it
- Two lists with sub-heads "How It Works" and "Natural Benefits" render with bullets
- A placeholder card area exists on the right (CSS placeholder; no real image yet)
- 5 callout text snippets render somewhere (likely stacked, unpositioned)

If any of those are missing, the schema or template didn't parse — re-read both files, fix, re-push.

- [ ] **Step 4: Commit & push to GitHub**

```bash
cd /Users/jonas/Documents/IDE/TCC
git push origin main
```

---

## Task 4: Style the left column (heading, intro, lists, closing)

**Files:**
- Modify: `tcc-theme/sections/one-ingredient-split.liquid` — replace the minimal `<style>` block with the left-column styles below.

Inherits Resilia typography pushed in `9ac9122`: section h2 30/36 wt 700 desktop, body 16/24 wt 400, all letter-spacing normal.

- [ ] **Step 1: Replace the `<style>` block**

In `tcc-theme/sections/one-ingredient-split.liquid`, find the existing `<style>` block (the minimal one from Task 1) and replace it with:

```html
<style>
  [data-section-type="one-ingredient-split"] {
    padding-top: var(--ois-pad-top, 80px);
    padding-bottom: var(--ois-pad-bot, 80px);
  }
  .one-ingredient-split--cream { background: var(--tcc-bg, #f6f4ef); color: var(--tcc-ink, #0a1628); }
  .one-ingredient-split--white { background: #ffffff;                  color: var(--tcc-ink, #0a1628); }

  /* ===== Grid ===== */
  .one-ingredient-split__grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: clamp(40px, 6vw, 80px);
    align-items: center;
  }
  @media (max-width: 749px) {
    .one-ingredient-split__grid { grid-template-columns: 1fr; gap: 32px; }
  }

  /* ===== Left column typography (mirrors Resilia / commit 9ac9122) ===== */
  .one-ingredient-split__eyebrow {
    font-family: var(--tcc-font-sans);
    font-size: var(--tcc-fs-eyebrow, 11px);
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 1.5px;
    color: var(--tcc-ink-soft);
    margin: 0 0 var(--tcc-s-2, 16px);
  }
  .one-ingredient-split__heading {
    font-family: var(--tcc-font-sans);
    font-size: 30px;
    line-height: 36px;
    font-weight: 700;
    letter-spacing: normal;
    margin: 0 0 24px;
    color: inherit;
  }
  @media (max-width: 749px) {
    .one-ingredient-split__heading { font-size: 26.6px; line-height: 31.92px; }
  }

  .one-ingredient-split__intro,
  .one-ingredient-split__closing {
    font-family: var(--tcc-font-sans);
    font-size: 16px;
    line-height: 24px;
    font-weight: 400;
    letter-spacing: normal;
    margin: 0 0 16px;
  }
  @media (max-width: 749px) {
    .one-ingredient-split__intro,
    .one-ingredient-split__closing { font-size: 15.6px; line-height: 23.4px; }
  }
  .one-ingredient-split__intro p,
  .one-ingredient-split__closing p { margin: 0 0 16px; }
  .one-ingredient-split__intro p:last-child,
  .one-ingredient-split__closing p:last-child { margin-bottom: 0; }

  .one-ingredient-split__closing { margin-top: 24px; }

  .one-ingredient-split__list-heading {
    font-family: var(--tcc-font-sans);
    font-size: 16px;
    line-height: 24px;
    font-weight: 400;
    margin: 24px 0 8px;
    letter-spacing: normal;
  }
  .one-ingredient-split__list-heading strong { font-weight: 700; }

  .one-ingredient-split__list {
    list-style: none;
    padding: 0;
    margin: 0 0 8px;
  }
  .one-ingredient-split__list li {
    position: relative;
    padding-left: 22px;
    font-family: var(--tcc-font-sans);
    font-size: 16px;
    line-height: 24px;
    font-weight: 400;
    letter-spacing: normal;
    margin: 0 0 10px;
  }
  @media (max-width: 749px) {
    .one-ingredient-split__list li { font-size: 15.6px; line-height: 23.4px; }
  }
  .one-ingredient-split__list li::before {
    content: "";
    position: absolute;
    left: 4px;
    top: 9px;
    width: 8px;
    height: 8px;
    background: #2a8458; /* Resilia green dot */
    border-radius: 50%;
  }

  /* ===== Right column (placeholder card; full card styling in Task 5) ===== */
  .one-ingredient-split__card-wrap { width: 100%; }
  .one-ingredient-split__card {
    margin: 0;
    aspect-ratio: 1 / 1;
    background: var(--ois-card-bg, #0F3F2E);
    border-radius: 24px;
    position: relative;
    overflow: hidden;
  }
</style>
```

- [ ] **Step 2: Pull-rebase + push to Shopify**

```bash
cd /Users/jonas/Documents/IDE/TCC
git add tcc-theme/sections/one-ingredient-split.liquid
git commit -m "style(pdp): one-ingredient-split left column — Resilia typography + green-dot bullets"
git fetch origin && git pull --rebase origin main
cd tcc-theme
shopify theme push --theme=161862353132 --nodelete --json
```

- [ ] **Step 3: Screenshot & verify**

Navigate Playwright to the spike-detox PDP, viewport 1280×900, scroll to the section. Save screenshot as `/Users/jonas/Documents/IDE/TCC/task-ois-task4-left-col-desktop.png`. Compare to Resilia's left column on `https://try.resilia.shop/rsl`:

- Heading reads at ~30/36 weight 700
- Body paragraphs read at 16/24 weight 400
- "How It Works:" and "Natural Benefits:" sub-heads are bold inline with colon
- Bullets show green dots, 8px, with comfortable vertical rhythm
- The right column is a plain dark-green square card (no callouts visible yet — they're in the DOM but unpositioned)

If any spacing reads tighter/looser than Resilia, adjust the `margin` values in the style block and re-push.

- [ ] **Step 4: Push to GitHub**

```bash
cd /Users/jonas/Documents/IDE/TCC
git push origin main
```

---

## Task 5: Build the card visual (image / CSS placeholder centered)

**Files:**
- Modify: `tcc-theme/sections/one-ingredient-split.liquid` — extend the `<style>` block with `.one-ingredient-split__figure` rules and the CSS placeholder.

- [ ] **Step 1: Append card styles to the `<style>` block**

Append the following inside the existing `<style>` block (just before its closing tag):

```css
  /* ===== Card figure ===== */
  .one-ingredient-split__card {
    padding: 48px;
  }
  @media (max-width: 749px) {
    .one-ingredient-split__card { padding: 24px; }
  }
  .one-ingredient-split__figure {
    position: absolute;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1;
  }
  .one-ingredient-split__figure-img {
    max-width: 60%;
    max-height: 60%;
    object-fit: contain;
    display: block;
  }

  /* CSS-rendered placeholder — 5 amber capsule pills clustered. */
  .one-ingredient-split__figure-placeholder {
    position: relative;
    width: 220px;
    height: 220px;
    display: grid;
    grid-template-columns: 60px 60px 60px;
    grid-template-rows: 60px 60px;
    gap: 8px;
    align-content: center;
    justify-content: center;
    transform: rotate(-12deg);
  }
  .one-ingredient-split__figure-placeholder span {
    background: linear-gradient(135deg, #f4c14d 0%, #d49a1e 100%);
    border-radius: 60px / 30px;
    box-shadow:
      inset 0 -2px 4px rgba(0,0,0,0.18),
      inset 0 2px 4px rgba(255,255,255,0.45),
      0 2px 6px rgba(0,0,0,0.25);
  }
  .one-ingredient-split__figure-placeholder span:nth-child(1) { grid-column: 2 / 3; grid-row: 1 / 2; }
  .one-ingredient-split__figure-placeholder span:nth-child(2) { grid-column: 1 / 2; grid-row: 1 / 2; transform: translateX(8px); }
  .one-ingredient-split__figure-placeholder span:nth-child(3) { grid-column: 3 / 4; grid-row: 1 / 2; transform: translateX(-8px); }
  .one-ingredient-split__figure-placeholder span:nth-child(4) { grid-column: 1 / 3; grid-row: 2 / 3; transform: translate(20px, -8px); }
  .one-ingredient-split__figure-placeholder span:nth-child(5) { grid-column: 2 / 4; grid-row: 2 / 3; transform: translate(-20px, -8px); }
```

- [ ] **Step 2: Commit, pull-rebase, push to Shopify**

```bash
cd /Users/jonas/Documents/IDE/TCC
git add tcc-theme/sections/one-ingredient-split.liquid
git commit -m "style(pdp): one-ingredient-split card figure + CSS capsule placeholder"
git fetch origin && git pull --rebase origin main
cd tcc-theme
shopify theme push --theme=161862353132 --nodelete --json
```

- [ ] **Step 3: Screenshot & verify**

Navigate to the PDP at 1280×900, scroll to the section, screenshot to `/Users/jonas/Documents/IDE/TCC/task-ois-task5-card-desktop.png`. Confirm:
- Card is a dark-green rounded square (~520×520 desktop)
- A small cluster of amber/gold "capsules" appears centered inside the card
- No callouts visible yet (they're hidden behind the figure with no positioning)

- [ ] **Step 4: Push to GitHub**

```bash
cd /Users/jonas/Documents/IDE/TCC
git push origin main
```

---

## Task 6: Position the 5 callouts (no leader lines yet)

**Files:**
- Modify: `tcc-theme/sections/one-ingredient-split.liquid` — extend `<style>` block with callout positioning.

- [ ] **Step 1: Append callout styles**

Append the following inside the existing `<style>` block (before its closing tag):

```css
  /* ===== Callouts ===== */
  .one-ingredient-split__callouts {
    position: absolute;
    inset: 0;
    z-index: 2;
    pointer-events: none;
  }
  .one-ingredient-split__callout {
    position: absolute;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 8px;
    color: #ffffff;
    text-align: center;
    max-width: 140px;
    pointer-events: auto;
  }
  .one-ingredient-split__callout-icon {
    width: 44px;
    height: 44px;
    border-radius: 50%;
    border: 1.5px solid rgba(255,255,255,0.9);
    display: inline-flex;
    align-items: center;
    justify-content: center;
    background: transparent;
  }
  .one-ingredient-split__callout-icon svg { width: 22px; height: 22px; }
  .one-ingredient-split__callout-label {
    display: flex;
    flex-direction: column;
    line-height: 16px;
  }
  .one-ingredient-split__callout-line1 { font-size: 13px; font-weight: 600; }
  .one-ingredient-split__callout-line2 { font-size: 13px; font-weight: 400; opacity: 0.9; }

  /* Position variants — anchored to the 5 Resilia corners. */
  .one-ingredient-split__callout--top_left     { top: 32px;    left: 32px;   align-items: flex-start; text-align: left; }
  .one-ingredient-split__callout--mid_left     { top: 50%;     left: 16px;   transform: translateY(-50%); align-items: flex-start; text-align: left; }
  .one-ingredient-split__callout--mid_right    { top: 50%;     right: 16px;  transform: translateY(-50%); align-items: flex-end;   text-align: right; }
  .one-ingredient-split__callout--bottom_left  { bottom: 32px; left: 32px;   align-items: flex-start; text-align: left; }
  .one-ingredient-split__callout--bottom_right { bottom: 32px; right: 32px;  align-items: flex-end;   text-align: right; }
```

- [ ] **Step 2: Commit, pull-rebase, push to Shopify**

```bash
cd /Users/jonas/Documents/IDE/TCC
git add tcc-theme/sections/one-ingredient-split.liquid
git commit -m "style(pdp): one-ingredient-split position the 5 callouts on the card"
git fetch origin && git pull --rebase origin main
cd tcc-theme
shopify theme push --theme=161862353132 --nodelete --json
```

- [ ] **Step 3: Screenshot & verify against Resilia**

Take two screenshots:
- `/Users/jonas/Documents/IDE/TCC/task-ois-task6-tcc-desktop.png` (our PDP, 1280×900, scrolled to section)
- `/Users/jonas/Documents/IDE/TCC/task-ois-task6-resilia-desktop.png` (Resilia equivalent: `https://try.resilia.shop/rsl`, 1280×900, scrolled to "One Ingredient. Endless Immune Support.")

Compare side by side. Confirm:
- 5 callouts visible at TL / ML / MR / BL / BR
- Each callout: icon in a thin white circle on top, 2-line label below
- TCC callout text content matches the spec table (4,000 FU per capsule, Wild-fermented Bacillus subtilis, Third-party tested every batch, K2-free for blood-thinner users, Single ingredient no proprietary blend)
- Color/positioning is visually parallel to Resilia (within ~16px tolerance — not pixel-perfect)

If a callout is clipped or overlapping the capsule cluster, adjust the `top/left/bottom/right` values and re-push.

- [ ] **Step 4: Push to GitHub**

```bash
cd /Users/jonas/Documents/IDE/TCC
git push origin main
```

---

## Task 7: Add the SVG dotted leader lines (desktop only)

**Files:**
- Modify: `tcc-theme/sections/one-ingredient-split.liquid` — append leader-line styles.

The SVG element was rendered in the Task 1 markup with `viewBox="0 0 100 100"`; coordinates use percentage-of-card units. This task only adds the CSS that positions and styles the SVG.

- [ ] **Step 1: Append leader-line styles**

Append the following inside the existing `<style>` block (before its closing tag):

```css
  /* ===== Dotted leader lines (desktop only) ===== */
  .one-ingredient-split__leaders {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    z-index: 1;
    pointer-events: none;
    overflow: visible;
  }
  @media (max-width: 749px) {
    .one-ingredient-split__leaders { display: none; }
  }
```

- [ ] **Step 2: Commit, pull-rebase, push to Shopify**

```bash
cd /Users/jonas/Documents/IDE/TCC
git add tcc-theme/sections/one-ingredient-split.liquid
git commit -m "style(pdp): one-ingredient-split dotted leader lines (desktop)"
git fetch origin && git pull --rebase origin main
cd tcc-theme
shopify theme push --theme=161862353132 --nodelete --json
```

- [ ] **Step 3: Screenshot & verify**

Take a screenshot at 1280×900 to `/Users/jonas/Documents/IDE/TCC/task-ois-task7-leaders-desktop.png`. Confirm:
- 5 thin white dotted lines emerge from each callout's icon area
- All 5 converge near the center of the capsule cluster
- Line opacity ~0.65 (subtle, not aggressive)

If lines look too thick / too short / wrong angle, adjust the `x1`/`y1` coordinates in the Liquid SVG block (Task 1's markup) — those are the per-position anchor points. The convergence target is `(50, 50)` (center of card). Re-push.

- [ ] **Step 4: Push to GitHub**

```bash
cd /Users/jonas/Documents/IDE/TCC
git push origin main
```

---

## Task 8: Mobile layout

**Files:**
- Modify: `tcc-theme/sections/one-ingredient-split.liquid` — append mobile-specific styles.

On mobile, the grid is already single-column from Task 4. This task restyles the callouts inside the card on mobile so they don't try to absolute-position around a much smaller figure.

- [ ] **Step 1: Append mobile styles**

Append the following inside the existing `<style>` block (before its closing tag):

```css
  /* ===== Mobile: callouts collapse to a 2-col pill grid below the figure ===== */
  @media (max-width: 749px) {
    .one-ingredient-split__card {
      aspect-ratio: auto;
      padding: 24px 20px 28px;
      min-height: 360px;
      display: flex;
      flex-direction: column;
    }
    .one-ingredient-split__figure {
      position: relative;
      inset: auto;
      width: 100%;
      height: 160px;
      flex: 0 0 auto;
      margin-bottom: 20px;
    }
    .one-ingredient-split__figure-placeholder {
      width: 160px;
      height: 160px;
      grid-template-columns: 44px 44px 44px;
      grid-template-rows: 44px 44px;
    }
    .one-ingredient-split__callouts {
      position: relative;
      inset: auto;
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 16px 12px;
    }
    .one-ingredient-split__callout,
    .one-ingredient-split__callout--top_left,
    .one-ingredient-split__callout--mid_left,
    .one-ingredient-split__callout--mid_right,
    .one-ingredient-split__callout--bottom_left,
    .one-ingredient-split__callout--bottom_right {
      position: relative;
      top: auto; left: auto; right: auto; bottom: auto;
      transform: none;
      flex-direction: row;
      align-items: center;
      text-align: left;
      gap: 10px;
      max-width: none;
    }
    .one-ingredient-split__callout-icon { width: 36px; height: 36px; flex: 0 0 36px; }
    .one-ingredient-split__callout-icon svg { width: 18px; height: 18px; }
    .one-ingredient-split__callout-label { line-height: 15px; }
    .one-ingredient-split__callout-line1,
    .one-ingredient-split__callout-line2 { font-size: 12px; }

    /* Mobile section order — heading sits above the card, body+lists below. */
    .one-ingredient-split__text { display: contents; }
    .one-ingredient-split__heading { order: 1; }
    .one-ingredient-split__eyebrow { order: 0; }
    .one-ingredient-split__card-wrap { order: 2; margin: 8px 0 24px; }
    .one-ingredient-split__intro        { order: 3; }
    .one-ingredient-split__list-heading { order: 4; }
    .one-ingredient-split__list         { order: 5; }
    .one-ingredient-split__closing      { order: 6; }
    .one-ingredient-split__grid { display: flex; flex-direction: column; }
  }
```

Note: the `display: contents` trick on `__text` collapses the wrapper so the flex `order` values on its children participate in the parent `__grid` flex order — necessary to reorder heading → card → body without restructuring the markup.

- [ ] **Step 2: Commit, pull-rebase, push to Shopify**

```bash
cd /Users/jonas/Documents/IDE/TCC
git add tcc-theme/sections/one-ingredient-split.liquid
git commit -m "style(pdp): one-ingredient-split mobile layout (heading > card > body, pill grid)"
git fetch origin && git pull --rebase origin main
cd tcc-theme
shopify theme push --theme=161862353132 --nodelete --json
```

- [ ] **Step 3: Screenshot at mobile viewport**

Navigate Playwright to the spike-detox PDP at viewport 390×844. Scroll to the section. Save screenshots:
- `/Users/jonas/Documents/IDE/TCC/task-ois-task8-tcc-mobile.png` (TCC)
- `/Users/jonas/Documents/IDE/TCC/task-ois-task8-resilia-mobile.png` (`try.resilia.shop/rsl` at 390×844, scrolled to equivalent section)

Confirm on TCC mobile:
- Heading appears first
- Dark-green card appears second, full-width, capsule figure at top
- 2-column pill grid of 5 callouts below the figure inside the card
- No dotted leader lines
- Body paragraphs + "How It Works" / "Natural Benefits" lists + closing paragraph appear in order below the card
- Card content is readable, callouts don't overflow

- [ ] **Step 4: Push to GitHub**

```bash
cd /Users/jonas/Documents/IDE/TCC
git push origin main
```

---

## Task 9: Final polish + STATUS doc update

**Files:**
- Modify: `STATUS-pdp.md` — note the new section is live, with placeholder image flag
- (Optional) Modify: `tcc-theme/sections/one-ingredient-split.liquid` — final spacing tweaks if anything is off

- [ ] **Step 1: Side-by-side comparison check**

Final visual diff against Resilia. Open all four reference shots side by side:
- `/Users/jonas/Documents/IDE/TCC/task-ois-task6-tcc-desktop.png` vs `task-ois-task6-resilia-desktop.png`
- `/Users/jonas/Documents/IDE/TCC/task-ois-task8-tcc-mobile.png` vs `task-ois-task8-resilia-mobile.png`

Tolerance: spacing within ~16px, callout positions within ~24px, typography exact (since the typography pass already locked it). If anything is off by more than that, edit the relevant CSS and push one polish commit. Otherwise skip to Step 2.

- [ ] **Step 2: Update `STATUS-pdp.md`**

Open `/Users/jonas/Documents/IDE/TCC/STATUS-pdp.md` and add an entry under the most recent phase section. If a heading already exists for "Resilia parity work", add this bullet under it; otherwise create a new bullet block:

```markdown
- **2026-05-15** — Shipped `one-ingredient-split` section under `trial_stats` on the Spike Detox PDP. Mirrors Resilia's "One Ingredient. Endless Immune Support." beat (style, spacing, topic). Capsule image is a CSS-rendered placeholder — replace with real product photography when available.
```

- [ ] **Step 3: Commit STATUS update**

```bash
cd /Users/jonas/Documents/IDE/TCC
git add STATUS-pdp.md
git commit -m "docs(status): log one-ingredient-split section ship"
git fetch origin && git pull --rebase origin main
git push origin main
```

- [ ] **Step 4: Final verification screenshot**

Take a final desktop screenshot at 1280×900 and a final mobile shot at 390×844, saved as:
- `/Users/jonas/Documents/IDE/TCC/one-ingredient-split-final-desktop-2026-05-15.png`
- `/Users/jonas/Documents/IDE/TCC/one-ingredient-split-final-mobile-2026-05-15.png`

These are reference artifacts, not committed — they live in the repo root with the other dated screenshot files.

---

## Open follow-ups (not in scope for this plan)

- **Real capsule-cluster photography:** replace the CSS placeholder via the section's `card_image` setting once a real photo exists. No code change needed.
- **Icon set expansion:** the snippet currently ships glyphs for `capsule`, `leaf`, `shield`, `beaker`, `no_k2`, `sparkle`, `molecule`. If the schema's icon enum grows past these, extend `tcc-theme/snippets/one-ingredient-icon.liquid` with a matching `when` branch.
- **Reuse on Complete PDP:** the section is generic. Adding a `one_ingredient_split` instance to `templates/product.spike-detox-complete.json` with Complete-specific copy is a paste-and-edit job, not a section change.
