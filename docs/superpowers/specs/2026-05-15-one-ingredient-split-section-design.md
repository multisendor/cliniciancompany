# One-Ingredient Split Section — `one-ingredient-split`

**Date:** 2026-05-15
**Scope:** New custom Shopify section for the "single-ingredient pitch" — two-column layout with a dark-green capsule card and dotted-leader callouts on the right, mirroring Resilia's "One Ingredient. Endless Immune Support." section on `try.resilia.shop/rsl`. First use: Spike Detox PDP, slotted directly under `trial_stats`.
**Status:** Spec — drafted; user said "go", staying true to Resilia on style, spacing, and topic.

---

## 1. Purpose

Mirror Resilia's "one-ingredient" moment on the Spike Detox PDP, immediately under the trial-stats outcome strip. Resilia's section is a high-conversion "why this single ingredient is powerful" beat that sits after their proof points — same placement we want here.

The existing `one_ingredient` section (position 15, headline "One Ingredient. Endless Spike Defense.", plain `image-with-text`) stays unchanged. This new section adds the Resilia-styled visual beat earlier in the page.

## 2. What it is NOT

- Not a generic image-with-text block (rejected — the dark-green capsule card with five dotted-leader callouts is distinctive enough not to graft onto `image-with-text` or `alternating-row`)
- Not a multi-product section (Spike Detox specific copy; schema is generic so it could be reused on Complete or future PDPs)
- Not a replacement for the existing `one_ingredient` section at position 15

## 3. Placement

`templates/product.spike-detox.json`:
- Add a new section key `one_ingredient_split` of type `one-ingredient-split`
- Insert into `order` array immediately after `trial_stats`, before `subscription`

Resulting order (truncated):
```
... cardio_solution, trial_stats, one_ingredient_split, subscription, guarantee, ...
```

## 4. Files

**New:**
- `tcc-theme/sections/one-ingredient-split.liquid`

**Modified:**
- `tcc-theme/templates/product.spike-detox.json` — add section key + slot into order

## 5. Section anatomy

Two-column grid on desktop (≥ 750px). On mobile (< 750px), collapses to single column.

### Left column (text)

1. Heading (h2) — Resilia weight/size already in scope from the typography pass: 30/36, wt 700, ls normal
2. Intro paragraph 1
3. Intro paragraph 2 (optional)
4. List A sub-head (bold inline, default "How It Works")
5. List A — 3 bullets (green dot bullets, matches existing `alternating-row` bullet styling)
6. List B sub-head (default "Natural Benefits")
7. List B — 4 bullets
8. Closing paragraph (optional)

### Right column (visual card)

- A `<figure>` with rounded corners and the dark forest-green background (default `#0F3F2E`, exposed as a setting)
- Centered hero image inside the card: capsule cluster (image_picker setting; if empty, a CSS-rendered placeholder cluster ships so the section is never blank)
- 5 callout blocks positioned at corners: top_left, mid_left, mid_right, bottom_left, bottom_right
- Dotted leader lines drawn via inline SVG overlay (`stroke-dasharray: 2 4`), connecting each callout's anchor dot to a focal point on the figure

Card aspect on desktop ≈ 520×560 px (mirror Resilia's proportion). On mobile, card is full-width minus 20px gutter, height auto.

## 6. Responsive behavior

**Desktop (≥ 750px):**
- `grid-template-columns: 1fr 1fr`
- `gap: clamp(40px, 6vw, 80px)`
- Container `max-width: 1200px`, `padding-inline: clamp(20px, 4vw, 48px)` — matches `outcome-stats.liquid` container

**Mobile (< 750px):**
- Single column
- Order: heading → card → body paragraphs → lists → closing
  - Rationale: matches the `fix(theme): mobile layout matches Resilia` pattern shipped on `alternating-row` (heading above, body below the visual)
- Inside the card on mobile: capsule figure stays at the top of the card, then the 5 callouts re-render as a 2-column icon-pill grid directly below the figure (icon glyph + 2-line label). The dotted leader lines + absolute positioning are dropped on mobile — dotted lines at < 400px viewport widths read as noise; the pill grid is more readable.

**Card on mobile:**
- `aspect-ratio: 1 / 1` so the capsule image scales cleanly
- Internal padding: 24px

## 7. Liquid schema

```jsonc
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
            { "value": "capsule",     "label": "Capsule" },
            { "value": "leaf",        "label": "Leaf / herb" },
            { "value": "shield",      "label": "Shield (tested)" },
            { "value": "beaker",      "label": "Beaker / lab" },
            { "value": "no_k2",       "label": "K2-free badge" },
            { "value": "sparkle",     "label": "Sparkle (single)" },
            { "value": "molecule",    "label": "Molecule / enzyme" }
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
```

## 8. Copy (Resilia-faithful, topic-mirrored)

Resilia's paragraph anatomy is: intro framing ("Unlike many supplements…") → mid-line setup ("Here's what makes [X] so powerful in every serving:") → two parallel lists → reassurance closing ("With [X], you're giving your body…"). The TCC copy below mirrors that anatomy beat-for-beat.

**Eyebrow:** *(empty — Resilia has no eyebrow on this section)*

**Heading:** One Ingredient. Centuries of Use.

**Intro paragraph 1:**
> Unlike most nattokinase supplements built around proprietary blends, undisclosed K2, or 2,000-FU regulatory minimums, Pure keeps it simple — featuring just one studied, time-tested enzyme: wild-fermented nattokinase from traditional Bacillus subtilis. From its serine-protease structure to its decades-deep cardiovascular research base, nattokinase offers a clean, single-ingredient way to support healthy circulation, fibrinolytic balance, and daily resilience.

**Intro paragraph 2 (mid-line setup, mirrors Resilia exactly):**
> Here's what makes Pure so powerful in every serving:

**List A — How It Works (3, mirrors Resilia's "Key Compounds" subject-verb-benefit structure):**
1. Serine protease activity acts directly on fibrin to support healthy blood-flow regulation
2. Fibrinolytic action helps the body maintain natural clot-clearance balance
3. Wild Bacillus subtilis fermentation delivers the same traditional natto enzyme studied since 1980

**List B — Natural Benefits (4, mirrors Resilia's positive-verb structure):**
1. Supports cardiovascular and circulatory health
2. Helps maintain healthy fibrinolytic balance
3. Promotes steady, sustained daily energy
4. Built K2-free for compatibility with common blood thinners

**Closing paragraph (mirrors Resilia's "With X, you're giving your body…" reassurance):**
> With Pure, you're giving your body one of nature's most-studied enzymes — trusted for centuries in traditional Japanese natto and validated by 40+ years of peer-reviewed cardiovascular research.

## 9. Visual callouts (5, dose-forward, Resilia positions)

| Position | Line 1 | Line 2 | Icon |
|---|---|---|---|
| top_left | 4,000 FU | per capsule | capsule |
| mid_left | Wild-fermented | Bacillus subtilis | leaf |
| mid_right | Third-party tested | every batch | shield |
| bottom_left | K2-free for | blood-thinner users | no_k2 |
| bottom_right | Single ingredient | no proprietary blend | sparkle |

## 10. Styling tokens

Inherits the Resilia-aligned typography pushed in commit `9ac9122`:

- Section heading: `30/36`, wt 700, ls normal (desktop) — `26.6/31.92`, wt 700 (mobile)
- Body: `16/24`, wt 400, ls normal (desktop) — `15.6/23.4`, wt 400 (mobile)
- List sub-heads ("How It Works:", "Natural Benefits:"): `16/24`, wt 700, ls normal, inline-bold treatment with a colon
- List items: `16/24`, wt 400, ls normal, green-dot bullet (reuse the SVG dot from `alternating-row.liquid`)

Card:
- BG color: `#0F3F2E` (exposed as setting)
- Border radius: `24px`
- Internal padding: `48px` desktop / `24px` mobile
- Callout pill BG: `transparent` with a `1.5px` white circular icon stroke ring (matches Resilia)
- Callout label text: `13/16`, wt 400, color `#FFFFFF`, ls normal
- Dotted leader: `stroke="#FFFFFF"`, `stroke-width="1"`, `stroke-dasharray="2 4"`, `opacity="0.7"`

## 11. CONTRACT-5 audit-rule check

Verified against the bound audit rules in `research-clinical-evidence.md` and `addendum-fidelity-phase5.md`:

- ✅ No "pharmaceutical-grade" — copy uses "clinically active range" implicitly via reference to Chen 2022 in adjacent sections
- ✅ No "below 3,600 FU is placebo" wedge — frames dose as "stack to your protocol"
- ✅ No FLCCC / McCullough / IMA borrowed authority
- ✅ No RN/MD credentials or testimonials
- ✅ No urgency tactics
- ✅ No diagnostic claims — copy uses "supports", "helps maintain", "promotes", "compatible with" (structure-function language)
- ✅ Blood-thinner consult reminder is already on the trial_stats footnote immediately above this section; not duplicated here per the established pattern
- ✅ FDA disclaimer already lives in the FAQ footer per the rule

## 12. Build sequence

1. Create `tcc-theme/sections/one-ingredient-split.liquid` — markup + inline `<style>` block (matches per-section style pattern in `outcome-stats.liquid` and `alternating-row.liquid`)
2. Add `one_ingredient_split` section key to `templates/product.spike-detox.json`, prepopulated with the copy and the 5 callout blocks, and slot into `order` after `trial_stats`
3. `shopify theme push --theme=161862353132 --nodelete --json` (CONTRACT-2)
4. Visual QA via Playwright at desktop 1280×900 and mobile 390×844 against `https://try.resilia.shop/rsl` — confirm spacing, card proportions, callout placement, list typography
5. Iterate on spacing / callout positioning if visual diverges from Resilia
6. Commit (`feat(pdp): one-ingredient-split section under trial_stats — Resilia visual style`) and `git push origin main` (CONTRACT-3)

## 13. Open items / placeholders

- **Card image asset:** no real capsule-cluster photo exists yet. Ship with the CSS placeholder (a small grid of rounded "pills" approximating a cluster, or a single bottle/cap silhouette) until a product photo is taken. Document in `STATUS-pdp.md` as a placeholder.
- **Icon set:** the schema includes 7 icon options. Inline SVGs sourced from a single icon snippet (`tcc-theme/snippets/oi-icon.liquid`) — patterns established by `alternating-row` (uses a green checkmark inline SVG). For v1, ship the 5 icons actually used (capsule, leaf, shield, no_k2, sparkle); add the others as the schema grows.
