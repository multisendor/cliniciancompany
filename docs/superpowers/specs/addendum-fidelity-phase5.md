# Addendum — Phase 5 polish (after v1 PDPs ship)

**Source:** `research-fidelity-2026-05-05.md` + decisions on top.
**Trigger:** apply once the Phase 1–4 build agent has shipped Pure + Complete v1 PDPs.

## What changed since the Phase 1 brief

The fidelity agent confirmed only one material update on TWC since 2026-05-04: a **3-card video testimonial carousel embedded inside the hero gallery column** (Shaun, Gina, Taylor). Pricing, FAQ, PMC citations, review widget all unchanged.

Outside TWC's PDP itself, the agent surfaced:

- **TWC has only one spike SKU** at $89.99. No Pure / Complete tier. Our 2-SKU ladder is a real architectural moat — write copy that owns it.
- **TWC's Trustpilot is 2.8/5 across 582 reviews.** Operations complaints (shipping, sub-cancellation, refunds) are TWC's softest flank. Execution is a real positioning lever.
- **TWC runs a 60-sec TV spot** ("Unexplained Health Symptoms") with per-network landing pages and a $6M Rumble commitment. Out of v1 scope but informs Complete's ad-funnel landing-page strategy later.
- **Pricing unchanged** at TWC. Our $39 / $89 / $79 ladder remains in target.

## Phase 5 additions (do these after Phase 4 QA)

Each is small, fits the clinical-clean aesthetic, and adds CVR without violating audit rules.

### 5.1 Free-shipping threshold bar
- Wire a free-shipping bar into `announcement-bar.liquid` (one of the rotating messages).
- Copy: *"Free U.S. Shipping on orders $75+ · 60-day money-back guarantee · Every batch COA-published."*
- No urgency, no countdown — just facts.

### 5.2 Drug-interaction chip near ATC
- New tiny block inside `product-details-buybox.liquid` (or as a new `safety-chip` block type already in the buybox schema if it exists).
- Render: small chip with a subtle medical-cross icon + text *"Talk to your healthcare provider before use, especially if you take blood thinners."*
- Sits directly under the Add-to-Cart button.
- Honest disclosure → trust signal, not a deterrent (the audit + corpus both confirmed this lifts trust with the cardiac and ClotSurvivor avatars).

### 5.3 Hero credibility row (60-day guarantee badge)
- Add a 4-icon row immediately under the buybox H1: *60-day money-back · Third-party tested · K2-free for blood-thinner users · Made in USA, GMP-certified*.
- TWC has a silent 30-day implied refund. **Beat them with explicit 60-day** (already in `money-back-guarantee.liquid`).

### 5.4 AFib-complementary-use FAQ entry
- New FAQ in `bottom-faq.liquid` content (template-side):
  - **Q:** *"I'm on Eliquis (or Xarelto / warfarin). Can I take this?"*
  - **A:** Honest answer per `research-clinical-evidence.md` §4: explain that nattokinase has fibrinolytic activity, that Chen 2022's trial saw aspirin-synergistic outcomes without bleeding events, but that **substituting nattokinase for prescription anticoagulation has a documented warning case (Elahi 2015)**. The recommendation is **complementary use under physician supervision, not substitution**. Encourage the conversation, don't run from it.
- Important framing: this is FTC-positive (we're being honest about a real safety concern), avatar-positive (corpus shows ClotSurvivors and AFib subs explicitly want this conversation), and operationally cleaner than the silent passive treatment TWC gives it.

### 5.5 Optional: 3-card video-testimonial slot in hero
- Mirror TWC's new "Your Health. Your Stories." pattern inside the hero gallery column.
- Use `video-testimonials-slider.liquid` (already built, currently unwired) but configured for 3 cards inside the hero, not as a separate section.
- **If we don't have real videos yet,** ship a placeholder card with copy *"Customer stories coming soon — send yours to stories@…"* and circle back when video assets exist.

### 5.6 Operational-moat positioning copy (passive)
- Don't make this loud — it's positioning, not a section.
- In the FAQ "How long until I get it?" answer: explicit shipping window (e.g. *"Most orders ship within 24 hours from our [STATE] facility. U.S. delivery in 3–6 business days."*).
- In the FAQ "How do I cancel my subscription?" answer: explicit one-click cancel language with no hoops. (TWC's Trustpilot pain point.)
- These two answers, told plainly, beat 80% of the operational complaints in TWC's Trustpilot reviews.

## What we explicitly DON'T copy from TWC

- "McCullough Protocol™" wording (trademark, borrowed authority)
- "Approved by the Chief Medical Board" verbatim phrasing (we don't have a board)
- TWC's specific 71% / 77% stats (those are their internal survey data, not ours)
- Live-purchase ticker, exit-intent modal, countdown timers — already excluded by clinical-clean rule

## What we don't add at v1 but plan for v1.5+

- Real reviews widget (Okendo / Judge.me) once we have 25+ verified buyers
- Real "Featured On" press logos once we have real coverage
- Real doctor authority block (named practitioner)
- TrueMed HSA/FSA real integration
- Custom landing pages per ad funnel (matches TWC's per-network LPs)

## Sequencing

Phase 5 polish runs only after Phase 4 (Playwright QA on v1) confirms the structural build is clean. If anything in Phase 1–4 conflicts with these additions, Phase 1–4 wins. Phase 5 is additive, not corrective.
