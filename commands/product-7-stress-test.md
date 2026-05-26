---
description: Multi-POV antagonist that tests the investment thesis behind a product artifact. Hunts for logical errors, omissions, hidden assumptions, planning ambiguity, and missing proofs of market/concept/viability. Writes ./artifacts/stress-tests/<target>-<date>.md.
---

# /product-7-stress-test [target]

You are running the antagonist gate. At this stage in the playbook, the team has a clear vision. The question to answer is: **is the opportunity real?**

Your job is to test the investment thesis. You're hunting for:

- **Logical errors** — claims that don't follow from the premises
- **Omissions** — what's missing from the artifact that should be there
- **Hidden assumptions** — what the artifact takes for granted that may not be true
- **Ambiguity in planning** — what's vague where it should be specific
- **Missing proofs** — proof of market, proof of concept, proof of viability — and call them out when absent

You will form **theories of failure** — specific hypotheses for how this fails — and ask the **sharp questions that test them**.

The output is a structured stress-test artifact with a recommendation: Proceed / Proceed-with-changes / Pause / Kill. The recommendation is a vote, not a veto — the user decides.

This command implements the methodology of the `tear-it-apart` skill bundled with this plugin, with the investment-thesis lens applied. Reference the `tear-it-apart` SKILL.md (in this plugin) for the underlying multi-POV approach if helpful.

---

## Step 1 — Intro the job to be done

Open with this orientation:

> **Stress-test.** Now that we have a clear vision, we test the investment thesis. Is the opportunity real? I'll walk through this from multiple POVs — Customer, Market, Sponsor, Technical, Operator, Product, Devil's Advocate — looking for logical errors, omissions, hidden assumptions, and ambiguity. Each POV owns a specific *proof* to check: demand, market, viability, concept, operability, fit. Where proof is missing, I'll call it out. I'll form theories of failure and ask the questions that test them.
>
> The output is a recommendation: Proceed, Proceed-with-changes, Pause, or Kill. That's a vote, not a veto — you decide.
>
> Output: `./artifacts/stress-tests/<target>-YYYY-MM-DD.md`.

## Step 2 — Resolve the target

The command may be called with an explicit target:

- `/product-7-stress-test ./artifacts/PRFAQ.md` — explicit path
- `/product-7-stress-test mvp-scope` — short name (resolves to `./artifacts/mvp-scope.md`)
- `/product-7-stress-test` — no arg

Resolution rules:

1. **Explicit path:** use it directly. Verify it exists.
2. **Short name:** look for `./artifacts/<name>.md` first. If not found, try common variants (e.g., `gtm` → `gtm-plan.md`).
3. **No arg:** infer the most recent artifact in `./artifacts/` that hasn't been stress-tested yet. Check `./artifacts/product-log.md` for prior stress-test entries to skip already-tested artifacts. If multiple candidates, list them and ask the user to pick.

If the target can't be resolved, stop and ask the user to specify.

## Step 3 — Read context

Load:

- **The target artifact** — the thing being attacked.
- **Upstream context** — load any of these that exist: `./artifacts/PRFAQ.md`, `./artifacts/one-pager.md`, `./artifacts/gtm-plan.md`, `./artifacts/mvp-scope.md`, `./artifacts/principles.md`. These provide the surrounding investment thesis for cross-referencing.
- **Prior stress-tests on this target** — if `./artifacts/stress-tests/` has previous results for this artifact, read the most recent one. Don't repeat surfaced flaws that have been resolved; do call out flaws that were noted before but remain unresolved.

## Step 4 — Frame the investment thesis

Before attacking anything, state the thesis being tested. Pull from PRFAQ + GTM (and the target itself). One paragraph covering:

- **Customer:** who is this for (specific segment)?
- **Pain:** what pain is being relieved?
- **Why now:** what changed in the world that makes this reachable today?
- **Unit of value + price:** what's the unit being sold and at what price?
- **Why us:** what's our differentiation / unfair advantage?
- **Success:** what does "this worked" look like in concrete terms?

If you can't state the thesis cleanly because the source artifacts don't support a clean statement, **that is flaw #1**. Capture it: "Investment thesis is not clearly statable from current artifacts. Specifically, the [customer / pain / why-now / etc.] is fuzzy."

## Step 5 — Pick POV lineup

Default lineup by target:

| Target | POV lineup |
|---|---|
| PRFAQ / vision | Product, Customer, Sponsor, Market, Devil's Advocate |
| GTM plan | Customer, Sponsor, Market, Devil's Advocate |
| MVP scope | Sponsor, Technical, Operator, Customer |
| Other / free-form | Product, Customer, Sponsor, Technical, Operator, Market, Devil's Advocate (full lineup; let user trim) |

**Announce the lineup before starting.** Tell the user which POVs you're running and why. If the user wants to add, drop, or reorder POVs, accept the override.

## Step 6 — Walk each POV (sequential, investment-thesis lens)

For each POV in turn, produce this structured output. Be specific. Quote the artifact when you call out a flaw. Vague flaws ("might not work," "risky") are not flaws — name the failure mode, the specific number, the specific assumption.

```
### {POV name}

**Posture:** {one-line stance this role takes when reviewing the investment thesis}

**Logical errors:**
- {0–3 specific items, with quotes from the artifact}

**Omissions:**
- {0–3 items — what's missing that should be here}

**Hidden assumptions:**
- {0–3 items — what does this take for granted}

**Ambiguity in planning:**
- {0–3 items — what's vague where it should be specific}

**Missing proofs:** (POV-specific — see proof ownership below)
- {0–2 items — name the proof that's missing}

**Theory of failure:** {one specific hypothesis for how this fails, from this POV's lens}

**Question that tests the theory:** {one sharp question — the answer would update belief}
```

### POV-specific proof ownership

Each POV owns a specific *proof* to check. When that proof is absent or weak, the POV calls it out.

- **Customer** → **Proof of demand.** Would real customers pull this off the shelf? Is there evidence they want it (interviews, signups, prior product attempts, search behavior)? Or is demand assumed?
- **Market** → **Proof of market.** Is the TAM/SAM real, defensible, and growing? Are the sizing assumptions sourceable? Are competitors validating the market — or is the absence of competitors a signal there's no market?
- **Sponsor** → **Proof of viability.** Do the unit economics survive scrutiny? Payback period? Margins at v0.1 pricing? ROI sensitivity to the riskiest assumption? Capital intensity?
- **Technical** → **Proof of concept.** Can this actually be built? Scaled? Integrated? What's the build cost, and is it tractable? For AI products: is the eval/quality risk real, and how is it being mitigated?
- **Operator** → **Proof of operability.** Can this be run within budget without manual heroics? Support burden per customer? Runbook gaps? On-call risk? Manual toil baked into the design?
- **Product** → **Proof of fit.** Does the product actually solve the JTBD as stated? Is the unit of value aligned with the customer's perceived value? Is the MVP a coherent slice that delivers value as-is?
- **Devil's Advocate** → **Strongest case the thesis is wrong.** What's the smallest assumption that, if false, kills it? What would have to be true for this idea to be correct? If you had to bet against, what's your bet?

### Tone discipline

- Specific, not vague. Quote the artifact.
- Direct, not cruel. "This pricing decision will cause churn at renewal because the unit doesn't align with customer-perceived value" beats "your pricing is dumb."
- One sharp question per POV is worth more than a paragraph of hedging.
- If a POV genuinely finds nothing, say so. Don't manufacture flaws to fill the slot.

## Step 7 — Cross-POV synthesis

After all POVs are done, write:

### Convergent flaws
The 2–3 flaws that multiple POVs hit. These are the load-bearing problems. Rank them.

### Strongest theory of failure
The one theory of failure that, based on cross-POV evidence, is most likely to come true. State it as a single sentence. State the evidence for it.

### Load-bearing assumptions
The assumptions that, if false, collapse the thesis. List the top 3. For each: what's the assumption, what's our current evidence for or against it, and what experiment would resolve it.

### Proofs summary
A clean two-column summary:
- **Proofs we have:** market / concept / viability / demand / operability / fit — list which we can claim, with what evidence
- **Proofs we're missing:** the gaps — these become the work that gates the next phase

## Step 8 — Block-and-tackle

For each top flaw (from convergent flaws + strongest theory of failure), write a concrete move:

- **Flaw:** <restate>
- **Move:** <specific action — interview N customers, build N prototype, source M data, etc.>
- **Owner:** <named person — not "the team">
- **How you'd know it worked:** <what signal closes the flaw>

These become the must-do list before moving forward.

## Step 9 — Recommendation

Pick one and state it bluntly:

- **Proceed** — flaws are known and have owners. Ship the next phase.
- **Proceed with changes** — name the must-fix items before the next phase.
- **Pause** — a load-bearing assumption isn't supportable from current evidence. Name the experiment that would resolve it. Don't proceed until that experiment runs.
- **Kill** — the idea has a fatal flaw with no plausible fix. Name it bluntly.

State the recommendation in one paragraph. Then the structured artifact gets written.

## Step 10 — Write the artifact

Path: `./artifacts/stress-tests/<target-base>-YYYY-MM-DD.md`

Where `<target-base>` is the target file's base name without `.md` (e.g., `PRFAQ`, `mvp-scope`, `gtm-plan`).

If `./artifacts/stress-tests/` does not exist, create it.

Use this structure:

```markdown
# Stress test — <target>

*Run date: YYYY-MM-DD*
*Target: <path to target artifact>*
*POV lineup: <list>*

## Investment thesis under test
<One paragraph>

---

## POV passes

### <POV 1>
**Posture:** <...>
**Logical errors:** <...>
**Omissions:** <...>
**Hidden assumptions:** <...>
**Ambiguity in planning:** <...>
**Missing proofs:** <...>
**Theory of failure:** <...>
**Question that tests the theory:** <...>

### <POV 2>
<same structure>

...

---

## Cross-POV synthesis

### Convergent flaws
1. <...>
2. <...>
3. <...>

### Strongest theory of failure
<One sentence + evidence>

### Load-bearing assumptions
1. <Assumption> — Evidence: <...> — Experiment to resolve: <...>
2. ...

### Proofs summary
**Proofs we have:**
- <Market / concept / viability / demand / operability / fit> — <evidence>

**Proofs we're missing:**
- <...>

---

## Block-and-tackle

| Flaw | Move | Owner | How we'd know it worked |
|---|---|---|---|
| <...> | <...> | <...> | <...> |

---

## Recommendation

**<Proceed / Proceed with changes / Pause / Kill>**

<One paragraph rationale>
```

## Step 11 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with this header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append:

```
YYYY-MM-DD HH:MM PST — /product-7-stress-test — ./artifacts/stress-tests/<target>-<date>.md — <Recommendation>
```

Use the actual current local PST time. If unsure of the timezone, ask once and remember.

## Step 12 — Tell them what's next

Branch based on recommendation:

- **Proceed:**
  > Next: continue to the next playbook phase. If you just stress-tested PRFAQ → `/product-3-gtm`. If you just stress-tested MVP scope → `/product-8-engineering-handoff`.

- **Proceed with changes:**
  > Next: address the must-fix items in the block-and-tackle list. Re-run the upstream command(s) where needed (e.g., `/product-2-vision` if PRFAQ needs revision). Then continue.

- **Pause:**
  > Next: name the experiment that resolves the load-bearing assumption(s). Run it. Do not proceed to the next phase until the experiment returns a result that either confirms the assumption or kills the thesis.

- **Kill:**
  > Next: consider a pivot (which kept assumptions could form a different thesis?) or close the project cleanly. If pivoting, restart from `/product-2-vision` with the new direction.
