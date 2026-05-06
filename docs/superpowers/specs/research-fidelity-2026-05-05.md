# Spike Detox PDP — Fidelity Comparison vs. TWC

**Date:** 2026-05-05
**Purpose:** Lock the section blueprint for the TCC Spike Detox PDP today.
**References checked:** TWC Ultimate Spike Detox · TWC homepage · Healthy Heart · Ultra NAC · Tirzepatide Gum · Longevity. Current TCC PDP (gated). Prior teardown `research-pdp-teardown.md` (2026-05-04).
**Screenshots:** `/Users/jonas/Documents/IDE/Health Store/.playwright-mcp/fidelity-2026-05-05/` (8 files; see G).

---

## A. TWC Ultimate Spike Detox — current section state (top → bottom)

The 2026-05-04 teardown captured 19 sections. Verbatim hero copy is unchanged: H1 *"Ultimate Spike Detox"* · Sub *"Official McCullough Protocol™ formula researched for spike protein detox."* · 4 stat bullets (71% / 77% / taste & smell / 120-cap). Mid-page tagline still *"Counteract the toxic effects of harmful spike protein."* Authority block still *"Detox Stronger. Bounce Back Faster."* attributing *"the only one designed and used by Dr. Peter McCullough…developer of the McCullough Base Spike Detoxification Protocol."*

**Single material update since 2026-05-04:** a **"Your Health. Your Stories." 3-card video-testimonial carousel** is now embedded INSIDE the hero gallery column (3 patients: Shaun, Gina, Taylor). The doctor authority block also now uses a video card vs. a flat photo. **Everything else stable** — pricing unchanged ($89.99 strike → $80.99 sub), Okendo widget still **303 reviews** with AI summary "last updated 5 months ago" (no fresh review velocity, suggests they're not iterating). 9 FAQs with the studies entry still expanding to **8+ raw PMC/NIH URLs**. 5-card testimonial carousel: "From Skeptic to Believer — Patricia W., RN" / "Incredible! — Carolyn E." / "Second Time Using — Michael H." / "Wow — Anne M." / "Great Results — Linda M."

Internal pricing inconsistency observed: Ivermectin + Mebendazole shows $599.99 in the Spike Detox cross-sell vs. $349.99 on the homepage best-sellers — they're mid-price-drop on the Rx side and didn't propagate.

---

## B. TWC brand-wide patterns (template vs. PDP-specific)

**B1. Hard template, not bespoke.** Spike Detox / Healthy Heart / Ultra NAC / Tirzepatide Gum share the same 9-section spine: hero+buybox → mid-page tagline → 5-card testimonial carousel → doctor authority → mini VS table → ingredient tile grid → FAQ → "You May Also Like" → Okendo widget. Per-SKU swaps: doctor (McCullough on Spike; different MD on Ultra NAC; none on Tirzepatide Gum, replaced by "Chew → Activate → Control" 3-step), ingredient grid contents, comparison rows. **Build sections, not pages.**

**B2. Design system is conservative and consistent.** Serif H1, sans body. White bg, deep navy/teal primary, cyan accents, single bright-blue CTA. Buybox: light-blue info strip, blue checks, frequency dropdown, white "Add to cart" pill, strike + current price. Sticky-ATC mobile via `sticky-atc` class. Reviews always Okendo (AI summary on top). PMC links pasted as raw URLs in FAQ. HSA/FSA via TrueMed/WellnessPay. **No urgency, no countdowns, no fake scarcity** — subscribe-default is the only conversion lever.

**B3. The authority moat is the brand's biggest defensible asset and is uniform across PDPs.** Same press strip, same 5-doctor cyan banner, same "Doctor portrait + protocol + price + ATC" block. We cannot copy the doctor — the *pattern* is reusable with our own clinician.

---

## C. Current TCC PDP state

**Live:** still gated — `/products/spike-detox` redirects to default Shopify `/password`, *"The Clinician Company — Will be opening soon…"* (see `07-current-tcc-password-desktop.png`). No live teardown possible.

**Theme code (advanced):** `tcc-theme/templates/product.spike-detox.json` already wires: `product-media-gallery`, `product-details-buybox` (bundle_option / trust_feature / benefit blocks), `content-tabs`, `research-highlights`, `benefits-grid`, `text-testimonials-slider`, `brand-comparison-table`, `money-back-guarantee`, `bottom-faq`, `sticky-atc-bar`. Reusable sections present but not wired: `wellness-timeline`, `problem-solution-split`, `animated-stats-counter`, `video-testimonials-slider`, `hero-product-showcase`, `image-with-text`. Most of TWC's blueprint is buildable from existing parts; gaps in E.

---

## D. TWC's broader brand (15-min web research)

**D1. Ads.** 60-sec TV spot **"Unexplained Health Symptoms"** (iSpot `TfgV`, CTA `tryspikedetox.com/fox15`) with **per-network LPs** (`/pages/fox-news-ultimate-spike`, `/pages/oan-ultimate-spike`, `/pages/foxb-ultimate-spike`, `/pages/news-max-ultimate-spike-detox`). Public **$6M Rumble commitment**. Meta Ad Library was 403 (login-gated).

**D2. Trustpilot ≠ on-PDP widget.** **2.8 / 5 across 582 reviews.** Complaints cluster on shipping delays, subscription cancellations not honored, refund mishandling ($50 retained on a $255 order). The 4.8★ Okendo is *product satisfaction*; 2.8 Trustpilot is *operations*. **We beat them by execution** — visible easy-cancel, real-human contact, clean 30/60-day refund. Pure ops.

**D3. Pricing unchanged since 2026-05-04.** Spike Detox still $89.99 → $80.99 sub.

**D4. Full SKU map.** Supplements: Ultimate Spike Detox $89.99 · Spike Support · **Longevity $89.99 (Liposomal Glutathione + NR, formulated by Dr. Drew Pinsky)** · Ultra NAC $64.99 · Healthy Heart $64.99 · Mind Lift $59.99 · Recharge. Rx/telehealth: Ivermectin Collection · Tirzepatide $349.99 · Tirzepatide Gum $349.99 · Low-Dose Naltrexone · Shield · Therablue · RelieveRx · Iv+Meb $349–599. Kits: Medical Emergency Kit $299.99 + Contagion / Field / First Aid / Mold & Allergy / Radiation / Travel / Kids. Wellness Farms food. 1Wellness Memberships (Select / Premier / Elite). **Overlap with Pure / Complete:** TWC has *one* spike SKU at $89.99 — no Pure or Complete tier. **Architecture gap:** Pure ~$49 (anchor below) and Complete ~$99 (above, justified by 4× nattokinase + K2 + bioperine) gives us a ladder TWC has not built.

**D5. Category direction.** New: **Tirzepatide Gum** (chewable GLP-1, 3-step framing), **RelieveRx** (chronic-inflammation Rx), **Low-Dose Naltrexone**, **Anti-Aging Face Serum** (first cosmetic SKU). TWC is laddering toward longevity + Rx-adjacent + early beauty — *not* deeper into spike. Our spike moat is defensible 6+ months.

---

## E. The fidelity gap (decision-ready)

| TWC PDP section | Visual pattern | Current TCC equivalent | Gap to close | Effort |
|---|---|---|---|---|
| Announcement bar (3-msg rotator) | Slim cyan, arrows | Not in template JSON | Add `announcement-bar` w/ 3 msgs | **S** |
| Hero gallery + buybox | 2-col, "See Ingredients" pill, video-carousel inside gallery | `product-media-gallery` + `product-details-buybox` exist | Add overlay pill; wire `video-testimonials-slider` *into* gallery column | **M** |
| Hero stat bullets | 4 quantified bullets | `trust_feature` blocks exist | Populate w/ our own stats; FTC-clean | **S** |
| Subscribe & Save toggle (default ON) | Toggle + cadence dropdown + 3 perks | Buybox supports it; verify | Confirm default = subscribe + sub-perk strip | **M** **MUST-HAVE** |
| Drug-interaction chip | Inline above buybox | Not present | Add chip — paradoxical trust signal | **S** **MUST-HAVE** |
| HSA/FSA + WellnessPay strip | Inline below buybox | Not present | Integrate TrueMed widget | **M** **MUST-HAVE** |
| "Featured On" press logos | Logo strip | Not present | Add `press-logo-strip`. Use real placements only — omit until earned | **S** |
| Mid-page tagline strip | Big serif + capsule photo | `image-with-text` exists | Wire into template w/ our positioning line | **S** |
| 5-card testimonial carousel | Author + verified + 5★ | `text-testimonials-slider` wired | Confirm 5 cards; include one credentialed (RN/MD) | **S** |
| Doctor authority block | Doctor portrait + protocol + price + ATC | Not built | Build new section. **DO-NOT-COPY:** McCullough Protocol™ language. Need real partner clinician | **L** **MUST-HAVE** (gated by content) |
| Mini VS comparison table | 5-row check/X | `brand-comparison-table` wired | Finalize rows vs. typical nattokinase blends. Don't name TWC | **S** |
| Cross-product upsell | "Safely Source X 3 Ways" | N/A until 2nd SKU | Defer to Complete launch | — |
| Ingredient tile grid (7 tiles, 1-line mechanism + Studies link) | 7-card grid | Not built (`benefits-grid` is closest, different layout) | Build new `ingredient-tile-grid`. Each tile: photo + name + mechanism + tiny PMC link | **M** **MUST-HAVE** |
| FAQ with PMC citations | Accordion w/ raw NIH URLs | `bottom-faq` wired | Wire 8+ PMC links into "studies" question. Non-negotiable for spike audience | **S** **MUST-HAVE** |
| "You May Also Like" cross-sell | 4-card row | Not present | Build `related-products` section; use Pure / Complete / Heart / future SKUs | **M** |
| "Chief Medical Board" doctor banner | Cyan banner + 5 headshots | Not present | **DO-NOT-COPY visually.** Build *our* "Clinical Advisory Board" only if we have ≥3 named MDs; else **omit** rather than fake | **L** (gated by content) |
| Okendo reviews widget | Avg + breakdown + AI summary + sortable + verified | Not installed | Install Judge.me or Okendo. Seed ≥30 reviews via launch program before un-gating | **L** **MUST-HAVE** |
| Floating "UNLOCK 15% OFF" tab | Right-edge sticky pill | Not present | Klaviyo or Privy floating tab | **S** |
| Sticky ATC mobile bar | Title + rating + ATC pinned | `sticky-atc-bar` wired | Confirm works on mobile | **S** |
| Mid-page repeat ATC + price | Authority section CTA | Not present | Add ATC into doctor authority block | **S** |
| Footer email signup | "Get 15% off now" | Likely in footer-group | Confirm wired w/ promo code | **S** |
| LegitScript verify badge | Footer trust mark | Not present | Apply for cert; embed once approved | **L** (gated by certification) |

**Pre-un-gate must-haves (table-stakes):** subscribe-default · drug-interaction chip · ingredient tile grid · FAQ with PMC citations · doctor authority block (real clinician) · reviews widget seeded with ≥30 reviews · sticky-ATC mobile · HSA/FSA strip.

**DO-NOT-COPY** (legal / brand): "McCullough Protocol™" trademarked language; "Approved by the Chief Medical Board" verbatim phrasing; TWC's specific 71% / 77% stat bullets (their tested-user data — we need our own falsifiable numbers or omit).

---

## F. Top 5 CVR additions ON TOP of TWC's blueprint

All scored for the **clinical-clean Pure aesthetic** (no urgency tricks, no Funnelish theatre).

**F1. Free-shipping threshold bar w/ progress meter.** "$X to free shipping. You're at $Y." Live updates with qty. **Lift: High** (8–15% AOV on supplements). **FTC: None.** **Fit: Strong** — utility, not theatre.

**F2. "What to expect, week by week" timeline section.** Wire existing `wellness-timeline.liquid` between ingredient grid and FAQ. Week 1 → Month 1 → Month 3 → Month 6+. Conditions toward the 3-month protocol = 3-pack or 3-month sub. **Lift: High** (drives sub take-rate, pre-empts the "is it working yet?" cancel). **FTC: Low** if framed as "what users typically report" with our-survey caveat. **Fit: Strong** — clinical, educational.

**F3. Batch COA download link in the buybox.** Tiny "Download Lot # COA (PDF)" under ATC. TWC doesn't expose COAs; the skeptical-doctor cohort opens these. **Lift: Medium absolute, High on highest-LTV cohort.** **FTC: None** — real third-party docs. **Fit: Strong** — Thorne pattern.

**F4. AFib-substituter complementary-use FAQ.** Single FAQ entry: *"Can I take this with my Eliquis / Xarelto / blood thinner?"* → *"No. Do not substitute. Talk to your cardiologist first."* Reinforces the buybox drug-interaction chip. **Lift: Low direct CVR; high LTV protection.** **FTC: ELIMINATES risk** — legally safest move on the page; matches the audit corrections. **Fit: Strong** — clinical-honest.

**F5. Hero credibility row (3 badges, pre-buybox): "Made in USA cGMP · 3rd-Party Tested · 60-Day Money-Back".** Three earned trust marks TWC does *not* show in their hero. 60-day directly addresses the Trustpilot refund-complaint pattern. **Lift: High** (5–12% ATC rate in supplement A/B tests). **FTC: None** if docs exist day one. **Fit: Strong** — earned, no urgency.

**Rejected:** live-purchase ticker (FTC fake-activity target) · exit-intent $-off modal (cheapens anchor) · countdown timers / fake low-stock (incompatible with audience) · "Substitute your Eliquis" callout — reframed in F4 as the *opposite*: a safety warning. The audit corrections stand.

---

## G. Save confirmation

- Brief: `/Users/jonas/Documents/IDE/Health Store/docs/superpowers/specs/research-fidelity-2026-05-05.md`
- Screenshots: `/Users/jonas/Documents/IDE/Health Store/.playwright-mcp/fidelity-2026-05-05/` — `01-twc-spike-detox-desktop.png`, `01-twc-spike-detox-snapshot.md`, `02-twc-spike-detox-mobile.png`, `03-twc-home-desktop.png`, `04-twc-healthy-heart-desktop.png`, `05-twc-ultra-nac-desktop.png`, `06-twc-tirzepatide-gum-desktop.png`, `07-current-tcc-password-desktop.png`.
- Current TCC PDP not inspected live (still gated). Code-level review of `tcc-theme/templates/product.spike-detox.json` shows the spine is largely built; the gaps in section E are buildable from existing sections plus 4–5 new ones.
