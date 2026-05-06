# Health Store — Project Context for Claude Code

> Workspace for The Clinician Company (TCC) Shopify theme. Connected store: `gmmehe-01.myshopify.com`.

## HARD CONTRACTS (inviolable)

These are non-negotiable. Breaking them overwrites human work or creates merge conflicts that lose data.

### CONTRACT-1: ALWAYS pull before editing the theme

**Before making any edit to files under `tcc-theme/`, you MUST first pull the latest from Shopify** so changes the user or their staff made via the Shopify admin theme editor are not overwritten.

```bash
cd "/Users/jonas/Documents/IDE/Health Store/tcc-theme"
shopify theme pull --theme=<TARGET_THEME_ID> --nodelete
```

- **Default `TARGET_THEME_ID`**: the active "Spike Detox v1 Preview" theme is `161862353132` (unpublished). When this theme becomes live (or is replaced), update this number in this file in the same commit that publishes.
- The user and their staff edit via the Shopify admin theme editor in real time. **They WILL make changes between Claude Code sessions.** Pulling first preserves those.
- **`--nodelete`** is mandatory: pull should never delete local files Shopify doesn't know about (we have local-only working files like `STATUS-pdp.md`).
- After pulling, **`git status`** to see what came down. If anything came down, **commit it as a separate "sync from Shopify admin" commit** BEFORE making your own edits, so the human's work is preserved on its own commit and your edits are diffable separately.

If `shopify theme pull` is not available (CLI not installed, auth missing, network), STOP and surface the block. Do NOT edit blindly.

### CONTRACT-2: Push back to the same theme after edits

Same theme ID. After committing your edits:

```bash
cd "/Users/jonas/Documents/IDE/Health Store/tcc-theme"
shopify theme push --theme=<TARGET_THEME_ID> --nodelete --json
```

Push to the unpublished preview theme by default. **Never push to the live theme without explicit user authorization** in the same conversation.

### CONTRACT-3: Push to GitHub after every meaningful commit

Origin is `https://github.com/multisendor/cliniciancompany.git`. Push `main` after each phase, polish pass, or fix:

```bash
git push origin main
```

The user uses GitHub as backup + visibility. Don't let local commits sit unpushed.

### CONTRACT-4: Don't modify the donor theme or research output

- `Supplement Store/` is the Curably donor theme. Read-only here. It has its own git repo.
- `docs/superpowers/specs/research-*.md` and `addendum-*.md` and the strategy deliverables are read-only references for decisions. They can be ADDED to, but existing research briefs should not be silently rewritten — they are dated artifacts.
- Files under `/Users/jonas/Documents/IDE/Data Microservices/services/output/archives/` are scraped corpus data; never edit.

### CONTRACT-5: Audit rules are bound

The Spike Detox PDP is governed by a set of regulatory + audience-fit audit rules documented across the research briefs. **They survive across sessions.** Do not regress on:

- No "pharmaceutical-grade" — use "clinical-grade" or "practitioner-grade"
- No "below 3,600 FU is placebo" wedge — frame 4,000 FU as "dose to your protocol"
- No FLCCC / McCullough / IMA borrowed authority
- No RN/MD credentials in testimonials
- No urgency tactics (countdown bars, fake scarcity, hidden prices)
- No diagnostic claims; structure-function language only
- Required FDA + blood-thinner disclaimers in FAQ footer
- Lead cardiovascular framing; LC content via `content-tabs` self-select tab

Full rule list lives in `docs/superpowers/specs/research-clinical-evidence.md` and the audit context in `addendum-fidelity-phase5.md`.

---

## Project structure

```
Health Store/
├── tcc-theme/                — TCC Shopify theme (synced via Shopify CLI)
│   ├── sections/             — Liquid sections
│   ├── templates/            — JSON templates (product.spike-detox.json, product.spike-detox-complete.json, etc.)
│   ├── snippets/             — Liquid snippets
│   ├── assets/               — CSS, JS, images
│   ├── config/               — settings_data.json (theme tokens)
│   └── locales/              — i18n
├── Supplement Store/         — Curably donor theme (READ-ONLY, separate git)
├── docs/superpowers/specs/   — design specs, research briefs, strategy deliverables
├── docs/superpowers/plans/   — implementation plans
├── STATUS-pdp.md             — PDP build status tracker (per-phase notes)
└── CLAUDE.md                 — this file
```

## Sibling projects

- `/Users/jonas/Documents/IDE/Data Microservices/` — scrapers (Reddit / YouTube / TikTok / PDF / Amazon). FastAPI on port 8000, Next.js dashboard. Source of corpus consumed by Insights.
- `/Users/jonas/Documents/IDE/Insights/` — embeddings + learnings layer over the corpus. FastAPI on port 8001 when running. Build in progress in a separate Claude Code session.

## Common commands

```bash
# Pull latest from Shopify (DO THIS FIRST)
cd "/Users/jonas/Documents/IDE/Health Store/tcc-theme"
shopify theme pull --theme=161862353132 --nodelete

# Local dev preview (separate from the unpublished preview theme)
shopify theme dev --store=gmmehe-01.myshopify.com

# List themes on store
shopify theme list --store=gmmehe-01.myshopify.com --json

# Push to preview theme (after edits)
shopify theme push --theme=161862353132 --nodelete --json

# Visual QA via Playwright
# Use the public preview URL with theme override:
# https://gmmehe-01.myshopify.com?preview_theme_id=161862353132
# Specific PDPs:
# https://gmmehe-01.myshopify.com/products/spike-detox?preview_theme_id=161862353132
# https://gmmehe-01.myshopify.com/products/spike-detox-complete?preview_theme_id=161862353132
```

## Git conventions

- Commit per logical phase (not per file).
- Conventional-commits messages: `feat(pdp):`, `fix(footer):`, `polish(pdp):`, `docs:`, `chore:`.
- Pull from Shopify first → commit any incoming admin-editor changes as `sync(theme): pull from Shopify admin` BEFORE your own edits.
- Push to GitHub after each meaningful commit.

## Open questions / placeholders to address before going live

- Real product photography (currently using TWC's bottle as a placeholder)
- `/pages/coa` page for COA lookup (button in COA-transparency section currently links nowhere)
- TrueMed integration for HSA/FSA (currently shows "coming soon")
- Real customer testimonials seeded into reviews (current testimonials are softened VOC-grounded but not real verified buyers)
- Legal counsel review of "Spike Detox" product name (FTC implied-claim concern documented in `addendum-fidelity-phase5.md`)
- Real practitioner endorsement to replace the "no doctor authority block at v1" decision
