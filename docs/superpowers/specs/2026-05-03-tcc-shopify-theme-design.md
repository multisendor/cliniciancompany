# The Clinician Company — Shopify Theme Design Spec

**Date**: 2026-05-03
**Goal**: Ship a Standard v1 Shopify theme today for The Clinician Company (TCC), competing with twc.health in the cardiovascular/longevity supplement space, using the existing Curably ("Supplement Store") theme as a code donor.

---

## 1. Locked Decisions

| Decision | Value |
|---|---|
| Brand name | The Clinician Company (short: TCC) |
| Tagline | Practitioner-Grade Wellness |
| Hero product | Spike Detox (nattokinase-based; product handle `spike-detox`, already created in Shopify admin) |
| Visual direction | Clinical Modern — Function Health / Levels / Hims energy |
| Tech stack | Shopify Dawn 15.4.1 base, inherited via Curably port |
| Build approach | Hybrid B — copy Curably code, retokenize each section against a single token file before it ships |
| Scope today | Standard v1: Homepage + 1 PDP + Cart drawer + Collections + About (20 unique sections ported, 3 reused on PDP) |
| Shopify store | `gmmehe-01.myshopify.com` |
| Dev workflow | `shopify theme dev` against unpublished theme; live customer theme untouched until explicit push |

## 2. Success Criteria

1. New `tcc-theme/` folder is a runnable Shopify theme uploaded to `gmmehe-01` as an unpublished theme, previewable end-to-end.
2. All 5 pages (home, PDP for `spike-detox`, cart drawer, collection, about) render with TCC branding — no visible "Curably" or "Elare" strings anywhere.
3. Every ported section's CSS references tokens from `assets/theme-tokens.css`. Zero hardcoded hex colors or font sizes outside that file.
4. About page contains corpus-informed placeholder copy with at least 2 real medical-paper citations (PubMed/PMC/Cureus).
5. PDP buybox supports bundle pricing (1/3/6 bottles) and one-time-vs-subscribe toggle, mirroring twc.health's purchase model.
6. `shopify theme check` passes with no errors.
7. Mobile QA at the 749px breakpoint shows no obvious layout breakage on any of the 5 pages.

## 3. Architecture

### 3.1 Folder structure

The new theme lives at the top level of `Health Store/`, sibling to the Curably donor:

```
Health Store/
├── Supplement Store/   ← Curably (untouched donor)
└── tcc-theme/          ← The Clinician Company (NEW working theme)
    ├── assets/
    │   ├── theme-tokens.css       ← single source of truth for brand
    │   ├── theme-base.css         ← Curably's curably-custom.css, retokenized + renamed
    │   └── ...                    ← only ported assets, named without "curably-" prefix
    ├── blocks/
    ├── config/
    │   └── settings_data.json     ← brand strings live here
    ├── layout/
    │   └── theme.liquid           ← retokenized; loads theme-tokens.css before everything
    ├── locales/
    ├── sections/                  ← only ported sections (~17)
    ├── snippets/
    └── templates/
        ├── index.json
        ├── product.spike-detox.json
        ├── collection.json
        └── page.about.json
```

Sections not on the v1 ship list **stay in `Supplement Store/`** and are never copied. Don't drag dead code.

### 3.2 Token system — the consistency gate

Single file `assets/theme-tokens.css` defines every brand decision as a CSS custom property. The hard rule: **every ported section's CSS must reference tokens. No hardcoded hex codes, no random font sizes outside this file.** This is what enforces consistency and prevents the "varying font sizes / colors" issue.

```css
:root {
  /* Color */
  --tcc-bg: #fafbfc;
  --tcc-ink: #0a1628;            /* primary text + ui */
  --tcc-ink-soft: #4a5b78;
  --tcc-line: #e8eef5;
  --tcc-card: #ffffff;
  --tcc-positive: #00a86b;       /* ratings, checkmarks, used sparingly */
  --tcc-urgent: #c8281a;         /* sale tags, low-stock, used sparingly */

  /* Type */
  --tcc-font-sans: 'Inter', system-ui, sans-serif;
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

  /* Weight */
  --tcc-fw-body: 400;
  --tcc-fw-medium: 500;
  --tcc-fw-bold: 700;
  --tcc-fw-display: 800;

  /* Spacing — 8pt grid */
  --tcc-s-1: 8px;  --tcc-s-2: 16px;  --tcc-s-3: 24px;
  --tcc-s-4: 32px; --tcc-s-6: 48px;  --tcc-s-8: 64px;

  /* Radius / elevation */
  --tcc-radius: 6px;
  --tcc-shadow-card: 0 1px 3px rgba(10, 22, 40, 0.06);

  /* Breakpoint helper (for reference; CSS uses media queries directly) */
  --tcc-bp-mobile: 749px;
}
```

Inter is loaded via `@font-face` from Google Fonts inside `theme-tokens.css` (avoids the Shopify font-picker compatibility issue with Dawn).

### 3.3 Brand strings — `config/settings_data.json`

```json
{
  "current": {
    "brand_name": "The Clinician Company",
    "brand_short": "TCC",
    "brand_tagline": "Practitioner-Grade Wellness",
    "brand_claim_disclaimer": "These statements have not been evaluated by the Food and Drug Administration. This product is not intended to diagnose, treat, cure, or prevent any disease."
  }
}
```

All Liquid templates reference `{{ settings.brand_name }}`, `{{ settings.brand_short }}`, etc. Renaming the brand later is a one-line config edit.

## 4. Page-by-Page Composition (20 unique sections, 3 reused on PDP)

Notation: **PORT** = lift from Curably as-is, retokenize. **REUSE** = same section instance from earlier page.

### 4.1 Global (3, on every page)
| Section | Source | Notes |
|---|---|---|
| `announcement-bar` | PORT | v1 copy: "Free U.S. shipping over $50 · Practitioner-formulated" (editable in theme settings) |
| `header` | PORT | TCC wordmark left, simple horizontal nav (Shop / Science / About), cart icon right. No mega-menu, no search bar in v1 |
| `footer` | PORT | Newsletter signup, link columns (Shop / Company / Legal), payment icons row, claim disclaimer line |

### 4.2 Homepage — `templates/index.json` (5)
1. `hero-product-showcase` — hero with spike-detox front, CTA to PDP
2. `scrolling-promo-ticker` — press logos / "as seen in" / cert badges
3. `benefits-grid` — 3-4 icon+headline tiles
4. `brand-comparison-table` — TCC vs other supplements
5. `bottom-faq` — 6-8 expandable questions

### 4.3 PDP — `templates/product.spike-detox.json` (7 unique + 3 reused)
1. `main-product` — section container that hosts buybox + media gallery as inline blocks
2. `product-details-buybox` — title, price, **bundle selector (1/3/6)**, **subscribe-vs-onetime toggle**, ATC
3. `product-media-gallery` — image carousel + thumbs
4. `sticky-atc-bar` — mobile sticky add-to-cart
5. `research-highlights` — 2-3 cited studies (PubMed/PMC/Cureus from corpus)
6. `money-back-guarantee` — refund badge / promise card
7. `text-testimonials-slider` — customer quotes carousel
8. `benefits-grid` — REUSE
9. `brand-comparison-table` — REUSE
10. `bottom-faq` — REUSE

### 4.4 Cart drawer (1 logical unit)
| Section | Source | Notes |
|---|---|---|
| `cart-drawer` + helpers | PORT | Drawer slides in from right; includes `cart-icon-bubble`, `cart-notification-button`, `cart-notification-product`, `cart-live-region-text` snippets |

### 4.5 Collections — `templates/collection.json` (2)
1. `main-collection-banner` — title + description + image
2. `main-collection-product-grid` — filterable grid

### 4.6 About — `templates/page.about.json` (2)
1. `image-with-text` — used as 2 instances (founder story + practitioner advisory)
2. `rich-text` — mission statement / closing

### 4.7 Stripped from Curably (not ported)

- All blog/article sections (`featured-blog`, `main-article`, `main-blog`)
- Account flow sections (`main-account`, `main-login`, etc.) — keep Shopify defaults
- Niche unused sections (`bulk-quick-order-list`, `slideshow`, `collage`, `multirow`, `multicolumn`, `customer-survey-stats`, `feel-better-daily`, `clinical-benefits`, `clinically-studied-benefits`, `clinically-verified-stats`, `customer-survey-stats`, `email-signup-banner`, `image-banner`, `info-columns`, `problem-solution-split`, `proven-benefits`, `featured-collection`, `featured-product`, `featured-product-buybox`, `cert-badge-ticker`, `brand-trust-banner`, `animated-stats-counter`, `collapsible-content`, `collection-list`, `contact-form`, `content-tabs`, `custom-liquid`, `multicolumn`, `pickup-availability`, `predictive-search`, `quick-order-list`, `related-products`, `apps`, `newsletter`)

## 5. Branding & Copy System

### 5.1 Voice & tone

Authoritative + warm. "The smartest clinician in the room who actually has time to explain." Not gimmicky, not preachy, no exclamation marks in headlines.

### 5.2 Three corpus-anchored pillars

| Pillar | Audience signal (from corpus) | Copy treatment |
|---|---|---|
| **Practitioner-grade** | "practitioner" 219× in r/ClotSurvivors + r/covidlonghaulers | Headlines emphasize "clinician-formulated"; product cards show practitioner credentials |
| **Restoration over treatment** | heal 2904×, restore 26×, reverse 212× across YouTube transcripts | Action verbs: "clear," "restore," "renew" — never "fix" or "cure" (legal risk) |
| **Validation moment** | "finally" 1442× across Reddit | Social proof framed around the relief moment ("Finally feeling like myself") |

### 5.3 Copy sources per page

| Page | Source | Approach |
|---|---|---|
| Hero | Custom + competitor benchmark | "Clear arteries. Restored circulation." |
| PDP bullets | YouTube transcript mining + PubMed citations | Top-cited claims, 3 study refs in research-highlights |
| Comparison table | Industry research | TCC vs generic nattokinase: dose (FU/serving), fermentation method, third-party testing, vegan caps |
| FAQ | Reddit post mining | 6-8 real questions from r/ClotSurvivors, r/covidlonghaulers, r/Biohackers |
| About | `crawl/`, `pdf/` papers + corpus emotional language | Founder story: "saw too many patients dismissed" — uses gaslit/dismissed/abandoned signals. Cites Cureus, PMC, bioRxiv as credibility footnotes |
| Testimonials | Placeholder (realistic) | 6-8 quotes mirroring relief-moment language. Marked `<!-- placeholder -->` for real-review swap later |

### 5.4 Available citations (from `Data Microservices/services/output/archives/`)

- `crawl/cureus.com_articles_207654` — clinical approach to post-acute COVID sequelae
- `crawl/pubmed.ncbi.nlm.nih.gov_28763875` — nattokinase clinical trial
- `pdf/pmc.ncbi.nlm.nih.gov_fcvm_09_964977` — Frontiers in Cardiovascular Medicine
- `pdf/pmc.ncbi.nlm.nih.gov_molecules_27_05405` — Molecules journal nattokinase paper
- `pdf/local_2024_04_06_588397v1_full` — bioRxiv preprint

## 6. Implementation Order (8 phases, ~7-9 hours)

| # | Phase | Output | Est. |
|---|---|---|---|
| 1 | **Foundation** | `tcc-theme/` skeleton from selective Curably copy; `theme-tokens.css` written; brand strings in `settings_data.json`; Shopify CLI connected to `gmmehe-01`; `shopify theme dev` preview loads | 60–90 min |
| 2 | **Globals** | header, footer, announcement-bar ported + retokenized | 45 min |
| 3 | **Homepage** | 5 sections ported; `templates/index.json` assembled | 90 min |
| 4 | **PDP** | 7 unique sections ported + bundle/sub config; `templates/product.spike-detox.json` assembled; corpus-mined copy + 2-3 paper citations | 120–150 min |
| 5 | **Cart drawer** | cart-drawer + 4 helper snippets ported | 30 min |
| 6 | **Collections** | banner + grid ported; `templates/collection.json` assembled | 30 min |
| 7 | **About** | image-with-text + rich-text ported; `templates/page.about.json` assembled; corpus-informed copy written | 45 min |
| 8 | **Polish** | `shopify theme check`; mobile QA at 749px; cross-page token-consistency sweep | 60 min |

Commit per phase (8 commits) for clean rollback points.

## 7. Workflow

- Run `shopify theme dev --store gmmehe-01.myshopify.com` from `tcc-theme/` once at start of phase 1; leave running.
- Auto-syncs every save to an unpublished theme on `gmmehe-01`, returns a private preview URL with hot-reload.
- The customer-facing live theme is never touched until explicit `shopify theme push --live`.
- Each section port follows the same recipe:
  1. Copy file from `Supplement Store/sections/<file>` to `tcc-theme/sections/<file>`
  2. Grep for `{% render %}` and `asset_url` calls; copy any deps transitively
  3. Strip Curably-specific strings (rename `curably-` prefixes → `tcc-`; remove "Curably"/"curably.shop" references)
  4. Retokenize CSS — every color/font-size/spacing must reference a token from `theme-tokens.css`
  5. Smoke-test in preview URL

## 8. Risks & Mitigations

| # | Risk | Mitigation |
|---|---|---|
| 1 | Section dependencies: Curably sections render snippets and load helper JS | When porting, grep for `render` and `asset_url`; pull deps transitively. Maintain a per-section dep checklist |
| 2 | Tokenization is slow per section (~10-15 min each retokenizing) | Already factored into estimates. Accept it — it's the price of consistency |
| 3 | Inter font compatibility with Dawn font picker | Load via `@font-face` from Google Fonts in `theme-tokens.css`. Avoids Dawn's font-picker entirely |
| 4 | PDP buybox bundle/subscribe configuration depends on Shopify product variant + Subscriptions app setup | Confirm `spike-detox` product has variants for 1/3/6 bottles; Subscribe & Save needs Shopify Subscriptions or Recharge app installed (open item below) |
| 5 | Product photography may not be ready | Use a generic supplement-bottle mockup as placeholder; `<!-- replace with real photography -->` markers |
| 6 | `Health Store/` parent directory is not a git repo | Init `git` in `tcc-theme/` (or `Health Store/`) at start of phase 1 so commits work; first commit = scaffolding |

## 9. Open Items / Followups

These don't block the design but need a decision during implementation:

1. **Subscriptions app** — Is Shopify Subscriptions (built-in) installed on `gmmehe-01`, or is Recharge / another subscription app preferred? Affects PDP buybox config.
2. **Product variants for `spike-detox`** — Does the product already have 1-bottle / 3-bottle / 6-bottle variants set up? If not, we configure during phase 4.
3. **Product photography** — Real product photos available, or do we use a generic supplement-bottle mockup as placeholder?
4. **Real testimonials** — Do you have actual customer quotes yet, or do we use realistic placeholders marked for swap?
5. **Press logos** — For the trust ticker — do you have any "as seen in" logos (Forbes, Healthline, etc.), or do we use cert badges only (third-party tested, GMP, FDA-registered facility)?
6. **Inter or alt font** — Lock in Inter, or open to Söhne / Geist / IBM Plex as alternates with same Clinical Modern feel?

## 10. What This Spec Does NOT Cover

- The other ~70 Curably sections not on the v1 ship list — they stay in `Supplement Store/` and are not touched today
- Long-term portfolio brand expansion (multi-color system per product line) — that's a v2 conversation
- SEO / marketing / Klaviyo email flows / pixel setup
- Full content for all FAQ entries, all testimonials, full About prose — placeholder-tier copy only for v1
- Performance optimization beyond what Curably already does
- Legal review of medical claims / supplement marketing compliance — flagged as user responsibility before going to live theme
