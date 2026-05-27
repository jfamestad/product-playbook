---
name: playbook
description: Use when taking a product from "I think there's something here" to a buildable backlog. 13 commands (Phase 0–13) covering durable principles + toolchain + tech stack, PRFAQ with named first-three customers, GTM plan, design direction (via Claude Design), MVP scope with numbers, roles and tenancy and permissions, multi-POV stress test, explicit go/no-go commitment, product requirements, access-pattern-first data model, 13-question pre-build Q&A, and a buildable backlog with optional push to your team's issue tracker. Calls the `tear-it-apart` antagonist at the PRFAQ gate and the MVP-scope gate. Triggers on "new product idea", "PRFAQ", "MVP scope", "buildable backlog", "go no go", "first paying customer", "product playbook", "playbook".
---

# Product Playbook

Take a product idea from "I think there's something here" to a buildable backlog. 13 numbered slash commands (Phase 0 through Phase 13) plus a read-only `/status` utility. Each phase produces a concrete **artifact** that unlocks the next phase. Skip nothing — weak upstream artifacts cause drift downstream.

Full long-form reference: `references/full-playbook.md`. Read it once per product if this is your first run.

## When to Use

- A new product idea is on the table and the user wants a disciplined path forward
- The user says "PRFAQ", "MVP scope", "let's bootstrap the backlog", "first paying customer", "playbook"
- You're about to write code for a green-field idea and want to make sure the upstream artifacts exist
- Use **instead of** ad-hoc product planning when a repeatable sequence is wanted

## Core Discipline

Every phase produces artifacts. The artifact is the deliverable, not the conversation. If an artifact is missing or weak, downstream phases will reveal it as drift — stop and fix the upstream artifact rather than papering over.

Two antagonist gates exist before engineering handoff: after `/2-vision` (PRFAQ) and after `/5-mvp-scope`. Both invoke the `tear-it-apart` skill in this plugin via `/7-stress-test`.

## Conventions

**Artifact location.** Artifacts always live as local markdown files under `./artifacts/` (relative to the user's current working directory). Period. The local markdown is canonical — it's what *you* (the project AI) read on every turn. Don't scatter artifacts across the repo, the home directory, or wherever feels intuitive — `./artifacts/` is where you (or a fresh agent) goes to find them.

**Toolchain integration.** When a command writes an artifact that has a natural home in a vendor tool (backlog → GitHub Issues / Jira / Linear / Asana; design docs → Confluence / Notion; design files → Figma; product plans → Aha; etc.), the command must:

1. Check `./artifacts/toolchain.md` (populated during `/1-kickoff`) to see whether the team has named a tool for this artifact type.
2. If a tool is named AND that vendor's MCP server is available in the session, offer to load the local artifact into the tool (e.g. "Want me to push these backlog items to your GitHub repo as issues?").
3. If the user accepts, perform the push as an **additive sync** — the local markdown remains the source of truth. Future edits happen locally first, then re-push.
4. If no toolchain file exists, no tool is named for this artifact type, or the MCP isn't available, just write the local markdown and move on.

This convention applies to every artifact-writing command. Don't build separate `-github`, `-jira`, `-notion` command variants — one command per phase, branching at the integration step.

**Cross-cutting log.** Every command appends a single line to `./artifacts/product-log.md` on successful completion: `YYYY-MM-DD HH:MM <TZ> — /product-N-name — <artifact path or external destination>`. Timestamps are local PST unless the user has specified otherwise. Every command bootstraps the log file with a header if it doesn't already exist — don't assume `/0-intro` ran first.

**Existing artifacts.** Before overwriting, infer from the user's request whether they want to continue iterating an existing file or start fresh. Ask only if intent is genuinely unclear. Never silently overwrite.

## The 13 Phases

### Phase 0 — `/0-intro` — Orient
Walks the user through what the playbook is, the 12 phases, the 5 principles, and how the commands work. **Writes no artifact.** Only orients.

### Phase 1 — `/1-kickoff` — Principles + toolchain + tech stack
Capture the non-negotiables (constraints, convictions) that will be true for the product's whole life — plus the team's toolchain (issue tracker, design tool, docs/wiki, comms, CRM, source control) and tech stack (language, frontend, backend, database, infra, AI providers, CI/CD). Two artifacts: `./artifacts/principles.md` and `./artifacts/toolchain.md`. Principles you can't articulate up front are the ones you'll accidentally negotiate away later. The toolchain file is what later commands consult before offering vendor pushes.

### Phase 2 — `/2-vision` — PRFAQ + one-pager
Write the press release as if launching today, then a FAQ that pre-empts hard questions, then the one-pager. Artifacts: `./artifacts/PRFAQ.md` and `./artifacts/one-pager.md`. **Outcome:** you can describe the product to a stranger in 60 seconds **and** you've named your first three target customers by name, role, and the specific reason each would say yes. If you can't name three real humans, the PRFAQ isn't grounded enough yet.

**Required FAQ questions:** Who are the first three customers (named)? Why now? What's the second-best alternative and why does this win? What does "it worked" look like in 90 days? What does "it didn't work" look like, and what would you do then?

**Gate — run `/7-stress-test ./artifacts/PRFAQ.md`.** POV lineup: Product, Customer, Sponsor, Market, Devil's Advocate. Resolve or explicitly accept every critical flaw before moving on. Vision-stage flaws compound through every later phase — this is the cheapest place to find them.

### Phase 3 — `/3-gtm` — Named first-three + GTM plan
Turn the first-three customers from PRFAQ into a concrete go-to-market plan: ICP, pricing v0.1, outreach paths, success criteria, and kill triggers. Artifact: `./artifacts/gtm-plan.md`.

### Phase 4 — `/4-design` — Design direction via Claude Design
Seed Claude Design (Anthropic product at claude.ai/design) with the project artifacts as verbatim-quoted brief, iterate freely, then integrate the handoff bundle back into `./artifacts/design/`. Two artifacts depending on which step the user reached: `./artifacts/design-seed/` (the seed package) and `./artifacts/design/index.md` (the integrated bundle catalog).

### Phase 5 — `/5-mvp-scope` — Defensible MVP scope with numbers
Decide what is *in* v0.1 and what is out. Force a number on every dimension (budget, geo, sources, architecture stages). Artifact: `./artifacts/mvp-scope.md`. **Outcome:** scope that can be defended with numbers, not adjectives. "$300/mo data budget" resists drift; "we'll keep it focused" doesn't.

**Gate — run `/7-stress-test ./artifacts/mvp-scope.md`.** POV lineup: Sponsor, Technical, Operator, Customer. Output: scope is signed off, or specific cuts/additions are identified before moving on.

### Phase 6 — `/6-roles` — Roles, tenancy, permissions, operational features
Four-area conversation framework: solution roles, tenancy model (single vs multi-tenant), permissions matrix, and operational features (admin views, audit logs, support tooling). Each area asks "in MVP or deferred?" Artifact: `./artifacts/roles.md`. Feeds `/10-access-patterns` (partitioning, permission queries) and `/11-data-model` (Tenant, User, Role, Audit-log entities).

### Phase 7 — `/7-stress-test` — Multi-POV antagonist (gate)
Run `tear-it-apart` against any artifact. Two natural gates (PRFAQ, MVP-scope) plus ad-hoc on demand. Artifact: `./artifacts/stress-test-<artifact-name>.md` per run. Recommendation is Proceed / Proceed-with-changes / Pause / Kill — but it's a *vote*, not a *decision*. The decision happens in Phase 8.

### Phase 8 — `/8-go-no-go` — Explicit commitment (gate)
The conscious, dated, named-decider commitment to build (or pivot, or kill) — after the MVP-scope stress test and before engineering handoff. Four outcomes: **Go**, **Go with changes** (specific cuts/additions), **Pause** (named blocker + signal to resume + owner), **Kill** (what we learned, what was right, what's next). Locks pre-build commitments: time budget, money budget, scope-creep rule, in-build re-evaluation triggers, in-build kill triggers. Artifact: `./artifacts/go-no-go.md`. Required upstream by `/9-engineering-handoff` — engineering doesn't pick up requirements that haven't been formally committed to.

### Phase 9 — `/9-engineering-handoff` — Product requirements doc
Package everything resolved upstream (vision, GTM, design, scope, roles) into a single `./artifacts/product-requirements.md` that engineering can build from without re-deriving context. Feature tiers, critical path, cut order, business dependencies, glossary, open questions.

### Phase 10 — `/10-access-patterns` — How the data will be queried
Don't design tables. List the questions you need to ask the data, then design the table that answers them. Artifact: `./artifacts/access-patterns.md` listing every access pattern as a numbered row before any table layout. New access pattern later → add a row, check coverage. If no key serves it, that's real work — don't paper over.

### Phase 11 — `/11-data-model` — Single-table design defended by access patterns
Translate the access-pattern list into a concrete single-table design (or equivalent). Artifact: `./artifacts/data-model.md`. Each design choice must trace back to an access pattern from Phase 9.

### Phase 12 — `/12-prebuild-qa` — The 13 pre-build questions
13 questions a senior engineer / operator would ask before approving build start. Most are not pure-tech (those belong in Phases 9–10); these span product, ops, support, GTM, and engineering — sign-on, tenancy confirmation, pricing model, failure modes, testing strategy, naming conventions, email & comms, HITL (experience + risk lenses), pilot/beta deal scope, build sequence, repo organization, observability & on-call, customer-facing docs. Artifact: `./artifacts/prebuild-qa.md`. Every question gets an answer or an explicit defer-with-workaround.

### Phase 13 — `/13-backlog` — Buildable backlog (local + optional tracker push)
Decompose `product-requirements.md` into discrete work items. One markdown file per item under `./artifacts/backlog/` plus an `index.md`. Each item: bucket, size (S/M/L/XL), priority (P0/P1/P2), depends-on, acceptance criteria. Then — per the Toolchain integration convention — if `toolchain.md` names an issue tracker (GitHub / Jira / Linear / Asana) and its MCP is available, offer to push the items as additive sync. Local markdown remains canonical.

### `/status` — Read-only status check
Reads `./artifacts/product-log.md` and inventories `./artifacts/`. Reports completed phases, open stress-test gates, and the recommended next command. Writes nothing.

## The 6 Principles

Surfaced in `/1-kickoff`. User adopts, edits, drops, or adds.

1. **Artifacts > conversation.** Every phase produces a named file. If the file doesn't exist, the phase isn't done.
2. **Numbers > adjectives.** "$300/mo data budget, 3 metros, ship by July 15" resists drift. "Lean and focused" doesn't.
3. **Mock data is a schema spec.** Every field in a prototype maps 1:1 to a real entity at MVP. The cutover is mechanical when this holds.
4. **Antagonist gates are cheap.** A 30-minute multi-POV review at PRFAQ and MVP-scope is the highest-ROI time in the whole sequence.
5. **Surface assumptions, give an override.** When the playbook (or your product) picks a default, flag it as a guess and let the user override in two clicks.
6. **Working MVP > pre-build research.** With an AI team, the cost of a thin slice has collapsed. Default to shipping a slice to the named first-three and watching what they do. Reserve pre-build discovery for genuinely large builds, binary kill-the-project assumptions, or cases where the named first-three aren't real yet.

Two optional principles from the Working Backwards / PRFAQ tradition are also surfaced as defaults in `/1-kickoff`: **Customer obsession** (start from the pain, not the solution) and **Truth-seeking over selling** (reviews exist to find what's wrong, not to get approval).

## Final Artifact Inventory

By end of Phase 12, `./artifacts/` looks roughly like:

```
./artifacts/
├── product-log.md              # append-only timeline
├── principles.md               # Phase 1
├── toolchain.md                # Phase 1
├── PRFAQ.md                    # Phase 2
├── one-pager.md                # Phase 2
├── gtm-plan.md                 # Phase 3
├── design-seed/                # Phase 4 (seed step)
├── design/index.md             # Phase 4 (integrated)
├── mvp-scope.md                # Phase 5
├── roles.md                    # Phase 6
├── stress-test-PRFAQ.md        # Phase 7 (gate 1)
├── stress-test-mvp-scope.md    # Phase 7 (gate 2)
├── product-requirements.md     # Phase 8
├── access-patterns.md          # Phase 9
├── data-model.md               # Phase 10
├── prebuild-qa.md              # Phase 11
└── backlog/                    # Phase 12
    ├── index.md
    └── NNN-<slug>.md           # one per work item
```

If a tracker push happened in Phase 12, item files also carry a `tracker_url:` reference.

## Companion Skill

**`tear-it-apart`** — multi-POV antagonist invoked by `/7-stress-test` at the Phase 2 (PRFAQ) and Phase 5 (MVP scope) gates. Also usable standalone any time a doc, design, or plan needs stress-testing.

## What This Playbook Doesn't Cover (yet)

The current command set ends at a buildable backlog. The following are intended future phases not yet shipped as commands:

- **Clickable prototype** — a mock-data-spine prototype the user can click through in 10–20 minutes. (Some of this is now absorbed into `/4-design` via Claude Design.)
- **Path to first paying customer** — named first-three operational tracking, pilot agreement signable, pilot → paid go/no-go bar.
- **Path to scale** — pricing tiers beyond pilot, sales motion, growth thesis, org plan, kill criteria.

Until those ship, treat them as out of scope — the playbook stops at "you can start building."

## Common Mistakes

- **Designing tables before listing access patterns.** Always patterns first (`/10-access-patterns` before `/11-data-model`).
- **Skipping the PRFAQ because "we know what we're building".** You don't, until you write it.
- **Naming first three customers as personas, not people.** They must be real names with real intro paths.
- **Skipping the Phase 2 and Phase 5 antagonist gates.** Cheapest place in the whole sequence to find a fatal flaw; most expensive place to skip the check.
- **Hiding default assumptions** instead of surfacing them with an override.
- **Burying open decisions in chat** instead of inside the artifact. They get re-litigated and lost.
- **Pushing to a vendor tool and then editing there.** The local markdown is canonical. Edit locally, re-push.
