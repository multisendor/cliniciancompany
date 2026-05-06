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

(pending)

## Phase 3 — Complete PDP fork

(pending)

## Phase 4 — Visual QA via Playwright

(pending)
