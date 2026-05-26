# product-playbook

A Claude Code plugin for taking a product idea from "I think there's something here" to first paying customer and beyond. Sized for one or two builders plus an AI team. Not a heavyweight enterprise PDLC.

14 slash commands across 13 phases (plus `/product-status`). Every phase produces a concrete artifact. If you don't have the artifact, the phase isn't done.

---

## What you get

**Commands** — a numbered sequence of phase commands plus a read-only status check. Run them in order, or jump back any time. Each writes its output to `./artifacts/` so future commands (and future you) can pick up cold.

**Skills** — `playbook` (the methodology reference) and `tear-it-apart` (a multi-POV antagonist invoked at two gates and runnable standalone).

**Convention** — local markdown is canonical. If your team uses GitHub Issues, Jira, Linear, Notion, Confluence, Figma, etc., commands offer to push artifacts into those tools via vendor MCPs as an additive sync.

---

## Installation

Clone or symlink into your Claude Code plugins location, then enable via `/plugin` in Claude Code:

```bash
git clone git@github.com:jfamestad/product-playbook.git ~/path/to/plugins/product-playbook
```

Once enabled, all `/product-*` commands appear in Claude Code. Skills auto-trigger on relevant phrasing ("PRFAQ", "MVP scope", "tear this apart", etc.).

---

## The 13 phases

| # | Command | What you walk out with |
|---|---|---|
| 0 | `/product-0-intro` | Orientation — no artifact |
| 1 | `/product-1-kickoff` | `principles.md` + `toolchain.md` |
| 2 | `/product-2-vision` | `PRFAQ.md` + `one-pager.md` |
| 3 | `/product-3-gtm` | `gtm-plan.md` with named first-three customers |
| 4 | `/product-4-design` | `design-seed/` then `design/index.md` (via Claude Design) |
| 5 | `/product-5-mvp-scope` | `mvp-scope.md` with defensible numbers |
| 6 | `/product-6-roles` | `roles.md` — tenancy, permissions, operational features |
| 7 | `/product-7-stress-test` | `stress-test-*.md` — multi-POV antagonist review (vote, not decision) |
| 8 | `/product-8-go-no-go` | `go-no-go.md` — explicit Go / Go-with-changes / Pause / Kill commitment with named decider |
| 9 | `/product-9-engineering-handoff` | `product-requirements.md` |
| 10 | `/product-10-access-patterns` | `access-patterns.md` |
| 11 | `/product-11-data-model` | `data-model.md` (single-table design) |
| 12 | `/product-12-prebuild-qa` | `prebuild-qa.md` — 13 questions answered |
| 13 | `/product-13-backlog` | `backlog/` — one item per file + index, optional push to issue tracker |
| — | `/product-status` | Read the log, see where you are. No artifact written |

`/product-7-stress-test` is gated into the flow at two natural points (after `/product-2-vision` and after `/product-5-mvp-scope`) but can run ad-hoc on any artifact at any time.

---

## How to drive the plugin

### First run, fresh product

```text
/product-0-intro       # orient yourself — what the playbook is, what's coming
/product-1-kickoff     # principles + toolchain + tech stack
/product-2-vision      # draft PRFAQ + one-pager
/product-7-stress-test ./artifacts/PRFAQ.md   # gate 1
/product-3-gtm         # named first-three + pricing v0.1 + outreach plan
/product-4-design      # seed Claude Design, iterate, integrate handoff
/product-5-mvp-scope   # IN/OUT with numbers
/product-6-roles       # tenancy + permissions + operational features
/product-7-stress-test ./artifacts/mvp-scope.md   # gate 2
/product-8-go-no-go              # explicit commitment — Go / Go-with-changes / Pause / Kill
/product-9-engineering-handoff   # product-requirements.md
/product-10-access-patterns       # questions you need to ask the data
/product-11-data-model           # single-table design defended by patterns
/product-12-prebuild-qa          # 13 pre-build questions
/product-13-backlog              # buildable backlog → optional push to tracker
```

### Resuming a product already in flight

```text
/product-status        # read the log + inventory artifacts + see recommended next
```

`/product-status` reads `./artifacts/product-log.md`, lists which phases are complete, flags unresolved stress-test gates, and recommends one concrete next command.

### Re-running a phase

Any command can be re-run. Each detects whether the artifact already exists and asks (or infers) whether you want to iterate or start fresh. Never silently overwrites.

### Running the antagonist on demand

```text
/product-7-stress-test ./artifacts/<any-artifact>.md
```

Rotates POVs (Product, Customer, Sponsor, Technical, Operator, Market, Devil's Advocate), surfaces critical flaws + load-bearing assumptions, and ends with a Proceed / Proceed-with-changes / Pause / Kill recommendation.

---

## Artifacts and the log

Every artifact lands under `./artifacts/` (relative to your current working directory). The directory ends up looking like:

```
./artifacts/
├── product-log.md             # append-only timeline of phase completions
├── principles.md
├── toolchain.md
├── PRFAQ.md
├── one-pager.md
├── gtm-plan.md
├── design-seed/               # what you uploaded to Claude Design
├── design/                    # the integrated handoff bundle
│   └── index.md
├── mvp-scope.md
├── roles.md
├── stress-test-PRFAQ.md       # one per stress-test run
├── stress-test-mvp-scope.md
├── go-no-go.md                # explicit commitment to build (or pause/kill)
├── product-requirements.md
├── access-patterns.md
├── data-model.md
├── prebuild-qa.md
└── backlog/
    ├── index.md
    └── 001-init-repo-ci.md    # one file per work item
```

`./artifacts/product-log.md` is append-only. Each phase command writes one line: `YYYY-MM-DD HH:MM PST — /product-N-name — <artifact path>`. That's how `/product-status` reconstructs progress without any other state.

---

## Toolchain integration

Local markdown is always the source of truth. But if your team lives in vendor tools, commands offer to push artifacts into them.

`/product-1-kickoff` captures `./artifacts/toolchain.md` covering both **team toolchain** (issue tracker, design tool, docs/wiki, comms, email, CRM, source control) and **tech stack** (language, frontend, backend, database, infra, cloud, AI providers, CI/CD).

Subsequent commands consult `toolchain.md` and offer the relevant push:

| Artifact | Natural destination |
|---|---|
| PRFAQ, MVP scope, roles, requirements, prebuild-QA | Confluence / Notion / Google Docs |
| GTM plan first-three | Salesforce / HubSpot |
| Design index | Figma / Confluence / Notion |
| Engineering requirements | Confluence + Jira/Linear epic |
| Backlog items | GitHub Issues / Jira / Linear / Asana |

The push only fires if (a) the tool is named in `toolchain.md` and (b) the vendor's MCP is available in the current session. Otherwise the command stays local and moves on. Vendor pushes are **additive** — edit locally first, then re-push.

---

## The 5 principles

These govern every phase. `/product-1-kickoff` walks them and lets you adopt, edit, drop, or add.

1. **Artifacts > conversation.** Every phase produces a named file. If the file doesn't exist, the phase isn't done.
2. **Numbers > adjectives.** "$300/mo data budget, 3 metros, ship by July 15" resists drift. "Lean and focused" doesn't.
3. **Mock data is a schema spec.** Every field in the prototype maps 1:1 to a real entity at MVP. The cutover is mechanical when this holds.
4. **Antagonist gates are cheap.** A 30-minute multi-POV review at PRFAQ and MVP-scope is the highest-ROI time in the whole sequence.
5. **Surface assumptions, give an override.** When the playbook (or your product) picks a default, flag it as a guess and let the user override in two clicks.

Two optional principles from the Working Backwards / PRFAQ tradition (customer obsession, truth-seeking over selling) are surfaced as defaults in `/product-1-kickoff`.

---

## Skills

### `playbook`

The methodology reference. Loaded automatically when the user mentions PRFAQ, MVP scope, prototype, buildable backlog, first paying customer, or scale plan. Long-form playbook lives at `skills/playbook/references/full-playbook.md` — read it once per product if this is your first run.

### `tear-it-apart`

Multi-POV antagonist. Triggers on "tear this apart", "red team", "find the flaws", "pre-mortem". Invoked at the two gates by `/product-7-stress-test`, and runnable standalone any time a doc, design, plan, or strategy needs stress-testing.

Deep expertise in **packaging units of value** (what's the unit, does it align with customer-perceived value AND cost-to-serve, is it measurable/observable/disputable, what's the upgrade trigger, free-vs-paid line, expansion path).

---

## Who this is for

- **Solo builders and small teams** taking a new product from idea to first customers
- **Builders working with an AI team** (Claude, agents) who want a repeatable scaffolding
- **Anyone** who's felt the pain of a prototype that drifted from the PRFAQ, an MVP that ballooned, or a launch that hit avoidable flaws on day one

## What this is *not*

- A heavyweight enterprise PDLC framework — this is sized for one or two builders + AI
- Tech-stack opinionated beyond defaults surfaced in `/product-1-kickoff` and the prototype guidance
- Production-operations focused — the sequence ends at first paying customer + scale planning; ongoing operations are out of scope

---

## License

MIT — see [LICENSE](./LICENSE).

## Provenance

Distilled from the realleads.ai build (May 2026) — a solo-builder + AI-team product. Patterns that repeated 3+ times became playbook conventions. Patterns that only worked once didn't.
