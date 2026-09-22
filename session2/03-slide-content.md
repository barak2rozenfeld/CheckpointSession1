# Session 2 — Slide Content (per slide)

> Draft copy for each slide, English, CISO audience. Numbers marked `[verify]` must be confirmed from `research/` before building. Punchlines are the `.thread` line.

> **Sourcing rule (mandatory):** every slide that shows a number, claim, or comparison must carry **both at least one source and at least one assumption** in its `.src.box` block. No number without a source; no number without a stated assumption. Hero / pure-narrative slides carry no block.

---

## MAIN DECK

### Slide 1 · Hero
- **Title:** See Everything. Slow Nothing.
- **Sub:** Quantum 9000 for the Enterprise
- **Foot:** Barak Rozenfeld · Senior PM
- Visual: near-empty stage, Check Point logo, one large line. No data block.

### Slide 2 · The New Threat Reality
- **Eyebrow:** Why now
- **Title:** The threats you care about are hiding where you can't look.
- **Body:** 90%+ [verify] of enterprise traffic is now encrypted. Attackers use TLS to hide payloads. AI has lowered the cost of attacks and widened the surface (hybrid, remote, SaaS).
- **Punch:** "You can't protect what you can't see."
- **Sources & assumptions:** encrypted-traffic stat [verify] · threat-trend source [verify].

### Slide 3 · The CISO's Dilemma
- **Eyebrow:** The trade-off you live with today
- **Title:** Two bad choices. Every day.
- **Body (fork):**
  - Inspect everything -> throughput drops 50-70% [verify] -> users feel it -> the business pushes back.
  - Stay fast -> TLS inspection off -> blind to most of your traffic.
  - The hardware trap: over-provision (wasted CapEx) or under-size (forklift upgrade).
- **Engagement:** live toggle Firewall -> NGFW -> TP -> TP+TLS, throughput bar collapses on the last step.
- **Punch:** "Today, security is a tax on your business. It should not be."

### Slide 4 · Meet Quantum 9000
- **Eyebrow:** The platform
- **Title:** One box. Prevention-first. Fully managed.
- **Body (outcome badges, not specs):** 1RU footprint · Unified management (SmartConsole) · ThreatCloud AI shared intelligence · Prevention-first (block, don't just alert).
- **Sources & assumptions:** Check Point Appliance Comparison Chart.

### Slide 5 · Differentiator 1 — Best-in-class prevention
- **Eyebrow:** Differentiator 1 / 3
- **Title:** You catch what others miss.
- **Body:** Independent testing: 99%+ [verify] block rate (CyberRatings). ThreatCloud AI turns every blocked attack across the install base into protection for you. Prevention-first, not detect-and-hope.
- **Punch:** "Measurable risk reduction, validated by third parties."
- **Sources & assumptions:** CyberRatings comparative test · Check Point ThreatCloud AI page · **Assumptions:** 99%+ figure is the reported block rate from the cited test cycle [verify]; "prevention-first" is a posture claim, not a benchmarked number.

### Slide 6 · Differentiator 2 — One unified platform
- **Eyebrow:** Differentiator 2 / 3
- **Title:** Fewer tools. Fewer blind spots.
- **Body:** One console for policy, logs, and threat intel across network, cloud, and users. Consolidation lowers tool sprawl, closes the gaps between point products, and reduces the headcount needed to run it.
- **Visual:** 6-7 separate vendor boxes on the left -> one clean unified box on the right.
- **Punch:** "Consolidation is not just cheaper. It is safer."

### Slide 7 · Differentiator 3 — Future-proof by design (feature as benefit)
- **Eyebrow:** Differentiator 3 / 3 · The feature
- **Title:** ×4 performance. Same box. Just add a card.
- **Body:** A BlueField-3 DPU card drops into the empty PCIe slot of the 9700/9800 and offloads TLS + IPS/AV to dedicated silicon. Result: ~10 -> ~40 Gbps [verify: PoC target] full TP+TLS in the same 1RU. Same GAiA, same SmartConsole, same rack.
- **Engagement:** chassis before/after animation (empty slot -> card), gauge 10 -> 40.
- **Punch:** "A field upgrade, not a forklift."
- **Sources & assumptions:** BlueField-3 datasheet · AIFF PR · CP Appliance Chart · PoC targets are lab-validation goals, not shipping numbers.

### Slide 8 · The Twist — Buy once, keeps growing (Capacity-on-Demand)
- **Eyebrow:** The part competitors can't copy
- **Title:** The slot is your roadmap.
- **Body:** Because it is programmable NVIDIA silicon, not a burned-in ASIC, that one slot keeps giving:
  - Today: ×4 throughput with full TLS.
  - Tomorrow: post-quantum crypto.
  - Next: on-box AI threat detection on the DPU's Arm cores (does not tax the main CPU).
  - All as field upgrades, on the box you already own.
- Contrast: Fortinet's ASIC is fixed. A new engine means a new box.
- **Engagement:** roadmap timeline unfolding on the same card.
- **Punch:** "Investment protection. Cloud-like elasticity, on hardware you own."
- **Sources & assumptions:** BlueField-3 datasheet (programmable Arm cores) · NIST post-quantum program · Check Point AIFF PR · **Assumptions:** post-quantum and on-box AI are roadmap targets, not shipping features; the "field upgrade" path assumes the abstraction layer described in Session 1.

### Slide 9 · Proof
- **Eyebrow:** Don't take my word for it
- **Title:** Validated. Deployed. Proven.
- **Body (3 anchors):** CyberRatings 99%+ [verify] independent block rate · TCO advantage from consolidation and density [verify numbers] · BlueField already runs Check Point's AIFF in production today (the silicon is not a science project).
- **Engagement:** interactive TCO chart (CP vs Fortinet vs PA).
- **Sources & assumptions:** CyberRatings · TCO source [verify] · AIFF PR.

### Slide 10 · The CTA
- **Eyebrow:** Your next step
- **Title:** Prove it on your traffic. 30 days.
- **Body:** A 30-day POC in your environment: prove the ×4 with full TLS inspection on your own traffic, and map your 3-year upgrade path on the same hardware. Join the DPU design-partner program.
- **Punch:** "You keep the box. We'll show you how far it goes."

---

## APPENDIX — "ready if asked"

### A1 · vs Fortinet / Palo Alto
- Head-to-head, normalized per 1RU: CP 9800+DPU ~40 [verify] vs Fortinet ~17 vs PA ~18 TP+TLS per RU. Plus prevention block rate and single-console management. (Data from Session 1 fork slide.)

### A2 · Prove the TLS performance
- Methodology: distinguish Firewall Gbps from Threat Prevention Gbps; lab vs enterprise testing conditions; TP with TLS inspection is the honest, hardest number. Offer to run it live in the POC.

### A3 · Migration to your existing stack
- No rip-and-replace: same GAiA, same SmartConsole, phased policy cutover. The DPU is additive; NIC, firewall ASIC, and policy stay in place.

### A4 · Aren't you more expensive than Fortinet?
- TCO, not sticker price: one 1RU box does the work of a competitor stack; ×4 in the same rack; power and cooling per RU; fewer tools to license and staff. Show the 3-year TCO [verify numbers].

### A5 · NVIDIA lock-in?
- An abstraction layer in GAiA keeps the gateway silicon-agnostic; gen 2 can target Intel IPU or AMD Pensando. (Answer already framed in Session 1.)

### A6 · Is the card available? When?
- Honest: it is a roadmap proposal (FY27), validated first in a PoC, then a design-partner program. The 9000 you buy today is already best-in-class; the card is upside you help shape.

### A7 · Compliance and certifications
- FIPS 140-3 (crypto module), Common Criteria EAL, and the regulatory posture relevant to finance / healthcare / public sector.

---

## Numbers to lock before building (from research/)
- Encrypted-traffic % (slide 2).
- CyberRatings block rate (slides 5, 9, A1).
- TCO figures CP vs Fortinet vs PA (slides 9, A4).
- Per-1RU TP+TLS comparison (slide 7, A1) — Session 1 uses CP+DPU ~40, FTN ~17, PA ~18.
