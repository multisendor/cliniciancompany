# PDP Build Status — Pure + Complete (Spike Detox)

**Started:** 2026-05-04
**Builder:** Claude Code agent (Opus 4.7, 1M context)
**Working tree:** `/Users/jonas/Documents/IDE/Health Store/` (git: `main`)

## Phase 1 — Copy + reorder + hero

### Decisions
- **Hero pattern (b)**: kept `media + buybox` as separate top-of-page sections. Reorganized buybox to render the 4 stat bullets as a top-of-buybox block via a new `stat_bullets` block type (stat label + sublabel). Reason: `hero-product-showcase.liquid` does not natively render the product gallery + buybox bundle config + stat bullets together. Forking media+buybox into a single combined section would require ~600 lines of new code and would risk regressions on the Kaching bundle widget JS, sticky ATC JS, and price-sync MutationObserver — all of which currently live inside `product-details-buybox.liquid`. Option (b) is cheaper and safer.
- Removed/softened the hardcoded testimonial in `product-details-buybox.liquid` per audit rule #10.

### Audit-rule compliance
(populated at end of phase)

## Phase 2 — Three new sections

**Status:** complete.

Sections built:
- `tcc-theme/sections/coa-transparency.liquid` — large card with heading + sub + lookup form (input + button) + 3 chips (Identity, Potency, Heavy metals). Right side: CSS-only navy circular "COA Published" seal with conic-gradient ring. Mobile collapses to single column with seal moved above text.
- `tcc-theme/sections/sumi-origin-story.liquid` — 2-col layout. Left visual: dark navy block with "TOKYO 1980" wordmark, corner ticks, and faint petri-dish circle motifs (no fake person photo). Right: eyebrow, H2, 3 paragraphs, pull quote with left ink-rule. Layout selectable image-first / text-first.
- `tcc-theme/sections/hsa-fsa-eligible.liquid` — single-line strip with medical-cross icon in a circular badge, primary text + soft note, optional link. Top + bottom border; sits between sections at low visual weight.

Wiring:
- `templates/product.spike-detox.json`: added 3 new section blocks (coa, origin, hsa) and inserted them in `order` after `stats` (positions: stats → coa → origin → hsa → faq), per blueprint.

Phase 5 polish folded in (cheap items only):
- **5.4 AFib FAQ entry** — added `f3b` block (between f3 and f4) titled "I'm on Eliquis (or Xarelto / warfarin) for AFib. Can I take this?" Honest framing per audit rules: complementary use under physician supervision, not substitution. References Elahi 2015 substitution-warning case. Block_order updated.
- **5.3 Hero credibility row** — done. Added optional `show_credibility_row` toggle + 4 chip-text settings to `product-details-buybox.liquid`. Chips render in a 4-column grid (2-col on mobile) directly under the title divider. Chip defaults: 60-day money-back · Third-party tested · K2-free for blood-thinner users · Made in USA, GMP-certified. Wired `show_credibility_row: true` into the Pure template buybox settings.

Tokens used: only existing `--tcc-*` variables. No new fonts, colors, or type sizes invented.

Audit-rule sanity: no new "pharmaceutical-grade" / placebo wedge / borrowed authority / RN-MD credentials introduced. AFib FAQ uses structure-function language and physician-supervision framing.

## Phase 3 — Complete PDP fork

(pending)

## Phase 4 — Visual QA via Playwright

(pending)
