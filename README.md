# product-playbook

A Claude Code plugin for taking a product idea from "I think there's something here" to first paying customer and beyond — distilled from real solo-builder + AI-team work, May 2026.

Two skills, one disciplined sequence.

## What's in the box

### `playbook` — 11-phase product playbook (Phase 0 → Phase 10)

A repeatable sequence of phases, each producing concrete **artifacts** that unlock the next phase:

| Phase | Outcome |
|------|---------|
| **0** Durable principles | Non-negotiables captured as feedback memories |
| **1** PRFAQ + one-pager | 60-second pitch + named first-three customers + required FAQ questions answered |
| **2** MVP scope with numbers | Defensible scope (budget, geo, source count) with antagonist sign-off |
| **3** Access-pattern-first data model | Single-table design defended by access patterns, not aesthetics |
| **4** Meta-feedback as you go | Durable guidance saved as `feedback_<topic>.md` memories |
| **5** Clickable prototype | Local URL the user can click through in 10–20 minutes |
| **6** Iterate on the prototype | Respond to clicks; centralize variation in `lib/<x>-norms.ts` |
| **7** Verify and close the loop | Click-through verification + new feedback memories captured |
| **8** Bootstrap the buildable backlog | Repo + labels + milestone + issue template + decisions-resolution loop |
| **9** Plan path to first paying customer | First-three nailed down; pilot agreement signable; pilot→paid go/no-go bar |
| **10** Plan path to scale | Pricing tiers, sales motion, growth thesis, org plan, kill criteria |

Two antagonist gates — at **Phase 1 (PRFAQ)** and **Phase 2 (MVP scope)** — call the `tear-it-apart` skill. These are the cheapest places in the whole sequence to find a fatal flaw.

### `tear-it-apart` — multi-POV antagonist

A constructive antagonist that rotates through point-of-view lenses in sequential thinking steps:

- **Product** — JTBD fit, units of value, packaging, scope discipline
- **Customer** — adoption friction, time-to-first-value, willingness to pay
- **Financial sponsor** — unit economics, payback, ROI sensitivity, kill criteria
- **Technical** — feasibility, scaling, integration surface, vendor lock-in
- **Operator** — support burden, runbook gaps, manual toil
- **Market / competitive** — moat, timing, incumbent response, why-now
- **Devil's advocate** — strongest case the idea is wrong

Each POV produces critical flaws, risks, weaknesses, and one sharp question. Cross-POV synthesis identifies the load-bearing assumptions. Ends with a Proceed / Proceed-with-changes / Pause / Kill recommendation.

Deep expertise in **packaging units of value** (what's the unit, does it align with customer-perceived value, does it align with cost-to-serve, is it measurable/observable/disputable, what's the upgrade trigger, what's the free-vs-paid line, what's the expansion path).

Works **standalone** (any time a doc, design, plan, or strategy needs stress-testing) or **inside `playbook`** as a phase gate.

## Installation

This plugin follows the Claude Code plugin format. With Claude Code's plugin system:

```bash
# Clone or copy this directory into your plugins location, then enable it
# via /plugin in Claude Code, or wire it in directly via your settings.
```

Once installed, the skills become available to Claude. Trigger them by:

- **The playbook**: ask Claude about "PRFAQ", "MVP scope", "build a prototype", "buildable backlog", "first paying customer", or "scale plan" — or invoke directly with `/product-playbook:playbook`.
- **Tear it apart**: ask Claude to "tear this apart", "red team this", "find the flaws", "pre-mortem this" — or invoke directly with `/product-playbook:tear-it-apart`.

## Who this is for

- **Solo builders and small teams** taking a new product from idea to first customers
- **Builders working with an AI team** (Claude, agents) who want a repeatable scaffolding
- **Anyone** who has felt the pain of a prototype that drifted from the PRFAQ, an MVP that ballooned, or a launch that hit avoidable flaws on day one

## What this is *not*

- A heavyweight enterprise PDLC framework — this is sized for one or two builders + AI
- Tech-stack opinionated beyond the Phase 5 prototype defaults (Next.js / Tailwind / localStorage)
- Production-operations focused — Phase 10 is the last phase covered; ongoing operations are out of scope

## Philosophy

- **Artifacts > conversation.** Every phase outputs a concrete artifact. If you don't have the artifact, the phase isn't done.
- **Numbers > adjectives.** "$300/mo data budget" resists drift; "we'll keep it focused" doesn't.
- **Mock data is a schema spec.** Every field in the prototype maps to a real entity at MVP. The cutover is mechanical when this is true.
- **Antagonist gates are cheap.** The PRFAQ-gate and MVP-scope-gate antagonist passes are the highest-ROI 30 minutes in the whole sequence.
- **Surface assumptions, give an override.** When the playbook (or your product) guesses at a default, flag the guess and let the user override in two clicks.

## Full reference

The long-form playbook lives at `skills/playbook/references/full-playbook.md`. Read it once per product if this is your first run.

## License

MIT — see [LICENSE](./LICENSE).

## Provenance

Distilled from the realleads.ai build (May 2026) — a solo-builder + AI-team product. Patterns that repeated 3+ times became playbook conventions. Patterns that only worked once didn't.
