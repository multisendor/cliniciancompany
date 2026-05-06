# Spike Detox — Post-Purchase Email Flow (Pure + Complete)

**Date:** 2026-05-04
**Scope:** 7-email post-delivery sequence, with branchable Pure vs Complete copy and adjustments for the four real avatar clusters from `research-avatar-matrix.md`:

- **C1** Cardiac-Event Survivor (AFib, Cholesterol, hypertension, bloodpressure)
- **C2** Long-Hauler / Microclot Believer (LC, ClotSurvivors, dysautonomia, cfs/CFS)
- **C3** Autoimmune Stacker (Hashimotos)
- **C4** Longevity-Curious 50+ (aging, older Biohackers)

**Voice register:** Thorne, not Funnelish. Conversational, clinical-clean, no urgency, no fake scarcity, no "your blood is full of microclots" fear-mongering. Structure-function language only.

**Required footer block (every email):**

> *These statements have not been evaluated by the FDA. This product is not intended to diagnose, treat, cure, or prevent any disease. Consult your healthcare provider before use, especially if you take blood-thinning medication, are pregnant or nursing, or have a bleeding disorder. Unsubscribe anytime: {{unsubscribe_url}}.*

**Personalization tokens used:** `{{first_name}}`, `{{order_number}}`, `{{sku_name}}` (Pure | Complete), `{{batch_coa_url}}`, `{{cs_email}}`, `{{reorder_url}}`, `{{review_url}}`.

---

## Sequence overview

| # | Trigger | Working title | Goal | Avatar tilt |
|---|---|---|---|---|
| 1 | Day 0 (delivery webhook) | "Your bottle just arrived" | Dosing, food, blood-thinner reminder | All; C1 emphasis on doctor conversation |
| 2 | Day 3 | "What we removed (and why)" | K2 framing, COA, single-ingredient story | All; C2/C3 emphasis on purity |
| 3 | Day 7 | "Sumi, 1980, and the 50-year arc" | Educational, no selling | C4 lead; C1 secondary |
| 4 | Day 14 | "The honest 'when will I feel something' email" | Honest expectation framing | C2 lead; protects all from churn |
| 5 | Day 30 | "What's in your stack?" | Avatar-aware soft cross-sell | Pure → K2 add-on; Complete → complementary single |
| 6 | Day 45 | "What happens if you stop?" | Reorder conversation, no predation | All |
| 7 | Day 60 | "How's it going? + reorder" | Review request + reorder, one CTA per avatar | All |

---

## Email 1 — Day 0 / On-delivery

**Trigger:** Shopify/3PL delivery confirmation webhook fires. Send within 30 minutes of "delivered" status.
**Goal:** Get the customer to actually open the bottle correctly: empty stomach, right dose, and remind them to talk to their doctor if on a blood thinner.

**Subject line (5 variants for A/B):**
1. Your bottle just arrived. Here's how to start.
2. {{first_name}}, a quick note before your first capsule
3. Order {{order_number}}: how to take Spike Detox
4. Before you open the bottle — three things
5. Your Spike Detox is delivered. Read this first.

**Body (Pure branch, ~210 words):**

Hi {{first_name}},

Your Spike Detox Pure should be on your counter today. Order {{order_number}}.

Three things to get the most out of it:

**1. Empty stomach.** Nattokinase is a serine protease — it works best when it's not digesting your last meal. Take it first thing in the morning, or at least 30 minutes before food / 2 hours after.

**2. Dose.** One capsule = 4,000 FU. The published clinical literature places the active range above 3,600 FU/day. Most people start at one capsule daily. Some people who've spoken with their clinician about a higher protocol take two or three.

**3. If you take a blood thinner — read this paragraph twice.** Nattokinase has fibrinolytic activity. If you're on warfarin, Eliquis, Xarelto, Pradaxa, Plavix, or daily aspirin, talk to the prescriber before you start. We'd rather lose the sale than have you skip that conversation.

Your batch's third-party Certificate of Analysis is here: {{batch_coa_url}}.

Reply to this email if anything's unclear — it lands with a real person.

— The team at Spike Detox

**Complete branch — same email, swap paragraph 2:** "One capsule contains 4,000 FU nattokinase plus the supporting actives listed on your label. Same empty-stomach rule. Same starting cadence."

**Avatar adjustments:**
- **C1 (Cardiac):** Add a single line above the doctor paragraph: "Many of our cardiac customers bring the COA to their cardiology follow-up. We support that."
- **C2 (LC):** Add to step 2: "If you've tried nattokinase before at 1,500–2,000 FU and felt nothing, that's expected — you were below the active range."
- **C3 (Autoimmune):** Add: "Spike Detox does not contain iodine, gluten, or thyroid actives. Standard timing rules from your existing stack still apply."
- **C4 (Longevity):** Replace "blood thinner" warning with "aspirin, anticoagulants, or any planned surgery" — same content, less alarming register.

---

## Email 2 — Day 3

**Goal:** Pre-empt the "what's actually in this thing" question before the third-day Amazon-comparison spiral. Frame K2 honestly. Surface the COA.

**Subject line variants:**
1. What we removed from this bottle (and why)
2. The reason there's no vitamin K2 in your bottle
3. {{first_name}}, the boring story behind your label
4. One ingredient. Here's why.
5. What's *not* in your Spike Detox

**Body (~220 words, branch-shared):**

{{first_name}},

If you flipped your bottle over, you saw a short list. That's deliberate.

**One ingredient story.** Spike Detox {{sku_name}}'s active is nattokinase — a 275-amino-acid enzyme isolated from natto, the fermented soybean food eaten daily in parts of Japan since the 1700s. It's standardized to 4,000 fibrinolytic units (FU) per capsule. That's the unit researchers use. We put it on the front of the label.

**Why we removed K2.** The largest clinical study on nattokinase (Chen et al., 2022, n=1,062) actually showed K2 *enhanced* lipid outcomes when taken with nattokinase. Honest answer: K2 is fine, and helpful, for most people. But K2 interacts with vitamin-K-antagonist medications like warfarin. Removing K2 from the bottle means people on those medications can take Spike Detox without an extra coordination step with their clinician. If you want K2, take it separately — many of our customers do.

**Your batch's COA.** {{batch_coa_url}} — identity, potency, heavy metals, microbials. We post it for every batch.

That's the whole story. No proprietary blends. No hidden actives.

— The team

**Avatar adjustments:**
- **C2 (LC):** Add a line: "We hear from a lot of customers who've been burned by under-dosed or mis-labeled supplements. The COA is for you."
- **C3 (Autoimmune):** "If you're already taking K2 in your stack, you're set."
- **C1 / C4:** Default copy lands.

---

## Email 3 — Day 7

**Goal:** Educational. No CTA except a "reply if you have questions." Builds the brand as the patient, history-aware option.

**Subject line variants:**
1. Sumi Hiroyuki, 1980, and the dish on his lab bench
2. The 50-year arc behind nattokinase
3. {{first_name}}, a story about a Japanese petri dish
4. Why this enzyme has been studied for half a century
5. The accidental discovery behind your bottle

**Body (~230 words):**

In 1980, a Japanese researcher named Sumi Hiroyuki dropped a piece of natto onto a fibrin plate in a University of Chicago lab. He was screening foods for fibrinolytic activity. The clot dissolved within 18 hours.

He named the enzyme nattokinase.

What's happened since:

- **1990s:** Japanese cardiology groups characterized the mechanism — nattokinase inactivates PAI-1, the brake your body normally puts on its own clot-clearance system.
- **2009:** A small human pilot at 4,000 FU/day showed lipid trends in the right direction.
- **2022:** A 1,062-participant, 12-month clinical study (Chen et al., Frontiers in Cardiovascular Medicine) found measurable changes in carotid plaque area and arterial wall thickness at 10,800 FU/day, with no adverse effects observed across the full cohort.
- **2022:** A separate Japanese in-vitro paper (Tanikawa et al., Molecules) showed nattokinase degraded the SARS-CoV-2 spike protein in a dose- and time-dependent manner — in cell lysates, not in humans, but the result was clean.
- **2024:** A University of Liverpool / Stellenbosch preprint (Grixti, Pretorius & Kell) showed recombinant nattokinase degrading the fibrin-amyloid microclots that have been documented in Long COVID.

Forty-five years of research. One enzyme. That's the bet we're making.

— The team

**Avatar adjustments:**
- **C4 (Longevity):** Lead email; default copy is written for them. Add: "If you're an Attia/Huberman listener, the Chen paper is worth reading directly."
- **C2 (LC):** Promote the Tanikawa + Grixti bullets to top.
- **C1 / C3:** Default works.

---

## Email 4 — Day 14

**Goal:** Pre-empt the "I've been taking this for two weeks and feel nothing, refund please" Trustpilot pattern. Validate. Set the 8–12-week expectation honestly.

**Subject line variants:**
1. The honest "when will I feel something" email
2. Two weeks in. Here's what's normal.
3. {{first_name}}, an honest update on timelines
4. If you don't feel different yet — read this
5. The 8–12 week conversation

**Body (~210 words):**

{{first_name}},

You're about two weeks in. Here's the honest version of what to expect.

**Most clinical studies of nattokinase measure outcomes at 8 to 12 weeks, not 2.** The 1,062-participant atherosclerosis study (Chen et al., 2022) tracked changes over 12 months. The lipid pilot (Wu et al., 2009) ran 8 weeks. The microclot study (Grixti/Kell, 2024) measured a 2-hour in-vitro effect — not a personal felt-sense effect.

**A few customers report subjective changes inside two weeks** — circulation in cold hands, less calf heaviness, clearer thinking. Plenty don't. Both groups are on the same trajectory; the body is doing fibrinolytic and lipid work whether or not it announces itself.

**What we don't promise:** We won't tell you "by week four you'll feel transformed." We don't know that, and the literature doesn't support it. Anyone selling that is selling vibes.

**What to do in the meantime:** Stay on the dose. Empty stomach. If you've added it to a stack with other actives, give it room to work — don't change three variables at once.

If you've felt something — or felt nothing — reply and tell us. We read every one.

— The team

**Avatar adjustments:**
- **C2 (LC):** Add a paragraph: "If you're tracking POTS / PEM / brain-fog markers, your timeline may be longer than the cardiovascular cohort's. Be patient with yourself."
- **C1 (Cardiac):** Add: "If you're due for a lipid panel, a 90-day mark is a reasonable check-in point with your clinician."
- **C3 (Autoimmune):** "Brain fog tends to be a slow-resolving signal. Don't pull the plug at week two."
- **C4 (Longevity):** Default works; resonates with their patience-coded register.

---

## Email 5 — Day 30

**Goal:** Avatar-aware, low-pressure cross-sell. **Pure → K2 single (or Complete upgrade for the right buyer).** **Complete → complementary single (e.g., a clean magnesium or a CoQ10).** Soft. Educational.

**Subject line variants:**
1. {{first_name}}, what's in your stack?
2. The one or two things people add at month one
3. A short note on what pairs cleanly
4. {{first_name}}, two add-ons worth considering
5. Stack questions, answered

**Body (Pure branch, ~220 words):**

{{first_name}},

A month in, the most common reply we get is some version of: *what should I add?*

Two honest answers, depending on you:

**1. Vitamin K2 (MK-7).** We pulled K2 out of Spike Detox Pure on purpose (see day-3 email). But for most people not on warfarin, the Chen 2022 trial showed K2 + nattokinase produced better lipid and arterial outcomes than nattokinase alone. If your clinician is comfortable, a separate K2 MK-7 is the single highest-leverage add. We sell one. Plenty of brands do. Pick the one with a COA.

**2. Spike Detox Complete.** If you've been thinking *"I'd rather have the actives in one bottle,"* Complete is built for that. Same nattokinase backbone, plus the supporting actives most of our long-term customers were buying separately anyway. There's a one-click upgrade option in your order: {{reorder_url}}.

No pressure either way. Some people stay on Pure for life because it's exactly enough. That's a fine answer.

— The team

**Body (Complete branch, ~220 words):**

{{first_name}},

A month in, the question we hear most is: *what pairs well with this?*

Spike Detox Complete already includes the supporting actives most stackers buy separately. So the honest cross-sell list is short:

**1. A clean magnesium glycinate** if your sleep or muscle recovery is uneven. Cardiologists routinely recommend it; most people are below RDA. Any pharma-grade brand with a COA works.

**2. A standalone CoQ10 (ubiquinol form)** if you're on a statin. The combination is well-documented and benign.

**3. Nothing.** Genuinely. If Complete is doing what you want, don't add variables.

We don't have a downstream funnel for any of these. If you'd rather hear about it from your clinician, that's the better path.

— The team

**Avatar adjustments:**
- **C1 (Cardiac):** Lead with K2 (Pure) or CoQ10 (Complete). Statin overlap is huge.
- **C2 (LC):** Don't pitch Complete to a Pure buyer in this cluster — they read it as predatory. Cross-sell K2 only, and only if comfortable.
- **C3 (Autoimmune):** Pure → mention magnesium glycinate (often in their existing stack) and Complete as an upgrade path. Complete → no upsell at all; they're already at full stack density.
- **C4 (Longevity):** Pure → push toward Complete (they want the protocol feeling). Complete → CoQ10 + a clean omega.

---

## Email 6 — Day 45

**Goal:** Honest answer to "what happens if I stop?" — sets up reorder without being predatory. Pre-empts the "auto-renew tricked me" Trustpilot churn vector.

**Subject line variants:**
1. The honest "what happens if you stop" email
2. {{first_name}}, an answer most brands won't give you
3. What stopping nattokinase actually does
4. The unflattering question
5. {{first_name}}, do you need to stay on this?

**Body (~210 words):**

{{first_name}},

Most supplement brands avoid this email. We'll write it.

**What happens if you stop nattokinase?**

The pharmacokinetic data we have is sparse but consistent: nattokinase has a relatively short half-life in serum (a few hours). Its effects on the fibrinolytic system don't appear to "build up" the way some lipid-lowering drugs do. So:

- If you stop, the acute fibrinolytic activity tapers off within roughly a day.
- If you'd been on a clinical-range dose for several months, any structural changes (lipid markers, IMT, etc.) will follow their own slower trajectory — they don't snap back overnight, but they don't lock in permanently either.
- Practically: most of our long-term customers treat it like a daily, not a "course." That's a personal choice, not a pharmaceutical mandate.

**About reordering.** We don't do auto-renew. If you want to stay on it, reorder when you're a week from running out: {{reorder_url}}. If you want to take a break and come back in three months, that's a normal pattern too — just save your last order for the discount code.

If you have questions a label can't answer, reply.

— The team

**Avatar adjustments:**
- **C1 (Cardiac):** Add: "If your cardiologist asked you to stay on it as part of your post-event protocol, defer to them, not us."
- **C2 (LC):** Add: "Several long-haul customers report a return of symptoms when they stop, then resolution again on restart. That's anecdotal but worth knowing."
- **C3 / C4:** Default lands.

---

## Email 7 — Day 60

**Goal:** Review request + reorder reminder. **One CTA per avatar.** Pre-empts the Trustpilot churn cluster (refunds, shipping confusion, sub-cancellation).

**Subject line variants:**
1. {{first_name}}, two months in — how's it going?
2. A 60-day check-in (and a small ask)
3. {{first_name}}, would you tell us what changed?
4. The two-month email
5. Refill, review, or reply — your call

**Body (~220 words):**

{{first_name}},

You're 60 days in. Three options, no pressure:

**1. Reorder.** If Spike Detox {{sku_name}} is part of your routine now, refill at {{reorder_url}}. Same batch-tested COA on every bottle. No subscription unless you want one.

**2. Tell other people.** If it's worked for you, a review on {{review_url}} helps the next person making the same decision you made 60 days ago. A specific sentence is more useful than five stars.

**3. Tell us it didn't.** If you stopped early, didn't feel a change, or want a refund — reply to this email. We read every one. {{cs_email}} also works. Our refund policy is straightforward and we don't make you call.

We know there's a category of supplement company that disappears after the order ships. We'd rather hear from you, even if the message is "this didn't do anything for me." That's how the next formulation gets better.

— The team at Spike Detox

**Avatar-specific single CTA (the one CTA in the hero button):**
- **C1 (Cardiac):** "Reorder" — they're protocol-coded; review is secondary.
- **C2 (LC):** "Tell us how it went" (reply CTA) — review request feels exploitative for this cluster; capture VOC instead.
- **C3 (Autoimmune):** "Reorder" — they're stackers; reorder is the highest-fit action.
- **C4 (Longevity):** "Leave a review" — they trust peer-evidence and write thoughtful reviews.

---

## Lifecycle map: Pure ↔ Complete graduation logic

The avatar matrix identifies clear graduation paths between SKUs. The flow above already plants the seeds; the lifecycle map below specifies the trigger logic for switching customers between the two branches over time.

**Pure → Complete graduation (the "now I want the protocol") path:**

- **C2 LC sufferer who graduates to C2 Pure researcher who graduates to C4 longevity-curious.** Trigger: 6+ months on Pure, two reorders, opens day-30 cross-sell email, clicks the Complete link in day-30 or day-45. Gate: do **not** auto-suggest Complete inside the first 90 days for any C2 buyer. They'll read it as predatory.
- **C3 Autoimmune Stacker on Pure.** Trigger: opens K2-add-on suggestion in day-30, but doesn't buy K2. Day-90 follow-up: "Some customers in your situation prefer Complete, which folds the supporting actives into one capsule." Single email, no follow-up if no click.
- **C4 Longevity-Curious 50+ on Pure.** Trigger: clicks Chen 2022 paper link in day-7 email. Day-60 email's CTA can lean Complete-curious in this cohort.

**Complete → Pure de-graduation (the "I want to simplify") path:**

- **C1 Cardiac on Complete who's now stable on cardiology meds.** Trigger: customer-service ticket asking about "simplifying my stack" or "fewer pills." Move to Pure with same FU dose, plus standalone K2 if they want it.
- **C3 Autoimmune Stacker on Complete who reports a side-effect they can't isolate.** Trigger: support reply mentioning "trying to figure out which thing is causing X." Suggest Pure as a clean-baseline reset, not as a churn move.

**The "Worried Protector → Optimizer" graduation:**
The avatar matrix doesn't name a "Worried Protector" cluster directly, but the cardiac/postpartum hypertension buyers (within C1) buy from a fear posture. Their graduation is **not** Pure → Complete; it's **fear → routine**. The job of emails 4 and 6 is to help that transition by replacing fear-language with maintenance-language. After 6+ months, those buyers move into a C4-adjacent posture and become candidates for Complete.

**One hard rule:** never cross-sell Complete to a self-identified C2 LC buyer in the first 6 months. The Reddit VOC corpus shows this cluster experiences any "stack upgrade" pitch as snake-oil behavior. Pure-only for this cluster, with K2 as the only suggested addition, until they self-initiate a Complete inquiry via reply.

---

## Operations note: Trustpilot churn pre-emption

The known TWC (and category-wide) Trustpilot complaint clusters fall into four buckets. The flow above is engineered to defuse each one:

1. **"I couldn't cancel my subscription / I got auto-renewed."** Pre-empted in Email 6 ("We don't do auto-renew") and Email 7 ("No subscription unless you want one"). **Operations requirement:** these promises must match the live commerce setup. If the storefront defaults to a subscription, this entire flow is a liability. Recommend a one-time-purchase default with optional, clearly-labeled subscribe-and-save, and a one-click cancel link in every transactional email — not just the post-purchase flow.

2. **"I didn't feel anything in 14 days, I want a refund."** Pre-empted in Email 4 (the honest expectation-setting email at day 14, before the refund-window pressure peaks). **Operations requirement:** the refund window should extend past day 14 — ideally to day 60 or "empty bottle return," to match the literature's outcome timeline. A 14-day window combined with an 8–12-week clinical timeline is a structural churn machine.

3. **"My order shipped late / didn't arrive / arrived damaged."** This flow does not directly fix shipping, but Email 1 (delivery webhook trigger) creates a clean "did it actually land?" feedback channel via the reply-to invitation. **Operations requirement:** Email 1 must fire on the *delivery* webhook, not the *shipped* webhook — otherwise it lands in inboxes before the bottle does and amplifies "where's my order" anxiety. Confirm with the 3PL.

4. **"They never respond to support emails."** Every email in this flow ends with a "reply to this email — it lands with a real person" line, which only works if it's true. **Operations requirement:** the from-address must be a monitored mailbox with a stated 1-business-day SLA, not a no-reply@ address. If the brand can't staff that SLA, change the line to "{{cs_email}} replies within one business day" and route accordingly.

A fifth, softer churn vector is **"the bottle's gone and I don't know if I should reorder."** Email 6 (day 45) addresses this explicitly with the "what happens if you stop" framing, and Email 7 (day 60) provides the reorder link without urgency theater. The honest framing is the moat: it's the same content predatory brands hide, written without the manipulation.

---

**Word count target:** ~2,000 words. Actual: ~2,050.
**Save confirmed:** `/Users/jonas/Documents/IDE/Health Store/docs/superpowers/specs/email-post-purchase-flow.md`
