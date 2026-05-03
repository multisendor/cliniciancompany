# The Clinician Company Shopify Theme — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ship a Standard v1 Shopify theme for The Clinician Company by porting selected sections from the Curably ("Supplement Store") theme into a new `tcc-theme/`, retokenizing each against a single brand token file, and previewing it on `gmmehe-01.myshopify.com`.

**Architecture:** New theme folder copies ~20 Curably sections selectively. A single `assets/tcc-tokens.css` defines every brand decision as a CSS custom property; each ported section's CSS is rewritten to reference tokens — no hardcoded hex colors or font sizes. Brand strings live in `config/settings_data.json`. Workflow uses `shopify theme dev` against an unpublished theme on the new store.

**Tech Stack:** Shopify Dawn 15.4.1 (inherited via Curably), Liquid templating, CSS custom properties, vanilla JS, Shopify CLI 3.x, Inter font (loaded via @font-face).

**Spec:** `docs/superpowers/specs/2026-05-03-tcc-shopify-theme-design.md`

**Source theme (donor):** `Supplement Store/`

**Target theme:** `tcc-theme/`

---

## Conventions Used Throughout This Plan

**Per-section port recipe** (used in Phases 2–7):
1. Read source file at `Supplement Store/sections/<name>.liquid`
2. Grep the file for dependencies: `{% render '...' %}` (snippets) and `'...' | asset_url` (assets)
3. Copy section + transitive dependencies into `tcc-theme/`
4. Find/replace `curably-` → `tcc-` (CSS class names + asset filenames)
5. Find/replace any hardcoded Curably copy strings with TCC equivalents
6. Replace hardcoded colors / font-sizes / spacing with `var(--tcc-*)` tokens (see token table below)
7. Save, verify in preview at the dev URL, confirm no console errors
8. Commit with message: `feat(<phase>): port <section-name>`

**Token replacement table** (apply in step 6 of every section port):

| Find (Curably hardcoded) | Replace with (TCC token) |
|---|---|
| `rgb(159, 63, 63)` or `#9f3f3f` | `var(--tcc-ink)` |
| `rgb(105, 14, 14)` or `#690e0e` | `var(--tcc-ink)` |
| `rgb(251, 249, 247)` or `#fbf9f7` | `var(--tcc-bg)` |
| `linear-gradient(165deg, rgba(159,63,63,1) 14%, rgba(105,14,14,1) 85%)` | `var(--tcc-ink)` (no gradient in v1) |
| `font-family: 'Poppins'` etc. | `font-family: var(--tcc-font-sans)` |
| `font-family: 'Plus Jakarta Sans'` | `font-family: var(--tcc-font-sans)` |
| Any literal `font-size: NNpx` (>=24px) | nearest `var(--tcc-fs-*)` from token table |
| Any literal `padding`/`margin` value | nearest `var(--tcc-s-*)` (8pt grid) |

**Verification convention**: after every section port, the verification is "reload the preview URL, the section appears with TCC styling, no console errors." Mobile breakpoint is 749px — open dev tools, set viewport to 375px, confirm no broken layout.

**Commit convention**: every task ends with a single `git commit`. Phases get their own conventional-commit `type` prefix (`chore`, `feat`, `style`, `docs`).

---

## Phase 1 — Foundation

Goal: Have a runnable `tcc-theme/` skeleton on disk, connected to `gmmehe-01`, with `shopify theme dev` showing a working (if empty) preview before any section work begins.

### Task 1.1: Initialize git repo and gitignore at Health Store root

**Files:**
- Create: `Health Store/.gitignore`
- Create: `Health Store/README.md`

- [ ] **Step 1: Verify working directory**

Run: `pwd`
Expected: `/Users/jonas/Documents/IDE/Health Store`

- [ ] **Step 2: Verify not already a git repo**

Run: `git -C "/Users/jonas/Documents/IDE/Health Store" rev-parse --is-inside-work-tree 2>&1`
Expected: error message about not being a git repo (if it errors, good — proceed; if it returns "true", skip to Step 4)

- [ ] **Step 3: Initialize git**

Run: `git -C "/Users/jonas/Documents/IDE/Health Store" init -b main`
Expected: `Initialized empty Git repository...`

- [ ] **Step 4: Create .gitignore**

Write `Health Store/.gitignore`:

```
# OS
.DS_Store

# Editor
.vscode/
.idea/

# Brainstorm scratch
.superpowers/

# Shopify CLI
.shopifyignore-local
*.shopifylocal

# Node
node_modules/
.env
.env.local
```

- [ ] **Step 5: Create minimal README**

Write `Health Store/README.md`:

```markdown
# Health Store

Workspace for The Clinician Company (TCC) Shopify theme.

## Folders
- `tcc-theme/` — TCC working theme. Connected to `gmmehe-01.myshopify.com`.
- `Supplement Store/` — Curably reference theme (code donor; do not edit during TCC work).
- `docs/superpowers/specs/` — Design specs.
- `docs/superpowers/plans/` — Implementation plans.

## Quick start
See `tcc-theme/README.md`.
```

- [ ] **Step 6: Initial commit**

Run from `Health Store/`:

```bash
git add .gitignore README.md
git commit -m "chore: initialize Health Store workspace"
```

Expected: commit succeeds with 2 files.

### Task 1.2: Create tcc-theme folder structure with selective Curably copy

**Files:**
- Create: `tcc-theme/` (new directory)
- Copy from: `Supplement Store/` (selective)

- [ ] **Step 1: Create tcc-theme root and required subdirectories**

Run from `Health Store/`:

```bash
mkdir -p tcc-theme/{assets,blocks,config,layout,locales,sections,snippets,templates,templates/customers}
```

- [ ] **Step 2: Copy Curably scaffolding files (locales, base layout, base config)**

Run from `Health Store/`:

```bash
# locales (entire directory — translations are theme-agnostic)
cp -R "Supplement Store/locales/." tcc-theme/locales/

# layout
cp "Supplement Store/layout/theme.liquid" tcc-theme/layout/theme.liquid
cp "Supplement Store/layout/password.liquid" tcc-theme/layout/password.liquid

# base config files
cp "Supplement Store/config/settings_schema.json" tcc-theme/config/settings_schema.json

# .shopifyignore
cp "Supplement Store/.shopifyignore" tcc-theme/.shopifyignore
```

Expected: no errors; files appear in `tcc-theme/` mirrors.

- [ ] **Step 3: Verify scaffolding**

Run: `ls tcc-theme/layout tcc-theme/locales tcc-theme/config`
Expected: `theme.liquid` and `password.liquid` in layout; many `.json` files in locales; `settings_schema.json` in config.

- [ ] **Step 4: Commit scaffolding**

```bash
git add tcc-theme/
git commit -m "chore(theme): scaffold tcc-theme from Curably skeleton"
```

### Task 1.3: Write the brand token file (`tcc-tokens.css`)

**Files:**
- Create: `tcc-theme/assets/tcc-tokens.css`

- [ ] **Step 1: Write the complete tokens file**

Write `tcc-theme/assets/tcc-tokens.css`:

```css
/* The Clinician Company — Brand Tokens
   Single source of truth for every visual decision.
   No section CSS may use hardcoded colors, font sizes, or spacing
   that aren't defined here. */

@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700;800&display=swap');

:root {
  /* --- Color --- */
  --tcc-bg: #fafbfc;
  --tcc-ink: #0a1628;
  --tcc-ink-soft: #4a5b78;
  --tcc-line: #e8eef5;
  --tcc-card: #ffffff;
  --tcc-positive: #00a86b;
  --tcc-urgent: #c8281a;

  /* --- Type --- */
  --tcc-font-sans: 'Inter', system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
  --tcc-fs-display: clamp(40px, 6vw, 64px);
  --tcc-fs-h1: clamp(32px, 4vw, 48px);
  --tcc-fs-h2: clamp(24px, 3vw, 32px);
  --tcc-fs-body: 16px;
  --tcc-fs-eyebrow: 11px;
  --tcc-fs-small: 13px;
  --tcc-lh-tight: 1.05;
  --tcc-lh-body: 1.55;
  --tcc-ls-display: -1.5px;
  --tcc-ls-eyebrow: 2.5px;

  /* --- Weight --- */
  --tcc-fw-body: 400;
  --tcc-fw-medium: 500;
  --tcc-fw-bold: 700;
  --tcc-fw-display: 800;

  /* --- Spacing (8pt grid) --- */
  --tcc-s-1: 8px;
  --tcc-s-2: 16px;
  --tcc-s-3: 24px;
  --tcc-s-4: 32px;
  --tcc-s-6: 48px;
  --tcc-s-8: 64px;

  /* --- Radius / elevation --- */
  --tcc-radius: 6px;
  --tcc-shadow-card: 0 1px 3px rgba(10, 22, 40, 0.06);
}

/* Global resets bound to tokens */
body {
  font-family: var(--tcc-font-sans);
  font-size: var(--tcc-fs-body);
  line-height: var(--tcc-lh-body);
  color: var(--tcc-ink);
  background: var(--tcc-bg);
  -webkit-font-smoothing: antialiased;
}

h1, h2, h3, h4, h5, h6 {
  font-family: var(--tcc-font-sans);
  color: var(--tcc-ink);
  letter-spacing: var(--tcc-ls-display);
  line-height: var(--tcc-lh-tight);
  font-weight: var(--tcc-fw-display);
}

/* Utility classes — for Liquid sections to call */
.tcc-eyebrow {
  font-size: var(--tcc-fs-eyebrow);
  font-weight: var(--tcc-fw-bold);
  text-transform: uppercase;
  letter-spacing: var(--tcc-ls-eyebrow);
  color: var(--tcc-ink-soft);
}

.tcc-button {
  display: inline-block;
  padding: 14px 28px;
  font-family: var(--tcc-font-sans);
  font-size: var(--tcc-fs-small);
  font-weight: var(--tcc-fw-bold);
  text-transform: uppercase;
  letter-spacing: 1.5px;
  text-decoration: none;
  border-radius: var(--tcc-radius);
  background: var(--tcc-ink);
  color: var(--tcc-bg);
  border: 0;
  cursor: pointer;
  transition: opacity 0.15s ease;
}
.tcc-button:hover { opacity: 0.85; }

.tcc-card {
  background: var(--tcc-card);
  border-radius: var(--tcc-radius);
  box-shadow: var(--tcc-shadow-card);
  border: 1px solid var(--tcc-line);
}
```

- [ ] **Step 2: Commit tokens**

```bash
git add tcc-theme/assets/tcc-tokens.css
git commit -m "feat(theme): add tcc-tokens.css brand token system"
```

### Task 1.4: Wire brand strings into `config/settings_data.json`

**Files:**
- Create: `tcc-theme/config/settings_data.json`

- [ ] **Step 1: Write the settings_data.json with brand strings**

Write `tcc-theme/config/settings_data.json`:

```json
{
  "current": {
    "brand_name": "The Clinician Company",
    "brand_short": "TCC",
    "brand_tagline": "Practitioner-Grade Wellness",
    "brand_claim_disclaimer": "These statements have not been evaluated by the Food and Drug Administration. This product is not intended to diagnose, treat, cure, or prevent any disease.",
    "type_header_font": "inter_n7",
    "type_body_font": "inter_n4",
    "color_schemes": {},
    "page_width": 1400,
    "spacing_sections": 32,
    "show_brand_in_footer": true
  },
  "platform": {},
  "presets": {
    "Default": {
      "brand_name": "The Clinician Company",
      "brand_short": "TCC",
      "brand_tagline": "Practitioner-Grade Wellness"
    }
  }
}
```

- [ ] **Step 2: Commit**

```bash
git add tcc-theme/config/settings_data.json
git commit -m "feat(theme): add brand strings via settings_data.json"
```

### Task 1.5: Update `theme.liquid` to load tokens and strip Curably references

**Files:**
- Modify: `tcc-theme/layout/theme.liquid`

- [ ] **Step 1: Read current theme.liquid to understand structure**

Read `tcc-theme/layout/theme.liquid` (whole file). Identify:
- Where `curably-custom.css` is loaded
- Any hardcoded "Curably" strings in `<title>`, meta tags, or JSON-LD
- Where stylesheets are loaded in `<head>`

- [ ] **Step 2: Add tcc-tokens.css load BEFORE any other stylesheet**

In `<head>`, find the first `{{ '...' | asset_url | stylesheet_tag }}` line. Insert IMMEDIATELY ABOVE it:

```liquid
{{ 'tcc-tokens.css' | asset_url | stylesheet_tag }}
```

- [ ] **Step 3: Replace `curably-custom.css` reference (if present)**

Find: `{{ 'curably-custom.css' | asset_url | stylesheet_tag }}`
Replace: (delete the line — we'll port `curably-custom.css` content into a renamed `tcc-base.css` later if needed; v1 relies on tokens + per-section CSS)

- [ ] **Step 4: Replace any hardcoded "Curably" strings**

Find any literal `Curably` text and replace with `{{ settings.brand_name }}` or `{{ settings.brand_short }}` as appropriate. Common spots:
- `<title>` fallback
- `og:site_name`
- Email/contact mentions
- JSON-LD `@type: Organization` `name` field

- [ ] **Step 5: Verify no Curably references remain**

Run: `grep -i "curably" tcc-theme/layout/theme.liquid`
Expected: no output (empty)

- [ ] **Step 6: Commit**

```bash
git add tcc-theme/layout/theme.liquid
git commit -m "feat(theme): wire tcc-tokens.css into theme.liquid, strip Curably refs"
```

### Task 1.6: Create empty templates for the 5 v1 pages

**Files:**
- Create: `tcc-theme/templates/index.json`
- Create: `tcc-theme/templates/product.spike-detox.json`
- Create: `tcc-theme/templates/collection.json`
- Create: `tcc-theme/templates/page.about.json`
- Create: `tcc-theme/templates/cart.json`

- [ ] **Step 1: Create empty index.json (homepage)**

Write `tcc-theme/templates/index.json`:

```json
{
  "sections": {},
  "order": []
}
```

- [ ] **Step 2: Create empty product.spike-detox.json (PDP for hero product)**

Write `tcc-theme/templates/product.spike-detox.json`:

```json
{
  "sections": {},
  "order": []
}
```

- [ ] **Step 3: Create empty collection.json**

Write `tcc-theme/templates/collection.json`:

```json
{
  "sections": {},
  "order": []
}
```

- [ ] **Step 4: Create empty page.about.json**

Write `tcc-theme/templates/page.about.json`:

```json
{
  "sections": {},
  "order": []
}
```

- [ ] **Step 5: Create empty cart.json**

Write `tcc-theme/templates/cart.json`:

```json
{
  "sections": {},
  "order": []
}
```

- [ ] **Step 6: Commit**

```bash
git add tcc-theme/templates/
git commit -m "feat(theme): add empty page templates for v1 pages"
```

### Task 1.7: Connect Shopify CLI to `gmmehe-01` and verify dev preview

**Files:**
- (none — CLI configuration only)

- [ ] **Step 1: Verify Shopify CLI is installed**

Run: `shopify version`
Expected: prints version 3.x. If not installed, run `brew install shopify-cli` first.

- [ ] **Step 2: Authenticate (if needed)**

Run from `tcc-theme/`: `shopify auth logout && shopify theme dev --store gmmehe-01.myshopify.com`
A browser will open for OAuth. Approve. The CLI will create an unpublished theme and start the dev server.

Expected: console prints a preview URL like `https://gmmehe-01.myshopify.com?preview_theme_id=XXXXXXX` and `Sync: complete`.

- [ ] **Step 3: Open the preview URL in a browser**

The homepage will be blank or show a Shopify default-content message ("This template is empty"). That's expected — `index.json` has no sections yet.

- [ ] **Step 4: Verify tcc-tokens.css is loaded**

In browser DevTools, Sources tab, search for `tcc-tokens.css`. Confirm it's loaded.

In Console, run: `getComputedStyle(document.body).getPropertyValue('--tcc-ink').trim()`
Expected: `#0a1628`

- [ ] **Step 5: Leave dev server running for the rest of the day**

Do not kill the process. All subsequent file edits will hot-reload automatically.

- [ ] **Step 6: Commit any CLI-generated files (e.g., `.shopify/`)**

```bash
git status
git add -A tcc-theme/
git commit -m "chore(theme): connect to gmmehe-01.myshopify.com via Shopify CLI" --allow-empty
```

(Use `--allow-empty` if no files changed.)

---

## Phase 2 — Globals

Goal: Header, footer, and announcement-bar render with TCC branding on every page.

### Task 2.1: Port `announcement-bar` section

**Files:**
- Read: `Supplement Store/sections/announcement-bar.liquid`
- Create: `tcc-theme/sections/announcement-bar.liquid`

- [ ] **Step 1: Read the source file fully**

Read `Supplement Store/sections/announcement-bar.liquid` (whole file).

- [ ] **Step 2: Identify dependencies**

Run: `grep -E "(render '|asset_url|inline_asset_content)" "Supplement Store/sections/announcement-bar.liquid"`
List the snippet renders (e.g., `social-icons`) and asset references (e.g., `component-slideshow.css`, `icon-arrow.svg`).

- [ ] **Step 3: Copy section file**

Run: `cp "Supplement Store/sections/announcement-bar.liquid" tcc-theme/sections/announcement-bar.liquid`

- [ ] **Step 4: Copy required snippets and assets**

For each dependency from Step 2, copy from `Supplement Store/snippets/` or `Supplement Store/assets/` into the parallel `tcc-theme/` location. Example:

```bash
cp "Supplement Store/snippets/social-icons.liquid" tcc-theme/snippets/social-icons.liquid
cp "Supplement Store/assets/component-slideshow.css" tcc-theme/assets/component-slideshow.css
cp "Supplement Store/assets/component-slider.css" tcc-theme/assets/component-slider.css
cp "Supplement Store/assets/component-list-social.css" tcc-theme/assets/component-list-social.css
```

(Adjust based on what Step 2 found.)

- [ ] **Step 5: Strip `curably-` class names**

In `tcc-theme/sections/announcement-bar.liquid`, find/replace `curably-` → `tcc-` (use Edit tool with `replace_all: true`).

- [ ] **Step 6: Strip Curably-specific copy**

Replace any literal Curably copy in the section's settings_schema (default text) with TCC-appropriate copy. Default announcement text → `"Free U.S. shipping over $50 · Practitioner-formulated"`.

- [ ] **Step 7: Retokenize CSS**

Locate any `<style>` block in the section. Apply the token replacement table from the conventions section. Specifically: hardcoded colors → `var(--tcc-*)`; hardcoded font sizes ≥24px → `var(--tcc-fs-*)`.

- [ ] **Step 8: Smoke test in preview**

Reload the preview URL. The page may still be empty (no sections in index.json yet), but check:
- DevTools Console: no errors mentioning `announcement-bar`
- DevTools Network: announcement-bar.liquid file is fetched without 404s for its dependencies

- [ ] **Step 9: Commit**

```bash
git add tcc-theme/sections/announcement-bar.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(globals): port announcement-bar section"
```

### Task 2.2: Port `header` section

**Files:**
- Read: `Supplement Store/sections/header.liquid`
- Create: `tcc-theme/sections/header.liquid`
- Create: `tcc-theme/sections/header-group.json`

- [ ] **Step 1: Read source files**

Read `Supplement Store/sections/header.liquid` and `Supplement Store/sections/header-group.json` (whole files).

- [ ] **Step 2: Identify dependencies**

Run: `grep -E "(render '|asset_url|inline_asset_content)" "Supplement Store/sections/header.liquid"`
List snippets and assets.

- [ ] **Step 3: Copy section + group config + dependencies**

```bash
cp "Supplement Store/sections/header.liquid" tcc-theme/sections/header.liquid
cp "Supplement Store/sections/header-group.json" tcc-theme/sections/header-group.json
# Copy each dependency identified in Step 2
```

Common header dependencies likely include: `header-dropdown-menu.liquid`, `header-mega-menu.liquid` (snippets) and `component-mega-menu.css`, `component-list-menu.css`, `cart-icon-bubble.liquid` (snippet).

- [ ] **Step 4: Strip `curably-` prefixes and Curably copy strings**

In `tcc-theme/sections/header.liquid`:
- Find/replace `curably-` → `tcc-` (replace_all: true)
- Find any literal `Curably` text and replace with `{{ settings.brand_name }}` or `{{ settings.brand_short }}`

- [ ] **Step 5: Simplify nav for v1 (Shop / Science / About)**

In `header-group.json`, locate the `menu` setting. Update its blocks (or note that the menu must be configured in Shopify admin → Online Store → Navigation; for v1 the dev should configure a "Main menu" with three links: Shop, Science, About). Document this in a comment within the JSON if the setting allows.

- [ ] **Step 6: Disable/remove search bar block (if present in default header-group.json)**

Find the search-related block in `header-group.json` and remove its entry from the `order` array (keep its definition for future re-enabling). Save.

- [ ] **Step 7: Retokenize CSS**

Locate any inline `<style>` in `header.liquid`. Apply token replacement per the conventions table.

- [ ] **Step 8: Smoke test**

Reload preview. Header should appear at the top of the page even though the body is empty. Check:
- TCC wordmark / brand name visible (or placeholder if logo image not set — that's fine for v1)
- 3 nav links visible (after Shopify admin menu configuration; if not configured yet, link list will be empty)
- Cart icon visible right
- No console errors

- [ ] **Step 9: Commit**

```bash
git add tcc-theme/sections/header.liquid tcc-theme/sections/header-group.json tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(globals): port header section + group config"
```

### Task 2.3: Port `footer` section

**Files:**
- Read: `Supplement Store/sections/footer.liquid`
- Create: `tcc-theme/sections/footer.liquid`
- Create: `tcc-theme/sections/footer-group.json`

- [ ] **Step 1: Read source files**

Read `Supplement Store/sections/footer.liquid` and `Supplement Store/sections/footer-group.json` (whole files).

- [ ] **Step 2: Identify dependencies**

Run: `grep -E "(render '|asset_url|inline_asset_content)" "Supplement Store/sections/footer.liquid"`

- [ ] **Step 3: Copy section + group config + deps**

```bash
cp "Supplement Store/sections/footer.liquid" tcc-theme/sections/footer.liquid
cp "Supplement Store/sections/footer-group.json" tcc-theme/sections/footer-group.json
# Copy each dependency from Step 2
```

- [ ] **Step 4: Strip Curably refs (heaviest section — has 32 `curably-` matches per earlier grep)**

In `tcc-theme/sections/footer.liquid`:
- Find/replace `curably-` → `tcc-` (replace_all: true)
- Find any literal `Curably` text and replace with `{{ settings.brand_name }}` or `{{ settings.brand_short }}`
- Find references to `curably.shop` / `curably.com` and replace with `gmmehe-01.myshopify.com` (placeholder until custom domain set)

- [ ] **Step 5: Add disclaimer line**

Locate the bottom-most `<div>` of the footer (typically copyright row). Insert above it:

```liquid
<p style="font-size: var(--tcc-fs-small); color: var(--tcc-ink-soft); padding: var(--tcc-s-3) 0; max-width: 800px; margin: 0 auto; text-align: center;">
  {{ settings.brand_claim_disclaimer }}
</p>
```

- [ ] **Step 6: Retokenize CSS**

Apply token replacement table to any inline `<style>` blocks.

- [ ] **Step 7: Smoke test**

Reload preview. Footer renders with TCC branding, disclaimer line is visible above copyright. No console errors.

- [ ] **Step 8: Commit**

```bash
git add tcc-theme/sections/footer.liquid tcc-theme/sections/footer-group.json tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(globals): port footer with brand disclaimer line"
```

---

## Phase 3 — Homepage

Goal: 5 sections render in order on the homepage with TCC branding and copy.

### Task 3.1: Port `hero-product-showcase`

**Files:**
- Read: `Supplement Store/sections/hero-product-showcase.liquid`
- Create: `tcc-theme/sections/hero-product-showcase.liquid`

- [ ] **Step 1: Read source file**

Read `Supplement Store/sections/hero-product-showcase.liquid` (whole file).

- [ ] **Step 2: Identify dependencies**

Run: `grep -E "(render '|asset_url|inline_asset_content)" "Supplement Store/sections/hero-product-showcase.liquid"`

- [ ] **Step 3: Copy section + deps**

```bash
cp "Supplement Store/sections/hero-product-showcase.liquid" tcc-theme/sections/hero-product-showcase.liquid
# Copy each dependency
```

- [ ] **Step 4: Strip Curably refs**

In the new file:
- Find/replace `curably-` → `tcc-`
- Find any literal Curably copy in default schema settings → replace with TCC defaults:
  - Eyebrow: `"Practitioner-Grade · Clinically Studied"`
  - Headline: `"Clear arteries. Restored circulation."`
  - Subhead: `"Pharmaceutical-strength nattokinase, formulated by clinicians for the cardiovascular health you deserve."`
  - CTA: `"Shop the Protocol"`
  - CTA link: `"/products/spike-detox"`

- [ ] **Step 5: Retokenize CSS**

Apply token replacement table to inline `<style>`.

- [ ] **Step 6: Commit**

```bash
git add tcc-theme/sections/hero-product-showcase.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(home): port hero-product-showcase"
```

### Task 3.2: Port `scrolling-promo-ticker`

**Files:**
- Read: `Supplement Store/sections/scrolling-promo-ticker.liquid`
- Create: `tcc-theme/sections/scrolling-promo-ticker.liquid`

- [ ] **Step 1: Read source file**

Read `Supplement Store/sections/scrolling-promo-ticker.liquid` (whole file).

- [ ] **Step 2: Identify dependencies**

Run: `grep -E "(render '|asset_url|inline_asset_content)" "Supplement Store/sections/scrolling-promo-ticker.liquid"`

- [ ] **Step 3: Copy section + deps**

```bash
cp "Supplement Store/sections/scrolling-promo-ticker.liquid" tcc-theme/sections/scrolling-promo-ticker.liquid
# Copy each dependency
```

- [ ] **Step 4: Strip Curably refs**

Find/replace `curably-` → `tcc-`. Replace default item text with TCC trust badges:

```
"Practitioner-Formulated"
"Independently Tested"
"FDA-Registered Facility"
"GMP Certified"
"Made in the USA"
"Vegan · Non-GMO"
```

- [ ] **Step 5: Retokenize CSS**

Apply token replacement table.

- [ ] **Step 6: Commit**

```bash
git add tcc-theme/sections/scrolling-promo-ticker.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(home): port scrolling-promo-ticker with TCC trust badges"
```

### Task 3.3: Port `benefits-grid`

**Files:**
- Read: `Supplement Store/sections/benefits-grid.liquid`
- Create: `tcc-theme/sections/benefits-grid.liquid`

- [ ] **Step 1: Read source file**

Read `Supplement Store/sections/benefits-grid.liquid` (whole file).

- [ ] **Step 2: Identify dependencies**

Run: `grep -E "(render '|asset_url|inline_asset_content)" "Supplement Store/sections/benefits-grid.liquid"`

- [ ] **Step 3: Copy section + deps**

```bash
cp "Supplement Store/sections/benefits-grid.liquid" tcc-theme/sections/benefits-grid.liquid
# Copy each dependency
```

- [ ] **Step 4: Strip Curably refs and write TCC default block content**

Find/replace `curably-`. Update default block content to 4 nattokinase benefits:

| Block # | Title | Subtitle |
|---|---|---|
| 1 | Clears Fibrin Deposits | The body's natural mechanism for cardiovascular cleanup |
| 2 | Supports Healthy Circulation | Studied to improve blood flow and arterial elasticity |
| 3 | Backed by 40 Years of Research | Discovered in Tokyo, 1980; studied across 200+ clinical trials |
| 4 | Practitioner-Grade Dosing | 4,000 FU per serving — what your clinician would prescribe |

- [ ] **Step 5: Retokenize CSS**

Apply token replacement table.

- [ ] **Step 6: Commit**

```bash
git add tcc-theme/sections/benefits-grid.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(home): port benefits-grid with nattokinase benefit copy"
```

### Task 3.4: Port `brand-comparison-table`

**Files:**
- Read: `Supplement Store/sections/brand-comparison-table.liquid`
- Create: `tcc-theme/sections/brand-comparison-table.liquid`

- [ ] **Step 1: Read source file**

Read `Supplement Store/sections/brand-comparison-table.liquid` (whole file).

- [ ] **Step 2: Identify dependencies**

Run: `grep -E "(render '|asset_url|inline_asset_content)" "Supplement Store/sections/brand-comparison-table.liquid"`

- [ ] **Step 3: Copy section + deps**

```bash
cp "Supplement Store/sections/brand-comparison-table.liquid" tcc-theme/sections/brand-comparison-table.liquid
# Copy each dependency
```

- [ ] **Step 4: Strip Curably refs and write TCC comparison rows**

Find/replace `curably-`. Update default row content. Comparison column 1 = "{{ settings.brand_short }}", column 2 = "Typical Store-Bought":

| Row label | TCC | Typical |
|---|---|---|
| Nattokinase Dose (per serving) | 4,000 FU | 1,000–2,000 FU |
| Fermentation Method | Wild-fermented Bacillus subtilis | Lab-cultured |
| Third-Party Tested | ✓ Every batch | ✗ Rarely |
| Vitamin K2 Removed | ✓ (safety for blood-thinner users) | ✗ Often present |
| Vegan Capsules | ✓ | Mixed |
| Practitioner-Formulated | ✓ | ✗ |

- [ ] **Step 5: Retokenize CSS**

Apply token replacement. The "✓" / "✗" should use `var(--tcc-positive)` and `var(--tcc-urgent)` respectively.

- [ ] **Step 6: Commit**

```bash
git add tcc-theme/sections/brand-comparison-table.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(home): port brand-comparison-table with TCC vs typical nattokinase rows"
```

### Task 3.5: Port `bottom-faq`

**Files:**
- Read: `Supplement Store/sections/bottom-faq.liquid`
- Create: `tcc-theme/sections/bottom-faq.liquid`

- [ ] **Step 1: Read source file**

Read `Supplement Store/sections/bottom-faq.liquid` (whole file).

- [ ] **Step 2: Identify dependencies**

Run: `grep -E "(render '|asset_url|inline_asset_content)" "Supplement Store/sections/bottom-faq.liquid"`

- [ ] **Step 3: Copy section + deps**

```bash
cp "Supplement Store/sections/bottom-faq.liquid" tcc-theme/sections/bottom-faq.liquid
# Copy each dependency
```

- [ ] **Step 4: Strip Curably refs and write TCC FAQ content**

Find/replace `curably-`. Update default FAQ blocks with these 8 questions (mined from r/ClotSurvivors, r/covidlonghaulers, r/Biohackers nattokinase discussions):

1. **Q: What is nattokinase?**
   A: Nattokinase is an enzyme extracted from natto, a traditional Japanese fermented soybean food. Discovered in Tokyo in 1980 by Dr. Hiroyuki Sumi, it's been studied for its effect on fibrin — a protein involved in blood clotting and arterial plaque.

2. **Q: How is this different from store-bought nattokinase?**
   A: Most store-bought nattokinase is dosed at 1,000–2,000 FU per serving. The Clinician Company formulates at 4,000 FU — the dose practitioners actually use in clinical practice. Every batch is third-party tested and vitamin K2 is removed for safety with blood thinners.

3. **Q: Is it safe to take with blood thinners?**
   A: Talk to your prescribing physician first. Our formulation removes vitamin K2 (which interferes with warfarin) but nattokinase itself has anti-coagulant effects. This is a conversation for your care team.

4. **Q: How long until I notice a difference?**
   A: Most clinical studies measure outcomes over 8–12 weeks. Fibrin clearance and circulation markers shift gradually, not overnight.

5. **Q: When should I take it?**
   A: On an empty stomach, ideally before bed or 30 minutes before a meal. Food can reduce enzyme activity.

6. **Q: Is this FDA-approved?**
   A: Dietary supplements are not FDA-approved; they're regulated differently from drugs. Our manufacturing facility is FDA-registered and follows GMP standards. {{ settings.brand_claim_disclaimer }}

7. **Q: What's your refund policy?**
   A: 60-day money-back guarantee. If you're not satisfied for any reason, email support@theclinicianco.com for a full refund.

8. **Q: Is this third-party tested?**
   A: Yes — every batch is independently tested for potency (FU activity), purity (heavy metals, microbials), and label accuracy. Certificates of Analysis available on request.

- [ ] **Step 5: Retokenize CSS**

Apply token replacement table.

- [ ] **Step 6: Commit**

```bash
git add tcc-theme/sections/bottom-faq.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(home): port bottom-faq with 8 corpus-mined questions"
```

### Task 3.6: Assemble `templates/index.json` with the 5 homepage sections

**Files:**
- Modify: `tcc-theme/templates/index.json`

- [ ] **Step 1: Write the template with sections in order**

Replace `tcc-theme/templates/index.json` contents with:

```json
{
  "sections": {
    "hero": {
      "type": "hero-product-showcase",
      "settings": {}
    },
    "trust": {
      "type": "scrolling-promo-ticker",
      "settings": {}
    },
    "benefits": {
      "type": "benefits-grid",
      "settings": {}
    },
    "comparison": {
      "type": "brand-comparison-table",
      "settings": {}
    },
    "faq": {
      "type": "bottom-faq",
      "settings": {}
    }
  },
  "order": ["hero", "trust", "benefits", "comparison", "faq"]
}
```

- [ ] **Step 2: Smoke test homepage**

Reload preview at the dev URL. The homepage should now render all 5 sections top-to-bottom with TCC branding. Check:
- Hero shows headline + CTA
- Trust ticker scrolls horizontally
- Benefits grid shows 4 tiles
- Comparison table shows TCC vs Typical
- FAQ shows 8 expandable items
- No console errors
- Mobile (375px) layout doesn't break

- [ ] **Step 3: Commit**

```bash
git add tcc-theme/templates/index.json
git commit -m "feat(home): assemble homepage template with 5 sections"
```

---

## Phase 4 — PDP

Goal: The `spike-detox` product page renders the buybox + media gallery + 5 trust sections, with bundle pricing (1/3/6) and subscribe-vs-onetime toggle.

### Task 4.1: Port `main-product` section

**Files:**
- Read: `Supplement Store/sections/main-product.liquid`
- Create: `tcc-theme/sections/main-product.liquid`

- [ ] **Step 1: Read source file**

Read `Supplement Store/sections/main-product.liquid` (whole file). This is the primary product container — likely large.

- [ ] **Step 2: Identify dependencies**

Run: `grep -E "(render '|asset_url|inline_asset_content|section '|content_for)" "Supplement Store/sections/main-product.liquid"`
There will be many. List all snippets and asset references.

- [ ] **Step 3: Copy section + all deps**

```bash
cp "Supplement Store/sections/main-product.liquid" tcc-theme/sections/main-product.liquid
# Copy every dependency identified
```

- [ ] **Step 4: Strip Curably refs**

Find/replace `curably-` → `tcc-`. Replace literal Curably copy with `{{ settings.brand_name }}` references.

- [ ] **Step 5: Retokenize inline CSS**

Apply token replacement table.

- [ ] **Step 6: Commit**

```bash
git add tcc-theme/sections/main-product.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(pdp): port main-product section container"
```

### Task 4.2: Port `product-details-buybox` (bundle + subscribe support)

**Files:**
- Read: `Supplement Store/sections/product-details-buybox.liquid`
- Create: `tcc-theme/sections/product-details-buybox.liquid`

- [ ] **Step 1: Read source file**

Read `Supplement Store/sections/product-details-buybox.liquid` (whole file). This is the most complex section — buybox with pricing, variants, ATC.

- [ ] **Step 2: Identify dependencies**

Run: `grep -E "(render '|asset_url|inline_asset_content)" "Supplement Store/sections/product-details-buybox.liquid"`

- [ ] **Step 3: Copy section + deps**

```bash
cp "Supplement Store/sections/product-details-buybox.liquid" tcc-theme/sections/product-details-buybox.liquid
# Copy every dependency
```

- [ ] **Step 4: Strip Curably refs**

Find/replace `curably-` → `tcc-`. Replace any literal Curably / Aged Garlic references with TCC / nattokinase appropriate copy.

- [ ] **Step 5: Verify bundle pricing block schema is intact**

In the section schema (`{% schema %}` block at bottom), confirm there are blocks for bundle tiers (e.g., type `bundle_option` or similar). If the names are Curably-specific (e.g., `garlic_bundle_1`), generalize them (`bundle_1`, `bundle_3`, `bundle_6`).

- [ ] **Step 6: Retokenize inline CSS**

Apply token replacement table.

- [ ] **Step 7: Commit**

```bash
git add tcc-theme/sections/product-details-buybox.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(pdp): port product-details-buybox with bundle pricing structure"
```

### Task 4.3: Port `product-media-gallery`

**Files:**
- Read: `Supplement Store/sections/product-media-gallery.liquid`
- Create: `tcc-theme/sections/product-media-gallery.liquid`

- [ ] **Step 1: Read source file**

Read `Supplement Store/sections/product-media-gallery.liquid` (whole file).

- [ ] **Step 2: Identify deps and copy**

```bash
grep -E "(render '|asset_url|inline_asset_content)" "Supplement Store/sections/product-media-gallery.liquid"
cp "Supplement Store/sections/product-media-gallery.liquid" tcc-theme/sections/product-media-gallery.liquid
# Copy deps
```

- [ ] **Step 3: Strip Curably refs**

Find/replace `curably-` → `tcc-`.

- [ ] **Step 4: Retokenize CSS**

Apply token replacement table.

- [ ] **Step 5: Commit**

```bash
git add tcc-theme/sections/product-media-gallery.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(pdp): port product-media-gallery"
```

### Task 4.4: Port `sticky-atc-bar`

**Files:**
- Read: `Supplement Store/sections/sticky-atc-bar.liquid`
- Create: `tcc-theme/sections/sticky-atc-bar.liquid`

- [ ] **Step 1: Read source + identify deps**

Read file; run grep for renders/assets.

- [ ] **Step 2: Copy section + deps**

```bash
cp "Supplement Store/sections/sticky-atc-bar.liquid" tcc-theme/sections/sticky-atc-bar.liquid
# Copy deps
```

- [ ] **Step 3: Strip Curably refs and retokenize**

Find/replace `curably-` → `tcc-`. Apply token replacement to inline CSS.

- [ ] **Step 4: Commit**

```bash
git add tcc-theme/sections/sticky-atc-bar.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(pdp): port sticky-atc-bar"
```

### Task 4.5: Port `research-highlights` and write copy with citations

**Files:**
- Read: `Supplement Store/sections/research-highlights.liquid`
- Create: `tcc-theme/sections/research-highlights.liquid`

- [ ] **Step 1: Read source file + identify deps**

Read file; run grep.

- [ ] **Step 2: Copy section + deps**

```bash
cp "Supplement Store/sections/research-highlights.liquid" tcc-theme/sections/research-highlights.liquid
# Copy deps
```

- [ ] **Step 3: Strip Curably refs**

Find/replace `curably-` → `tcc-`.

- [ ] **Step 4: Write TCC research blocks (default schema content)**

Replace default block content with 3 real citations from the corpus:

**Block 1:**
- Headline: "Frontiers in Cardiovascular Medicine"
- Body: "Nattokinase demonstrated significant fibrinolytic activity in human subjects, reducing arterial plaque volume in carotid imaging studies."
- Citation: "Frontiers in Cardiovascular Medicine, 2022 — PMC9 fcvm.09.964977"

**Block 2:**
- Headline: "Molecules Journal Review"
- Body: "Comprehensive review of nattokinase's mechanism: dissolves fibrin, inhibits platelet aggregation, and lowers blood pressure across multiple human trials."
- Citation: "Molecules, 2022 — PMC molecules.27.05405"

**Block 3:**
- Headline: "Cureus — Post-Acute COVID Sequelae"
- Body: "Nattokinase included in a clinical-protocol approach to post-acute sequelae following COVID-19, targeting microclot pathology."
- Citation: "Cureus, 2024 — Article 207654"

- [ ] **Step 5: Retokenize CSS**

Apply token replacement table.

- [ ] **Step 6: Commit**

```bash
git add tcc-theme/sections/research-highlights.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(pdp): port research-highlights with 3 real citations"
```

### Task 4.6: Port `money-back-guarantee`

**Files:**
- Read: `Supplement Store/sections/money-back-guarantee.liquid`
- Create: `tcc-theme/sections/money-back-guarantee.liquid`

- [ ] **Step 1: Read source + deps**

Read file; run grep.

- [ ] **Step 2: Copy + strip Curably refs**

```bash
cp "Supplement Store/sections/money-back-guarantee.liquid" tcc-theme/sections/money-back-guarantee.liquid
# Copy deps
```

Find/replace `curably-` → `tcc-`. Update default copy:
- Headline: "60-Day Money-Back Guarantee"
- Body: "Try {{ settings.brand_name }} for 60 days. If you're not satisfied for any reason — even if the bottle is empty — email us for a full refund. No questions asked."

- [ ] **Step 3: Retokenize CSS**

Apply token replacement.

- [ ] **Step 4: Commit**

```bash
git add tcc-theme/sections/money-back-guarantee.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(pdp): port money-back-guarantee"
```

### Task 4.7: Port `text-testimonials-slider` with placeholder testimonials

**Files:**
- Read: `Supplement Store/sections/text-testimonials-slider.liquid`
- Create: `tcc-theme/sections/text-testimonials-slider.liquid`

- [ ] **Step 1: Read source + deps**

Read file; run grep.

- [ ] **Step 2: Copy + strip Curably refs**

```bash
cp "Supplement Store/sections/text-testimonials-slider.liquid" tcc-theme/sections/text-testimonials-slider.liquid
# Copy deps
```

Find/replace `curably-` → `tcc-`.

- [ ] **Step 3: Write 6 placeholder testimonials**

Replace default testimonial blocks with 6 mirroring corpus relief-language. Mark each with `<!-- placeholder -->`:

```
1. "Finally feeling like myself again. Three months in and my morning grogginess is gone."
   — Sarah M., 54, verified buyer  <!-- placeholder -->

2. "My cardiologist asked what I was doing differently. I told him about TCC. He's now researching it."
   — Marcus K., 61, verified buyer  <!-- placeholder -->

3. "After Long Covid, I felt invisible to the medical system. This was the first thing that gave me my energy back."
   — Jennifer R., 47, verified buyer  <!-- placeholder -->

4. "I've tried other nattokinase brands. The 4,000 FU dose actually moves the needle."
   — David L., 58, verified buyer  <!-- placeholder -->

5. "My circulation issues from a clot 5 years ago — finally improving. Combined with what my doctor prescribed."
   — Patricia H., 66, verified buyer  <!-- placeholder -->

6. "Practitioner-grade is the right framing. This isn't a CVS supplement."
   — Robert T., 52, verified buyer  <!-- placeholder -->
```

- [ ] **Step 4: Retokenize CSS**

Apply token replacement.

- [ ] **Step 5: Commit**

```bash
git add tcc-theme/sections/text-testimonials-slider.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(pdp): port text-testimonials-slider with 6 placeholder testimonials"
```

### Task 4.8: Assemble `templates/product.spike-detox.json`

**Files:**
- Modify: `tcc-theme/templates/product.spike-detox.json`

- [ ] **Step 1: Write the template**

Replace `tcc-theme/templates/product.spike-detox.json` contents with:

```json
{
  "sections": {
    "main": {
      "type": "main-product",
      "blocks": {},
      "block_order": [],
      "settings": {}
    },
    "buybox": {
      "type": "product-details-buybox",
      "settings": {}
    },
    "media": {
      "type": "product-media-gallery",
      "settings": {}
    },
    "sticky": {
      "type": "sticky-atc-bar",
      "settings": {}
    },
    "research": {
      "type": "research-highlights",
      "settings": {}
    },
    "benefits": {
      "type": "benefits-grid",
      "settings": {}
    },
    "guarantee": {
      "type": "money-back-guarantee",
      "settings": {}
    },
    "testimonials": {
      "type": "text-testimonials-slider",
      "settings": {}
    },
    "comparison": {
      "type": "brand-comparison-table",
      "settings": {}
    },
    "faq": {
      "type": "bottom-faq",
      "settings": {}
    }
  },
  "order": [
    "main",
    "research",
    "benefits",
    "testimonials",
    "comparison",
    "guarantee",
    "faq",
    "sticky"
  ]
}
```

(Note: `buybox` and `media` are blocks inside `main-product`, not standalone sections. The above schema may need to be adjusted to nest them under `main`'s `blocks`. If `main-product` accepts buybox + media as inline blocks, edit `main` instead — verify schema during smoke test.)

- [ ] **Step 2: Smoke test PDP**

Visit `https://gmmehe-01.myshopify.com/products/spike-detox?preview_theme_id=XXX` (replace with the dev preview URL). Verify:
- Buybox renders with title, price, ATC button
- Media gallery shows product image (or placeholder)
- Sticky ATC appears at bottom on mobile (375px viewport)
- All trust sections render below
- No console errors

- [ ] **Step 3: Commit**

```bash
git add tcc-theme/templates/product.spike-detox.json
git commit -m "feat(pdp): assemble product.spike-detox template"
```

### Task 4.9: Configure spike-detox product variants in Shopify admin (USER ACTION)

**Files:** None — Shopify admin configuration.

- [ ] **Step 1: User opens Shopify admin**

User navigates to `https://gmmehe-01.myshopify.com/admin/products`, clicks the `spike-detox` product.

- [ ] **Step 2: Confirm or create variants**

Variant 1: "1 Bottle" — price $89.99
Variant 2: "3 Bottles" — price $239.97 (~$80/bottle, 11% off)
Variant 3: "6 Bottles" — price $419.94 (~$70/bottle, 22% off)

- [ ] **Step 3: Confirm Subscriptions app is installed (or install)**

Settings → Apps → Search "Shopify Subscriptions" (free, Shopify-built). Install if not present. Configure a 15% subscriber discount on the spike-detox product.

- [ ] **Step 4: Smoke test buybox renders bundle + sub options**

Reload PDP preview. Buybox should now show:
- Bundle selector (1 / 3 / 6)
- One-time-vs-subscribe toggle
- Price updates per selection

If not rendering, the `product-details-buybox.liquid` block schema may need its bundle blocks configured in the theme editor — open the theme editor at the preview URL, navigate to the spike-detox PDP, and add three "bundle option" blocks.

- [ ] **Step 5: Note in commit log (no code commit)**

This is a manual-config task. Note completion in chat to user.

---

## Phase 5 — Cart Drawer

Goal: Cart drawer slides in from the right when an item is added; shows line items, subtotal, checkout CTA.

### Task 5.1: Port `cart-drawer` and helper snippets

**Files:**
- Read: `Supplement Store/sections/cart-drawer.liquid`
- Create: `tcc-theme/sections/cart-drawer.liquid`
- Read: `Supplement Store/sections/cart-icon-bubble.liquid`
- Create: `tcc-theme/sections/cart-icon-bubble.liquid`
- Read: `Supplement Store/sections/cart-notification-button.liquid`
- Create: `tcc-theme/sections/cart-notification-button.liquid`
- Read: `Supplement Store/sections/cart-notification-product.liquid`
- Create: `tcc-theme/sections/cart-notification-product.liquid`
- Read: `Supplement Store/sections/cart-live-region-text.liquid`
- Create: `tcc-theme/sections/cart-live-region-text.liquid`

- [ ] **Step 1: Read all source files**

Read all 5 files listed above.

- [ ] **Step 2: Identify deps across all 5 files**

Run:
```bash
grep -hE "(render '|asset_url|inline_asset_content)" \
  "Supplement Store/sections/cart-drawer.liquid" \
  "Supplement Store/sections/cart-icon-bubble.liquid" \
  "Supplement Store/sections/cart-notification-button.liquid" \
  "Supplement Store/sections/cart-notification-product.liquid" \
  "Supplement Store/sections/cart-live-region-text.liquid" | sort -u
```

- [ ] **Step 3: Copy all 5 sections + deps**

```bash
for f in cart-drawer cart-icon-bubble cart-notification-button cart-notification-product cart-live-region-text; do
  cp "Supplement Store/sections/$f.liquid" "tcc-theme/sections/$f.liquid"
done
# Copy each dep from Step 2
```

- [ ] **Step 4: Strip Curably refs across all 5**

For each: find/replace `curably-` → `tcc-`. Replace any literal Curably copy.

- [ ] **Step 5: Retokenize CSS in each**

Apply token replacement table to inline `<style>` blocks in all 5 files.

- [ ] **Step 6: Smoke test cart**

Reload preview. Add the spike-detox product to cart from the PDP. Cart drawer should slide in from the right showing the line item with subtotal and Checkout button. No console errors.

- [ ] **Step 7: Commit**

```bash
git add tcc-theme/sections/cart-*.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(cart): port cart-drawer + 4 helper sections"
```

---

## Phase 6 — Collections

Goal: A collections page renders with banner + product grid.

### Task 6.1: Port `main-collection-banner`

**Files:**
- Read: `Supplement Store/sections/main-collection-banner.liquid`
- Create: `tcc-theme/sections/main-collection-banner.liquid`

- [ ] **Step 1: Read source + deps**

Read file; run grep.

- [ ] **Step 2: Copy + strip Curably refs**

```bash
cp "Supplement Store/sections/main-collection-banner.liquid" tcc-theme/sections/main-collection-banner.liquid
# Copy deps
```

Find/replace `curably-` → `tcc-`.

- [ ] **Step 3: Retokenize CSS**

Apply token replacement table.

- [ ] **Step 4: Commit**

```bash
git add tcc-theme/sections/main-collection-banner.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(collections): port main-collection-banner"
```

### Task 6.2: Port `main-collection-product-grid`

**Files:**
- Read: `Supplement Store/sections/main-collection-product-grid.liquid`
- Create: `tcc-theme/sections/main-collection-product-grid.liquid`

- [ ] **Step 1: Read source + deps**

Read file; run grep.

- [ ] **Step 2: Copy + strip Curably refs**

```bash
cp "Supplement Store/sections/main-collection-product-grid.liquid" tcc-theme/sections/main-collection-product-grid.liquid
# Copy deps
```

Find/replace `curably-` → `tcc-`.

- [ ] **Step 3: Retokenize CSS**

Apply token replacement table.

- [ ] **Step 4: Commit**

```bash
git add tcc-theme/sections/main-collection-product-grid.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(collections): port main-collection-product-grid"
```

### Task 6.3: Assemble `templates/collection.json`

**Files:**
- Modify: `tcc-theme/templates/collection.json`

- [ ] **Step 1: Write the template**

Replace `tcc-theme/templates/collection.json` contents with:

```json
{
  "sections": {
    "banner": {
      "type": "main-collection-banner",
      "settings": {}
    },
    "grid": {
      "type": "main-collection-product-grid",
      "settings": {}
    }
  },
  "order": ["banner", "grid"]
}
```

- [ ] **Step 2: Smoke test**

Visit `https://gmmehe-01.myshopify.com/collections/all?preview_theme_id=XXX`. Banner + product grid should render. Spike Detox product should appear in the grid (assuming it's published to "all" collection by default in Shopify).

- [ ] **Step 3: Commit**

```bash
git add tcc-theme/templates/collection.json
git commit -m "feat(collections): assemble collection template"
```

---

## Phase 7 — About Page

Goal: An About page renders with founder/practitioner story blocks plus a closing mission statement, all using corpus-mined copy.

### Task 7.1: Port `image-with-text`

**Files:**
- Read: `Supplement Store/sections/image-with-text.liquid`
- Create: `tcc-theme/sections/image-with-text.liquid`

- [ ] **Step 1: Read source + deps**

Read file; run grep.

- [ ] **Step 2: Copy + strip Curably refs + retokenize**

```bash
cp "Supplement Store/sections/image-with-text.liquid" tcc-theme/sections/image-with-text.liquid
# Copy deps
```

Find/replace `curably-` → `tcc-`. Apply token replacement.

- [ ] **Step 3: Commit**

```bash
git add tcc-theme/sections/image-with-text.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(about): port image-with-text"
```

### Task 7.2: Port `rich-text`

**Files:**
- Read: `Supplement Store/sections/rich-text.liquid`
- Create: `tcc-theme/sections/rich-text.liquid`

- [ ] **Step 1: Read + deps**

Read file; run grep.

- [ ] **Step 2: Copy + strip + retokenize**

```bash
cp "Supplement Store/sections/rich-text.liquid" tcc-theme/sections/rich-text.liquid
# Copy deps
```

Find/replace `curably-` → `tcc-`. Apply token replacement.

- [ ] **Step 3: Commit**

```bash
git add tcc-theme/sections/rich-text.liquid tcc-theme/snippets/ tcc-theme/assets/
git commit -m "feat(about): port rich-text"
```

### Task 7.3: Assemble `templates/page.about.json` with corpus-mined copy

**Files:**
- Modify: `tcc-theme/templates/page.about.json`

- [ ] **Step 1: Write the template with 3 sections (founder, practitioner-advisory, mission)**

Replace `tcc-theme/templates/page.about.json` contents with:

```json
{
  "sections": {
    "founder": {
      "type": "image-with-text",
      "settings": {
        "heading": "Why we built The Clinician Company",
        "text": "<p>For decades, patients with chronic illness — long Covid, post-clot recovery, hypertension, post-vaccine syndromes — have been told to wait, dismissed, or handed prescriptions that didn't address the root cause. We watched them search for answers in Reddit threads at 2 a.m. We watched them feel invisible.</p><p>The Clinician Company started as a question: what if the supplements that practitioners actually trust — the ones they reach for in their own clinics — were available without an appointment, at clinical doses, with the same testing rigor a hospital would demand?</p>",
        "image_position": "left",
        "heading_size": "h1"
      }
    },
    "advisory": {
      "type": "image-with-text",
      "settings": {
        "heading": "Practitioner-formulated. Independently verified.",
        "text": "<p>Every TCC formulation is reviewed by practicing functional-medicine clinicians before it ships. Every batch is third-party tested for potency, purity, and label accuracy. Certificates of Analysis are available on request.</p><p>Our hero formulation, <strong>Spike Detox</strong>, is built around nattokinase — an enzyme discovered in Tokyo in 1980 by Dr. Hiroyuki Sumi, studied across hundreds of clinical trials. We dose it at 4,000 FU per serving — what your clinician would prescribe — not the 1,000 FU most retail brands use to keep margins fat.</p>",
        "image_position": "right",
        "heading_size": "h1"
      }
    },
    "mission": {
      "type": "rich-text",
      "settings": {
        "heading": "Our mission",
        "text": "<p>To put practitioner-grade interventions in the hands of the people who need them most — and to treat them like the intelligent adults they already are.</p><p style=\"font-size: 13px; color: var(--tcc-ink-soft); margin-top: 32px;\">Selected references: <em>Frontiers in Cardiovascular Medicine</em>, 2022 (PMC 9.964977); <em>Molecules</em>, 2022 (PMC 27.05405); <em>Cureus</em>, 2024 (Article 207654 — clinical approach to post-acute COVID sequelae).</p>"
      }
    }
  },
  "order": ["founder", "advisory", "mission"]
}
```

- [ ] **Step 2: Create the actual /pages/about page in Shopify admin (USER ACTION)**

User opens Shopify admin → Online Store → Pages → Add page → Title: "About", Handle: `about`. Save. Set template to "page.about" in the dropdown.

- [ ] **Step 3: Smoke test**

Visit `https://gmmehe-01.myshopify.com/pages/about?preview_theme_id=XXX`. Three sections should render in order with the about copy.

- [ ] **Step 4: Commit**

```bash
git add tcc-theme/templates/page.about.json
git commit -m "feat(about): assemble about template with corpus-informed copy"
```

---

## Phase 8 — Polish

Goal: Theme passes shopify-cli linting, mobile QA reveals no broken layouts, no leftover Curably strings anywhere.

### Task 8.1: Run `shopify theme check` and fix any errors

**Files:** Various (errors will indicate)

- [ ] **Step 1: Run linter**

Run from `tcc-theme/`: `shopify theme check`
Expected: prints a list of warnings/errors per file.

- [ ] **Step 2: Fix all errors (severity: error)**

For each error:
- Read the file
- Fix the issue (often: missing translation, syntax error, deprecated tag)
- Re-run `shopify theme check`

- [ ] **Step 3: Triage warnings**

Warnings are OK to defer for v1 unless they indicate broken functionality. Note any deferred warnings for follow-up.

- [ ] **Step 4: Commit any fixes**

```bash
git add tcc-theme/
git commit -m "fix(theme): resolve shopify theme check errors"
```

### Task 8.2: Mobile QA at 749px breakpoint

**Files:** None — verification only.

- [ ] **Step 1: Open every v1 page at mobile viewport (375px)**

In browser DevTools, set viewport to 375x812 (iPhone 11). Visit each:
- `/` (homepage)
- `/products/spike-detox` (PDP)
- `/cart` (cart page) and add-to-cart from PDP for cart-drawer
- `/collections/all` (collection)
- `/pages/about` (about)

For each: scroll top-to-bottom, check no horizontal scroll, no overlapping text, sticky ATC visible on PDP, cart drawer opens correctly.

- [ ] **Step 2: Open every v1 page at desktop viewport (1440px)**

Same pages, same checks at 1440x900.

- [ ] **Step 3: Document and fix any breakage**

For any broken layout, identify the section, fix the CSS (using tokens), commit per fix:

```bash
git add tcc-theme/sections/<file>
git commit -m "style(<section>): fix <breakpoint> layout breakage"
```

### Task 8.3: Cross-page token-consistency sweep

**Files:** Various (sweep will identify)

- [ ] **Step 1: Grep for hardcoded hex colors in tcc-theme/sections/**

Run: `grep -rE "#[0-9a-fA-F]{3,6}\b" tcc-theme/sections/ tcc-theme/snippets/ | grep -v "tcc-tokens"`
Expected: minimal output. Each match is a possible un-tokenized color.

- [ ] **Step 2: Grep for hardcoded font sizes ≥ 24px**

Run: `grep -rEi "font-size:\s*[0-9]{2,}px" tcc-theme/sections/ tcc-theme/snippets/ | grep -vE "(13px|14px|15px|16px|17px|18px|19px|20px|21px|22px|23px|var\()"`
Expected: minimal output (small body sizes are OK as literals).

- [ ] **Step 3: Grep for any leftover "curably" or "Curably" strings**

Run: `grep -ri "curably" tcc-theme/`
Expected: no output.

- [ ] **Step 4: Grep for any leftover "elare" strings (Curably's original donor)**

Run: `grep -ri "elare" tcc-theme/`
Expected: no output.

- [ ] **Step 5: Fix any hits from Steps 1-4**

For each hit, edit the file and replace with the appropriate token or brand string. Commit per fix:

```bash
git add tcc-theme/<file>
git commit -m "style(theme): tokenize remaining <thing> in <file>"
```

- [ ] **Step 6: Final smoke test of all 5 pages**

Reload each page once more. Confirm no regressions from polish edits.

- [ ] **Step 7: Final commit (if anything left)**

```bash
git add tcc-theme/
git commit -m "chore(theme): v1 polish complete" --allow-empty
```

---

## Done

At this point, the v1 TCC theme is:
- Live at the dev preview URL (still unpublished — does not affect customers)
- All 5 pages render with TCC branding and corpus-informed copy
- All hardcoded brand decisions are in `tcc-tokens.css` and `settings_data.json`
- `shopify theme check` is green
- Mobile and desktop layouts verified

**To publish to live store** (separate decision, not part of v1 implementation):
```bash
cd tcc-theme/
shopify theme push --store gmmehe-01.myshopify.com --live
```

Run only when copy/photography/legal review are complete.

---

## Open items deferred from spec (Section 9)

These were noted in the spec as user-time decisions. None block the implementation plan above:

1. **Subscriptions app** — Confirm Shopify Subscriptions installed in Phase 4 Task 4.9.
2. **Product variants** — User configures in Phase 4 Task 4.9.
3. **Product photography** — User uploads to spike-detox product in Shopify admin; theme code is photography-agnostic.
4. **Real testimonials** — Replace placeholder testimonials in `tcc-theme/sections/text-testimonials-slider.liquid` once available.
5. **Press logos** — User uploads to scrolling-promo-ticker via theme editor when available.
6. **Inter or alt font** — Locked as Inter in `tcc-tokens.css`; swap by editing the `@import` URL and `--tcc-font-sans` value.
