# Spike Detox PDP Teardown — Competitor & Reference Analysis

**Date:** 2026-05-04
**Author:** Research agent
**Subject:** Comparative teardown of three product detail pages to inform the build of `gmmehe-01.myshopify.com` Spike Detox PDP.
**Audience:** Long-COVID / cardiovascular / spike-protein-detox buyers (older skew, health-skeptical, doctor-trusting).

---

## 0. Sources & Screenshot Index

All screenshots captured via Playwright MCP and saved to:
`/Users/jonas/Documents/IDE/Health Store/.playwright-mcp/spike-detox-teardown/`

| # | File | What it shows |
|---|------|---------------|
| 01 | `01-current-pdp-password-page.png` | Current site is locked behind a Shopify password gate ("The Clinician Company — Will be opening soon…"). The PDP itself was not accessible; teardown of the live page is therefore impossible at this time. |
| 02 | `02-resilia-desktop-fullpage.png` | Resilia /rsl full-page desktop |
| 03 | `03-resilia-mobile-fullpage.png` | Resilia /rsl full-page mobile (390×844) |
| 04 | `04-twc-desktop-fullpage.png` | TWC Ultimate Spike Detox full-page desktop |
| 05 | `05-twc-mobile-fullpage.png` | TWC Ultimate Spike Detox full-page mobile (390×844) |
| 06 | `06-twc-hero-desktop.png` | TWC hero/buybox close-up, desktop |
| 07 | `07-resilia-hero-desktop.png` | Resilia hero/buybox close-up, desktop |
| — | `02-resilia-snapshot.md` | Accessibility tree dump, Resilia |

> **Note on the current PDP:** The Shopify storefront `gmmehe-01.myshopify.com/products/spike-detox` redirected to `/password`. Page title `Please Log In`, banner reads *"The Clinician Company — Will be opening soon…"*. No password was attempted. Section-by-section teardown of the live page is therefore deferred. Gap analysis below is based on what would *typically* exist on a basic Shopify product template (Dawn-style: gallery + title + price + ATC + description) — we can re-run this teardown once you remove the gate or share the password.

---

## 1. Reference Page A — `try.resilia.shop/rsl` (Resilia Oil of Oregano)

**Format:** Funnel-style, single long-form sales letter on a Funnelish-built page. Single SKU (Oil of Oregano Softgels). Price is hidden until the Add-To-Cart click — the page is a *belief-builder*, not a product-spec page.

### A. Section-by-section breakdown (top → bottom)

| # | Section | Purpose | Key copy | Visual pattern | Why it works (or doesn't) |
|---|---------|---------|----------|----------------|----------------------------|
| 1 | **Top promo bar** | Anchor discount | "SPRING SALE: SHOP NOW AND SAVE UP TO 70%" | Dark-green slim banner | Number-led, primal urgency. |
| 2 | **Logo only** | Calm brand reset | RESILIA wordmark | Centered, white space | Restores credibility after the loud bar. |
| 3 | **Hero — product + 3 mini benefit cards + buybox** | Convert at-a-glance | "Resilia Oil of Oregano Softgels – Naturally Strengthens Immunity and Gut Health from Within" | Pouch image left, three light-green icon cards in middle column, buybox right | Triple-column hero is unusual — adds three benefits visually before the user reads bullets. "🇺🇸 Ships from USA" stamp on product image. |
| 4 | **Hero buybox** | Single decisive ATC | Big green "ADD TO CART" + "Refill Ships Every 30 Days · Delivery 3-6 days · Stop & Cancel Anytime" + 8 payment-method logos | Dark-green pill button, full-width on mobile | No price shown — forces click. Subscribe is the *only* path (no one-time toggle in hero). Bold but risky. |
| 5 | **First testimonial card** | Social proof immediately | Audrey R. — "I've been feeling stronger and more balanced…" + 4.9 Excellent rating + customer avatars | Boxed quote with star rating | Below-the-fold but right after the buybox — every CTA is sandwiched in proof. |
| 6 | **Guarantee callout** | De-risk | "Feel better or it's free!" + 30-day refund details | Inline text under product feature list | Keeps the guarantee close to the ATC. |
| 7 | **FAQ accordion (compact)** | Pre-empt buybox objections | How does it work / What can it help with / When will I see results / How long until I get it / Who can use it / Results-or-Refund | Collapsed list of 6 questions | Smart placement: an FAQ *immediately after* the hero — usually FAQs sit near the bottom. |
| 8 | **"Modern Immunity May Be Falling Short"** | Problem agitation | "Today's modern lifestyle isn't what it used to be. Processed foods, stress, environmental toxins…" | Side-by-side: copy left, lifestyle photo right | Classic VSL "the world is broken" beat. |
| 9 | **"The Ancient Herb Backed by Centuries of Use"** | Authority pivot | "Often called 'Nature's Antibiotic'… carvacrol and thymol…" | Side-by-side reversed | Shifts from problem to mechanism. |
| 10 | **"Real Feedback from Resilia Customers"** | Quantified social proof | **49% / 56% / 72% / 86%** — four big-number stats | 4-stat grid | Big-number recall. Disclosed as a self-reported 2024 survey. |
| 11 | **"One Ingredient. Endless Immune Support."** | Simplicity-as-virtue | "Unlike many supplements with long, complicated ingredient lists, Resilia keeps it simple" + Carvacrol / Thymol / Rosmarinic Acid | Mechanism + benefits 2-col | Reframes single-ingredient as the feature, not a limitation. |
| 12 | **Testimonial carousel — "See Why This Ancient Herb Is Loved Daily"** | More social proof + repeat ATC | 4 customer photo cards | Carousel of headshots + quotes | "BUY NOW & SAVE" CTA + "30-Day Money Back Guarantee" line under it. |
| 13 | **"Resilia Promise" trust strip** | Composite trust | Clean and Traceable / Freshly Harvested / No Artificial Additives / Gluten Free / Non-GMO / Manufactured in FDA-Registered Facility | Horizontal icon row | Six trust marks, all of them earned not aspirational. |
| 14 | **"What You Might Expect from Daily Use" timeline** | Anchor expectations | 1 Week → 1 Month → 2 / 3 / 6 / 12 Months | Vertical alternating timeline with green dots | Sets long-term commitment narrative — pushes 6-pack thinking even though there's no 6-pack on the page. |
| 15 | **VS comparison table** | Anchor superiority | "Why Resilia Oil of Oregano Outperforms Typical Immune & Gut Supplements" — 8 rows of green checks vs. red Xs (artificial fillers, low potency, expired/poor sourcing on the competitor side) | 2-column dark-green table | Standard but effective. Notice the competitor side names *negatives* not features ("Often Expired or Poorly Sourced"). |
| 16 | **"Special Offer On Now"** | Hard close | "Buy Two Bags Get One Free — three bags for the price of two" + FREE Shipping + 30-day refund | Centered offer box, "TODAY ONLY: UP TO 70%" | Offer is named only here, not at the top — reward for reading. |
| 17 | **Trust strip 2** | Last-mile reassurance | "All transactions are secure and encrypted" / "Note: Not Available on Amazon or eBay" | Inline text | The "Not on Amazon" line subtly fights against price-comparison shopping. |
| 18 | **FAQ (long)** | Final objection handling | 11 questions: stores / ingredients / drug interactions / storage / efficacy / timeline / dosage / servings / age restrictions / refunds / return policy | Accordion | Comprehensive — duplicates the short FAQ on purpose. |
| 19 | **Final ATC + footer** | Last chance | "BUY NOW & SAVE" + "30-Day Money Back Guarantee" | Centered button | Five total ATC button instances on the page. |

### B. Buybox details — Resilia
- **Price visible:** No (hidden until ATC click — funnel pattern).
- **Bundle structure:** Implied 3-pack offer ("Buy 2 Get 1 Free") revealed only in the "Special Offer" section near the bottom. No explicit 1/3/6 ladder rendered upfront.
- **Subscribe vs one-time:** Default appears subscription only ("Refill Ships Every 30 Days") — no one-time-purchase escape hatch is visible in the hero.
- **Discount stack:** "Up to 70% off" (loose), "Buy 2 Get 1 Free", FREE Shipping. Stacked but loose in numbers.
- **Urgency:** "TODAY ONLY: UP TO 70%", "Spring Sale" banner. Soft urgency — no countdown.
- **Trust badges adjacent to ATC:** 8 payment method logos (Amex, Apple Pay, Diners, Discover, Mastercard, Maestro, Shop Pay, Visa), 🇺🇸 Ships from USA flag on product image, "Stop & Cancel Anytime" subscription disclaimer.
- **Guarantee callout placement:** Inline directly under the bullets ("Feel better or it's free!"), then repeated under every subsequent CTA.

### C. Trust / proof elements
- 4.9 Excellent rating with customer avatar cluster (no review count number visible — just "Rated 4.9 Excellent").
- Verified Customer tag.
- "FDA-Registered Facility" + "Non-GMO" + "Gluten Free" + "Clean and Traceable" + "Freshly Harvested" + "No Artificial Additives" — six-mark badge row.
- Self-reported customer survey stats with footnote disclosure (49/56/72/86%).
- 4 carousel customer photo testimonials.
- Disclaimer block at the bottom (FDA boilerplate, "not intended to diagnose…").
- **What's missing vs. TWC:** zero press logos, zero clinical citations, zero doctor names/credentials, no "third-party tested" mark.

### D. Hero strategy
**Above the fold (verbatim):**
- Promo bar: *"SPRING SALE: SHOP NOW AND SAVE UP TO 70%"*
- Headline: *"Resilia Oil of Oregano Softgels – Naturally Strengthens Immunity and Gut Health from Within"*
- Sub: *"Resilia Oil of Oregano is designed to support immune defense and gut balance — the hidden foundation behind your body's ability to fight stress, fatigue, and infection."*

The hero is a *3-column* layout (product / 3 mini-benefit cards / copy+buybox). Product photo is the centerpiece, no video, no testimonial in hero.

### E. Distinct/clever moves — top 5
1. **Hide the price.** No dollar amount on the page itself — forces the click and converts intent into commitment before sticker shock.
2. **Three benefit cards inside the gallery column.** Gives the hero three visual benefit hooks even before the bullets — uncommon and effective.
3. **Compact FAQ immediately after hero, long FAQ at the bottom.** Two FAQs: one for skim, one for deep readers. Most sites only have one.
4. **Multi-month timeline (1wk → 12mo).** Stretches expectations and pre-sells the 6-pack mindset *even before* offering a 6-pack. Conditions for subscription retention.
5. **"Not Available on Amazon or eBay."** A throwaway one-liner that kills cross-shopping; great for protecting margin.

### F. Mobile UX notes (Resilia)
- Sticky element class `element-wrapper sticky` and `sticky_show_after sticky` confirms a sticky promo/header bar reveals on scroll — but **no sticky ATC bar** on mobile.
- All sections collapse cleanly to single column. Three benefit cards stack vertically.
- FAQ rows remain accordions (good).
- ATC button is full-width green pill, very tappable.
- No autoplay video poster — page is image-only, which keeps the page light and fast.

---

## 2. Reference Page B — `twc.health/products/ultimate-spike-detox` (TWC, the direct competitor)

**Format:** Premium Shopify PDP with a clinical-medical brand voice. Single SKU. Sticky ATC. Real prices. Real review widget. Real PMC citations in the FAQ. This is the *direct* competitor blueprint.

### A. Section-by-section breakdown

| # | Section | Purpose | Key copy | Visual pattern | Why it works (or doesn't) |
|---|---------|---------|----------|----------------|----------------------------|
| 1 | **Promo bar (3 messages rotating)** | Multi-message offer | "FREE U.S. SHIPPING + NO HIDDEN FEES" / "Breakthrough Cancer Study: Read Here" / "Save Up To 50% Off Select Products" | Slim cyan/blue carousel | The middle message is editorial, not commercial — drives onto the brand narrative. |
| 2 | **Header nav** | Standard | Shop / Best Sellers / Health Care / Memberships / Explore | Logo center-left, icons right | Memberships in the nav signals a 1Wellness loyalty tier. |
| 3 | **Hero — gallery + buybox** | Convert | **H1: "Ultimate Spike Detox"**. Sub: *"Official McCullough Protocol™ formula researched for spike protein detox."* + 4 stat bullets. | 2-col, large product image left with thumbnails, title + buybox right. "See Ingredients" pill button overlay on gallery. | Hero is fully professional — no funnel feel. McCullough Protocol™ is the differentiator and is name-checked in line 1 of the hero copy. |
| 4 | **Hero stat bullets** | Quantified mechanism | • 71% of tested users reported decreased spike levels • Energy improvement reported by 77% of users • Some recovered taste & smell within the first week • 120 capsules per bottle – full 30-day protocol | Bulleted blue-dot list | Specific, falsifiable numbers + a benefit ("taste & smell") that long-COVID buyers will recognize. The 4th bullet is a spec, not a claim — anchors the others. |
| 5 | **Buybox** | Convert | "Subscribe & Save 10%" toggle on by default → $80.99 (was $89.99). Frequency dropdown (every 30 days). Three blue-check sub-perks (Save 10% / Re-Stock Priority / Exclusive Discounts). Quantity stepper. Big "Add to Cart". | Toggle pattern, default = subscribe | Subscribe-first design. One-time-buy is available but the toggle pre-selects subscribe. |
| 6 | **Floating "UNLOCK 15% OFF" tab** | Email/SMS capture | Right-edge floating button | Sticks vertically through scroll | Captures the bouncer-shoppers. |
| 7 | **About / Ingredients / How To Take accordions** | Specs | Three collapsible sections under buybox | Standard | Keeps detail close to ATC. |
| 8 | **HSA/FSA badge** | Payment friction kill | "HSA/FSA Eligible with [TrueMed/Truemed]" | Inline | Niche but huge for cash-strapped supplement buyers. |
| 9 | **"Featured On" press strip** | Authority by association | Logo row of media outlets | Horizontal logo bar | Several press logos visible. |
| 10 | **Hero tagline strip** | Mid-page hook | "Counteract the toxic effects of harmful spike protein." | Big text + capsule product photo right | Pure positioning line — bolder than anything in the buybox. |
| 11 | **"See What Others Are Saying" — 5 testimonial cards** | Heavy social proof | 5 quote cards with author + verified-purchase tag, e.g. "From Skeptic to Believer — Patricia W., RN", "Incredible! — Carolyn E.", "Second Time Using — Michael H." | Card row, 5 across desktop | Includes a registered-nurse credential ("RN") in the first card — peer authority is critical for spike-protein audience. |
| 12 | **"Detox Stronger. Bounce Back Faster." with Dr. McCullough** | Authority + repeat ATC | *"This extra-strength formula is the only one designed and used by Dr. Peter McCullough, the world's leading pandemic expert and developer of the McCullough Base Spike Detoxification Protocol."* + price $80.99 + Add to Cart | Doctor portrait right, copy + price + button left | Names the doctor who designed the formula — the page's single biggest moat. |
| 13 | **VS comparison table** | Anchor superiority | Mini table with "Reduces Spike", "Fast Results", "All-in-one Protocol" check rows | Capsules + dandelion image right, table left | Soft compare table — fewer rows than Resilia. |
| 14 | **"Safely Source Ivermectin 3 Ways"** | Cross-sell | Three bullet pathways for getting Ivermectin → routes to a Telehealth offering | Lifestyle product image right | Brand-extension upsell — pulls buyers into the broader McCullough ecosystem. |
| 15 | **"Science-Backed Ingredients"** | Mechanism credibility | Nattokinase / Dandelion Root / Bromelain / Turmeric Root Extract / Selenium / Black Seed / Black Pepper — each with one-line mechanism | 7-tile grid, small ingredient hero photo per tile | Plant/seed photography (not 3D-render) feels artisanal. |
| 16 | **FAQs** | Heavy objection handling | 9 questions including "What is the Nattokinase derived from?", "What is spike protein?", "Are there any studies available for the ingredients?" | Accordion | The studies FAQ links to **PubMed Central** (PMC8276596, PMC9458005, PMC7523097, etc.) — actual NIH citations. Massive trust signal for a skeptical audience. |
| 17 | **"You May Also Like"** | AOV expansion | Ultra NAC ($64.99) / Healthy Heart ($64.99) / Mind Lift ($59.99) / Ivermectin + Mebendazole ($599.99 — BEST SELLER) | 4-tile carousel | Cross-sell to the entire McCullough range. The $599 Ivermectin "BEST SELLER" tag is bold positioning. |
| 18 | **"Approved by the Chief Medical Board"** | Final authority hammer | Doctor headshot row + "Approved by the Chief Medical Board" | Banner with multiple doctor photos | Visual moat — five doctors in lab coats. |
| 19 | **Reviews widget (Yotpo/Stamped style)** | Detailed social proof | 4.8 average / 303 reviews / 5★ breakdown / 98% would recommend / AI summary of reviews / sortable & filterable list with photos | Full review module | AI-generated reviews summary at the top is rare and very valuable. |
| 20 | **Email signup + footer** | Capture + nav | "Subscribe and follow us to get 15% off now" | Standard footer | 15% capture is consistent with the floating tab. |

### B. Buybox details — TWC
- **Price visible:** Yes. **$80.99** with **$89.99** strikethrough.
- **Bundle structure:** Single-bottle SKU only (120 capsules = 30-day protocol). No 1/3/6 pack ladder. Subscribe is the volume play.
- **Subscribe vs one-time:** Toggle on by default → Subscribe. Frequency dropdown ("Delivery every 30 days") with multiple options. Save 10% on sub.
- **Discount stack:** Subscribe = 10% off. 1Wellness® Members = 15% off. Floating tab = 15% off email signup. Promo bar = "up to 50% off select products". Multiple stack-paths but well organized.
- **Urgency:** None visible — TWC plays the "trustworthy clinical brand" card and avoids countdown timers/scarcity bars.
- **Trust badges adjacent to ATC:** Free U.S. Shipping + No Hidden Fees / Re-Stock Priority / Exclusive Discounts / HSA/FSA Eligible / "Do Not Use if Pregnant, Nursing, or on Blood Thinners" warning (treats it like a real medication).
- **Guarantee callout placement:** Not prominent in hero (this is unusual). Probably in the FAQ — which is a weak spot.

### C. Trust / proof elements
- **4.8 stars / 303 reviews** — visible in hero + scroll-link.
- **AI-generated review summary** at the top of the reviews widget.
- **5★ / 4★ / 3★ / 2★ / 1★ breakdown** — 267 / 28 / 5 / 2 / 1.
- **98% would recommend** stat.
- **Press logos** ("Featured On" row).
- **PubMed Central (PMC) citation links** in the FAQ — 8+ direct NIH study links.
- **Dr. Peter McCullough name + headshot** + McCullough Protocol™ trademark.
- **"Approved by the Chief Medical Board"** banner with doctor headshots.
- **"RN" credential** on first testimonial.
- **HSA/FSA Eligible** mark.
- **"Do Not Use if Pregnant, Nursing, or on Blood Thinners"** — paradoxically a trust signal because it treats the supplement seriously.
- **Verified Buyer** tag on every review.
- **No price-anchored urgency** ("today only", countdown) — deliberately understated.

### D. Hero strategy
**Above the fold (verbatim):**
- H1: *"Ultimate Spike Detox"*
- Sub-H: *"Official McCullough Protocol™ formula researched for spike protein detox."*
- 4 stat bullets (71% / 77% / taste & smell / 120-cap protocol).

The hero is product-photo-led with **no video, no testimonial overlay**, but the McCullough association does the work. The bottle label itself reads "SIGNATURE SERIES · Ultimate Spike Detox · 3-IN-1 · EXTRA STRENGTH · 4× MORE NATTOKINASE · 120 VEGETARIAN CAPSULES" — packaging itself communicates positioning.

### E. Distinct/clever moves — top 5
1. **PubMed citations in the FAQ.** Direct NIH study links for every ingredient. Converts skeptics. Almost no competitor does this.
2. **McCullough Protocol™ as a named, trademarked methodology.** Makes the SKU defensible and unforgeable — competitors can sell nattokinase but they can't claim to be "the McCullough formula".
3. **AI summary at top of the reviews module.** Pre-digests 100+ reviews into one paragraph for skim-readers.
4. **HSA/FSA badge (TrueMed integration).** Removes a 30%+ tax-advantage friction.
5. **Sticky ATC bar.** Detected with class `sticky-atc` — keeps the buybox alive on scroll for a 4000px+ page.

### F. Mobile UX notes (TWC)
- **Sticky ATC bar** on mobile (class `sticky-atc`) — title + rating + Add to Cart pinned to bottom.
- About / Ingredients / How To Take are collapsing accordions (good — shrinks the page meaningfully).
- The "Approved by Chief Medical Board" doctor banner stacks vertically and stays impactful.
- FAQ collapses well.
- "Featured On" press logos stay one-per-row on the smallest mobile.
- No autoplay video on mobile (smart for data + bounce).
- Floating "UNLOCK 15% OFF" tab still appears as a corner pill on mobile.

---

## 3. Comparative Synthesis

### What Resilia does that TWC does NOT
- **Hides price** until ATC click (funnel commitment device).
- **Big-number customer survey grid** (49/56/72/86%) as a discrete section.
- **Multi-month expectation timeline** (1 week → 12 months).
- **Two FAQs** (compact-near-hero + long-near-bottom).
- **8 payment-method logos** directly under the ATC.
- **"Not on Amazon or eBay"** scarcity line.
- **Three benefit micro-cards inside the gallery column** (a 3-col hero).
- **"TODAY ONLY: UP TO 70%"** explicit price anchor in the offer.

### What TWC does that Resilia does NOT
- **Real reviews widget** (303 verified reviews, AI summary, sortable, photo reviews).
- **PubMed citations** in the FAQ.
- **Named doctor authority** (Dr. McCullough) + trademarked protocol.
- **Approved by Chief Medical Board** doctor-row banner.
- **Press logos** ("Featured On" row).
- **Sticky ATC bar** on mobile.
- **HSA/FSA eligibility** badge (TrueMed).
- **Subscribe-or-One-Time toggle** with default = Subscribe.
- **Cross-sell carousel** ("You May Also Like") for AOV expansion.
- **Floating "Unlock 15% Off"** email-capture tab.
- **Drug-interaction warning** ("Do Not Use if Pregnant, Nursing, or on Blood Thinners") that paradoxically increases trust.
- **Visible price + strike-through** for anchoring.
- **Bottle-label-as-positioning** (4× More Nattokinase / Extra Strength / 120 Vegetarian Capsules etc. printed on the product itself).

### What the current `gmmehe-01` PDP MISSES (presumed-default Shopify)
Since the live page is gated, this list assumes a vanilla Shopify Dawn-style template (single-column gallery + title + price + ATC + description + reviews app). At minimum, the new PDP must add:

1. **No named clinical authority.** No equivalent of "McCullough Protocol™". Spike-detox audiences buy the *doctor*, not the formula — without an authority figure (yours or a partner clinician) the page has no moat.
2. **No PubMed citations.** Skeptical, research-driven buyers will not convert without direct NIH links for nattokinase, bromelain, dandelion root, turmeric, etc.
3. **No quantified outcomes in the hero.** Both references lead with %s — your buybox bullets need 3-4 falsifiable numbers (e.g. "X% of users reported Y in Z weeks").
4. **No subscribe-and-save toggle with default = subscribe.** Subscription LTV is the entire game in supplements; default selection drives ~50% sub take-rate.
5. **No sticky ATC on mobile.** TWC has it; long pages without it bleed conversions on iOS.
6. **No bundle ladder (1/3/6 packs).** Resilia hides one ("Buy 2 Get 1 Free"); TWC has none — but a single-SKU long-form product with no ladder leaves AOV on the table. *Recommendation:* expose a 1/3/6 ladder with the 3-pack as the visually default "Most Popular".
7. **No expectation-setting timeline.** Spike-detox is an 8-12 week mechanism (fibrinolytic enzymes need consistent dosing). A "Week 1 → Month 3 → Month 6" timeline pre-sells the 6-pack.
8. **No "Featured On" press logos or doctor-board banner.** The audience trusts faces and outlets.
9. **No comparison table vs. competitor / vs. typical detox protocols.** A 7-row VS table (e.g. vs. typical nattokinase blends — yours has 4× nattokinase, X enzymes, etc.) anchors price.
10. **No HSA/FSA eligibility callout.** Easy install via TrueMed; major friction reducer.
11. **No verified-buyer review widget.** The store appears too new to have organic reviews — seed via a launch program before opening the gate.
12. **No floating discount-capture tab.** "Unlock 15%" / email gate captures bouncers.
13. **No ingredient-mechanism tile grid.** Each ingredient needs a one-line "what it does" — buyers Google ingredients during checkout if you don't.
14. **No risk reversal next to the buybox.** "Feel better or it's free" / 30-day money-back must be visible at-CTA, not buried in a footer policy page.

### Which reference is the better template for spike-detox?
**TWC is the closer template** for the core PDP scaffold because:
- Same audience (post-COVID, doctor-trusting, research-skeptical).
- Same mechanism category (fibrinolytic enzymes + plant antioxidants).
- Same need for clinical-grade trust (PMC citations + doctor authority).
- Same buybox economics (single SKU + subscribe-first).

**But borrow Resilia's narrative spine** — specifically:
- The 6-step expectation timeline.
- The big-number survey grid.
- The 3-card mini-benefits inside the hero gallery column.
- The compact FAQ near the buybox.
- The "Buy 2 Get 1" framing if you don't want a public 1/3/6 ladder.

### Recommended hybrid section order — ideal blueprint

```
1.  Promo/announcement bar — multi-message rotator (free shipping, study link, sale)
2.  Hero
    - Gallery (left, 60/40 desktop)
    - 3 micro-benefit cards in middle column (Resilia move)
    - Title + Doctor/Protocol attribution sub-headline (TWC move)
    - 4-bullet quantified-outcome list with %s
    - Buybox: 1/3/6 pack ladder + Subscribe/One-Time toggle (default Subscribe = Save 15%)
    - Risk reversal line + payment logos
    - "Ships from USA" + "Made in FDA-Registered cGMP facility" badges
3.  Sticky ATC mobile bar (TWC) + floating "Unlock 15% Off" capture tab (TWC)
4.  Compact FAQ (6 questions — Resilia move)
5.  HSA/FSA Eligible strip (TWC)
6.  "Featured On" press logo row (TWC)
7.  Authority section: doctor portrait + protocol explanation (TWC)
8.  Big-number customer survey grid (Resilia) — reuses any user-data you have (X% reported Y)
9.  Problem agitation: "Why a Spike Detox Now?" — long-COVID + cardiovascular angle, 2-col with lifestyle photo
10. Mechanism: "The 4-Pillar Spike Protein Detox" — show the protocol pathway (degrade → bind → eliminate → repair)
11. Ingredient tile grid (TWC) — Nattokinase / Bromelain / Dandelion / Turmeric / Selenium / Black Seed / Black Pepper — each with mechanism + PMC link
12. Comparison VS table (Resilia/TWC) — your 4× Nattokinase vs. typical blends
13. Expectation timeline (Resilia) — Week 1 → 12 Months
14. Repeat ATC + Risk reversal
15. Heavy testimonials block — 5 cards, include at least one with a clinical credential ("RN", "MD", "PA")
16. Reviews widget — Yotpo/Judge.me with photo reviews + AI summary
17. "Approved by Medical Board" doctor banner (TWC)
18. Long FAQ with PMC citations (TWC) — this is non-negotiable for the spike audience
19. Cross-sell row — "You May Also Like" (TWC) — anchor pricier bundle
20. Final ATC + footer guarantee strip
```

### Gap-priority shortlist (ship-first)
If you can only build five things before un-gating the store, build these:
1. Subscribe & Save toggle (default subscribe) + a 1/3/6-pack visual ladder.
2. Doctor/protocol authority block with portrait — even if you license/partner.
3. Long FAQ with **at least 8 PubMed citation links** for the spike-protein ingredients.
4. Sticky ATC bar on mobile + floating 15%-off email tab.
5. Big-number customer-outcome strip + 6-step timeline.

These five address the cardiovascular/long-COVID buyer's three highest-friction questions: "Is the doctor real?", "Is the science real?", and "Is it worth the commitment?"

---

## 4. Save Confirmation

- Doc saved to: `/Users/jonas/Documents/IDE/Health Store/docs/superpowers/specs/research-pdp-teardown.md`
- Screenshots saved to: `/Users/jonas/Documents/IDE/Health Store/.playwright-mcp/spike-detox-teardown/`
  - `01-current-pdp-password-page.png`
  - `02-resilia-desktop-fullpage.png`
  - `02-resilia-snapshot.md`
  - `03-resilia-mobile-fullpage.png`
  - `04-twc-desktop-fullpage.png`
  - `05-twc-mobile-fullpage.png`
  - `06-twc-hero-desktop.png`
  - `07-resilia-hero-desktop.png`
- Current PDP could not be torn down — store is password-protected. Re-run after un-gating or share the storefront password.
