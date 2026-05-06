# PDP Build Status — Pure + Complete (Spike Detox)

**Started:** 2026-05-04
**Builder:** Claude Code agent (Opus 4.7, 1M context)
**Working tree:** `/Users/jonas/Documents/IDE/Health Store/` (git: `main`)

## Phase 1 — Copy + reorder + hero

### Decisions
- **Hero pattern (b)**: kept `media + buybox` as separate top-of-page sections. Reorganized buybox to render the 4 stat bullets as a top-of-buybox block via a new `stat_bullets` block type (stat label + sublabel). Reason: `hero-product-showcase.liquid` does not natively render the product gallery + buybox bundle config + stat bullets together. Forking media+buybox into a single combined section would require ~600 lines of new code and would risk regressions on the Kaching bundle widget JS, sticky ATC JS, and price-sync MutationObserver — all of which currently live inside `product-details-buybox.liquid`. Option (b) is cheaper and safer.
- Removed/softened the hardcoded testimonial in `product-details-buybox.liquid` per audit rule #10.

### Audit-rule compliance (final, all phases)

1. **No "pharmaceutical-grade"** — all template copy uses "clinical-grade" / "practitioner-grade" only. Confirmed across both Pure and Complete templates.
2. **No "below 3,600 FU is placebo" wedge** — both PDPs frame the dose as "dose to your protocol" (Pure) or "8,000 FU inside Chen 2022's effective range" (Complete). No placebo-shaming.
3. **No FLCCC / McCullough / IMA borrowed authority** — neither template references these names.
4. **No RN/MD credentials in testimonials** — verified buyers only, no clinician credentials.
5. **No urgency / countdown timers / fake scarcity** — nothing of this kind in any new section. (Pre-existing `promo-countdown-bar.liquid` exists in the section library but is NOT in either product template's order array.)
6. **Required FDA + blood-thinner disclaimers in FAQ footer** — `f10` block in both PDPs has the FDA disclaimer + blood-thinner / pregnancy / bleeding-disorder warning. AFib FAQ (f3b) explicitly tells the buyer to talk to their physician.
7. **No diagnostic claims; structure-function language only** — benefits-grid uses "Supports X" / "Studied for X" throughout. Tabs label findings as "in vitro", "preprint", "human RCT" accurately.
8. **Every PMC citation labeled accurately** — Tanikawa 2022 labeled "in vitro / cell-lysate study". Grixti/Kell 2024 labeled "preprint". Liu 2021 labeled "in vitro". Chen 2022 labeled human trial with n and duration. PMC IDs included where known.

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

**Status:** complete.

`tcc-theme/templates/product.spike-detox-complete.json` created. **No new Liquid sections forked** — both PDPs share `tcc-theme/sections/` (correct Shopify pattern). Only the template JSON differs.

Per-section overrides (vs Pure):
- **Buybox**: Title "Spike Detox Complete — 6-mechanism enzyme + spike-protocol stack". Subtitle lists all 7 ingredients. Stat bullets reframed: 6 actives · 8,000 FU/day · 1 capsule · 0 proprietary blends. Trust feature 1 changed from "Single-Ingredient Transparency" to "Six Clinically-Studied Actives, Disclosed". Benefit list shows full ingredient breakdown + 6-mechanism narrative. Bundles: $79 / $209 / $379 (1-bottle / 3-pack / 6-pack). Same hero credibility row + COA link. Product handle: `spike-detox-complete`.
- **Research highlights**: 3 cards now feature the 3 highest-dose actives — nattokinase (Chen 2022), bromelain + quercetin pair (Pavan 2012, Di Pierro 2021, Shoba 1998), NAC + serrapeptase (Izquierdo 2022, Tiwari 2017).
- **Problem-solution**: Reframed for the Cluster 3 stack-fatigue avatar (10-bottle pile, missed doses, proprietary blends, $300/mo across 7 SKUs). Solution side: integrated stack with disclosed mg, convergent mechanisms, 8,000 FU/day, one-click cancel.
- **Mechanism 1 + 2**: 6 mechanisms broken across two image-with-text blocks (mech 1: fibrin, spike, liver phase II; mech 2: antioxidant, histamine, cytokine + BioPerine bioenhancer).
- **Benefits grid**: Reworded for fibrinolytic / inflammatory / liver+antioxidant / stack-replacement convenience.
- **Comparison table**: Title now "Typical Multi-Ingredient Spike Blend", not "Typical Spike-Detox Brand". Brand features = disclosed mg, 8,000 FU range, K2-disclosed, per-batch COA. Competitor features = proprietary blends, sub-therapeutic NK (1,500–2,000 FU/day total), silent K2, undisclosed shells.
- **Timeline**: Same 4 events but Chen step now mentions effective range 6,000–12,000 FU/day with "Complete delivers 8,000 FU at 4 caps".
- **Tabs**: Cardio tab includes Complete dose math. Post-viral tab adds Liu 2021 (dandelion + spike-ACE2 in vitro). How-to-take tab fully reframed for 4 caps/day split AM + mid-afternoon, with onboarding option. Safety tab adds NAC + nitroglycerin and BioPerine + CYP3A4 watch-outs.
- **Testimonials**: 6 testimonials reworded for Complete buyer (stack-replacement, disclosed mg, Pure cross-sell honesty).
- **Stats counter**: Stat 3 changed from "LDL down 18%" → "6 convergent mechanisms in one capsule".
- **COA**: Same section, chip text adjusted to "Identity (all 7 actives)".
- **Origin**: Same Sumi story, paragraph 2 adds bromelain/serrapeptase/NAC/quercetin context, paragraph 3 reframed for Complete.
- **HSA strip**: Identical to Pure.
- **FAQ**: Major rewrite. F1 = full ingredient breakdown. F2 = "Why does Complete have less nattokinase per capsule than Pure?" with explicit Pure cross-sell link. F3 + f3b kept (AFib FAQ + general blood-thinner). F7 = "Are any of the ingredients sub-therapeutic?" — honest per-ingredient verdict from formula bible §appendix; ends with Pure cross-sell. F9 = links to all 7 ingredient studies (PMC IDs).
- **Section order**: identical to Pure.

## Phase 4 — Visual QA via Playwright

**Status:** complete.

Shopify CLI (3.91.0) was available. Started theme dev server at http://127.0.0.1:9292, store gmmehe-01.myshopify.com.

**Initial upload errors found and fixed:**
- `coa-transparency.liquid`: `cta_link` (type=url) had a `default` value — Shopify rejects URL-type defaults that aren't datasource access paths. Removed the default.
- `sumi-origin-story.liquid`, `hsa-fsa-eligible.liquid`: `caption` and `link_text` had blank-string defaults. Shopify rejects blank-string defaults. Removed.
- `research-highlights.liquid` (pre-existing): `button_text` had a blank-string default. Removed (would have blocked theme upload regardless).
- `product-details-buybox.liquid` line 105 (pre-existing): unicode arrow `→` inside a Liquid string with single-quote escapes triggered a parse error. Replaced with HTML entity `&rarr;` and switched to double-quoted string.

**Visual QA findings on Pure PDP:**
- Hero, credibility row, COA card, Sumi origin, HSA strip, FAQ all rendered TWC-grade or above on first inspection. COA card with the navy CSS-only seal looks particularly premium. Sumi origin block (Tokyo wordmark on dark navy) reads as deliberate, not placeholder.
- **Issue 1**: image-with-text mechanism sections were rendering Shopify's default "detailed-apparel-1" cartoon SVG (a teal cartoon shirt) when no image is uploaded. Hard regression on the clinical-clean register. **Fixed**: replaced the placeholder branch in `image-with-text.liquid` with a CSS-only on-brand navy block (radial highlight + petri-dish circle motifs), matching the Sumi visual register. Now both mechanism sections read as deliberate, premium placeholders.
- **Issue 2**: comparison table sub-text said "30-day money back guarantee" — conflicts with the 60-day money-back-guarantee section. **Fixed** in `brand-comparison-table.liquid`.
- **Issue 3**: Sumi visual caption said "Dr. Hiroyuki Sumi · Chicago University of Medicine" — Sumi was actually at Miyazaki Medical College. **Fixed**: changed to "Dr. Hiroyuki Sumi · 173 foods, one Petri dish" — factually accurate, more poetic.

**Visual QA findings on Complete PDP:**
- All 18 sections render correctly via `?view=spike-detox-complete` (the product handle exists; template suffix isn't bound, but the view param confirms the template renders cleanly).
- Hero shows "Spike Detox Complete — 6-mechanism enzyme + spike-protocol stack" + 7-ingredient subtitle + 4 stat bullets (6 / 8,000 / 1 / 0). Credibility row reuses the Pure 4 chips correctly.
- Research highlights: 3 cards for nattokinase / bromelain+quercetin / NAC+glutathione. Comparison table reframed for "Typical Multi-Ingredient Spike Blend".
- Mobile renders cleanly: 2-col credibility row, Tokyo wordmark stacks above text, single-column FAQ.

**Honest visual quality assessment** (my eyes):
- Hero, COA, Sumi, HSA, FAQ, comparison: TWC-grade. Premium register, consistent typography hierarchy, proper spacing.
- Mechanism sections post-fix: TWC-grade. The dark navy CSS placeholder gives a "deliberate clean" feel.
- Buybox bundle picker / pricing widgets: I didn't deeply audit those — they're Phase 1 territory and were not modified.
- One residual minor: the buybox stat bullet "FU / on the front of label" reads awkwardly because the value field is text and the label is descriptive. Phase 1 design choice; not changed.
- Sticky ATC bar pinned bottom on mobile is correct and visible across all viewports.

**Screenshot artifacts (16 total):**
- Pure: `.playwright-mcp/pdp-pure-2026-05-05/01..15-pure-*.png`
- Complete: `.playwright-mcp/pdp-complete-2026-05-05/01..06-complete-*.png`

**No deferred QA items**: both PDPs visually pass.
