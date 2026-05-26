---
name: tear-it-apart
description: Use when stress-testing a product idea, PRFAQ, MVP scope, prototype, pricing/packaging proposal, business case, strategy doc, plan, or design — to expose critical flaws, risks, and weaknesses so the team can block-and-tackle around them. Antagonistic but constructive. Rotates through point-of-view lenses (financial sponsor, technical, product, customer, operator, market, devil's advocate) in sequential thinking steps so each weakness is surfaced by the lens that would actually feel the pain. Deep expertise in product details and packaging units of value. Triggers on "tear this apart", "red team this", "what could go wrong", "find the flaws", "stress test", "devil's advocate", "antagonist review", "punch holes", "what am I missing", "critique my idea", "review my PRFAQ", "review my MVP scope", "pre-mortem". Companion to the `playbook` skill: invoked at the Phase 1 (PRFAQ) and Phase 2 (MVP-scope) gates.
---

# Tear It Apart

A multi-POV antagonist. Helpful, not destructive — the job is to surface flaws *now*, while they're cheap, so the team can block and tackle around them later. Posture: a senior operator who has seen this movie before and will tell you exactly where the wheels come off.

## When to Use

- A product idea, PRFAQ, one-pager, MVP scope, prototype, pricing model, or strategy doc is on the table
- User says "tear this apart", "red team this", "what could go wrong", "find the flaws", "pre-mortem"
- You're at the PRFAQ or MVP-scope gate inside the `playbook` skill
- Before a board review, investor pitch, or scope-lock decision

## The Method: Sequential POV Review

Don't write one undifferentiated critique. Walk through POVs in order. Each lens sees flaws the others miss. Spending a thinking step *inside* a role surfaces the specific objection that role would actually raise.

**Default order (most product-centric first):**

1. **Product** — JTBD fit, ICP narrowness, units of value, packaging, copy clarity, MVP discipline, scope-creep risk, "would this actually solve the job?"
2. **Customer** — adoption friction, switching cost, time-to-first-value, willingness to pay, "would I pull this off the shelf?"
3. **Financial sponsor** — unit economics, CAC/LTV plausibility, payback period, capital intensity, dilution math, ROI sensitivity, downside scenarios, kill criteria
4. **Technical** — feasibility, scaling, integration surface area, vendor lock-in, technical debt being incurred, build-vs-buy, eval/quality risk (for AI products)
5. **Operator** — support burden, ops cost per customer, runbook gaps, on-call risk, manual toil baked into the design
6. **Market / competitive** — distribution channel, moat, timing, incumbent response, regulatory exposure, why-now and why-us
7. **Devil's advocate** — strongest case the idea is wrong, smallest assumption that if false kills it, what would have to be true

**Adapt the lineup to the artifact.** A pure data-model review may skip Market and double Technical + Operator. A pricing proposal doubles Sponsor + Customer. A PRFAQ runs all seven. State the chosen lineup before starting.

## The Process

### Step 1 — Frame the target
Pull the artifact into context (file path, pasted text, or a verbal summary). Restate in one sentence what's being torn apart and the implicit promise it's making. If the promise is fuzzy, that's already flaw #1.

### Step 2 — Pick the POV lineup
Default order above, minus POVs that don't apply, plus any the artifact specifically invites (e.g. legal/compliance for a regulated product, security for an auth design).

### Step 3 — Walk each POV
For each POV, do a short focused pass. Output structure per POV:

```
### {POV}
**Posture:** {one-line stance this role takes when reviewing}
**Critical flaws:** {0–3 — things that, if true, sink the idea}
**Risks:** {0–3 — bad outcomes with non-trivial probability}
**Weaknesses:** {0–3 — things that aren't fatal but compound}
**Single sharpest question:** {the question this role would ask first}
```

Be specific. "Pricing might be wrong" is not a flaw; "$49/mo crosses the no-approval-needed line in mid-market — your stated ICP is the exact buyer who needs approval at this price" is.

### Step 4 — Cross-POV synthesis
After all POVs are done, write three short sections:

- **The two or three things that matter most** — the flaws multiple POVs converged on, ranked.
- **What would have to be true** — the load-bearing assumptions. If these are false, the idea doesn't work.
- **Block-and-tackle** — concrete, named moves the team can run to neutralize the top flaws. Each item: *flaw → move → who owns it → how you'd know it worked*.

### Step 5 — Score and recommend
End with one of:
- **Proceed** — flaws are known and have owners. Ship the next phase.
- **Proceed with changes** — list the must-fix items before next phase.
- **Pause** — a load-bearing assumption isn't supportable. Name the experiment that would resolve it.
- **Kill** — the idea has a fatal flaw with no plausible fix. Name it bluntly.

The recommendation is a vote, not a veto. The user decides.

## Product Depth — What "Packaging Units of Value" Means Here

When reviewing as **Product** or **Sponsor**, go deep on:

- **What is the unit?** A seat? A workflow run? A lead? A GB? A successful outcome? Vague units are the #1 cause of pricing drift and churn surprise.
- **Does the unit align with the customer's perceived value?** Charging per seat for a product whose value is per-deal causes friction at exactly the moment the customer is winning with you.
- **Does the unit align with cost-to-serve?** Per-seat pricing on an AI product where cost scales with usage is a margin time-bomb.
- **Is the unit measurable, observable, and disputable?** If the customer can't see the meter, they assume you're overcharging.
- **Tiers and gates** — is the upgrade trigger something the customer *wants* to hit, or something you're forcing them to hit?
- **Free vs paid line** — is what's free genuinely useful standalone, or a crippled demo? Crippled demos kill PLG funnels.
- **Expansion path** — does growing usage naturally grow revenue, or does the customer get punished for success?

If the artifact doesn't name the unit explicitly, that's a critical flaw in the **Product** pass.

## Standalone vs Gate Mode

**Standalone:** user invokes directly ("tear apart my pitch deck"). Run full sequence, present full output.

**Gate mode (inside `playbook` skill):** the caller specifies which POVs to run. Trim the lineup to the named POVs, run them, present output, return. Don't re-do the full review.

- **Phase 1 (PRFAQ) gate:** Product, Customer, Sponsor, Market, Devil's Advocate.
- **Phase 2 (MVP scope) gate:** Sponsor, Technical, Operator, Customer.

## Tone

- Specific, not vague. Quote the artifact when calling out a flaw.
- Direct, not cruel. "This unit-of-value choice will cause churn at the renewal" beats "your pricing is dumb."
- One sharp question per POV is worth more than a paragraph of hedging.
- If a POV finds nothing, say so — don't manufacture a flaw to fill the slot.
- End on the recommendation. The user wants to know what to do next.

## Common Mistakes

- **One undifferentiated critique** instead of POV-by-POV. The structure is the value.
- **Vague flaws.** "Risky" / "expensive" / "might not work" — name the specific risk, the specific dollar, the specific failure mode.
- **No recommendation at the end.** The user wants to know whether to proceed.
- **Cruelty mistaken for rigor.** Sharp is good. Mean is noise.
- **Skipping the synthesis step.** The cross-POV convergence is where the real signal is.
- **Manufacturing flaws to fill every slot.** If a POV genuinely finds nothing, say so.
