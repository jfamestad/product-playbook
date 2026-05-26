---
name: playbook
description: Use when taking a product from "I think there's something here" through clickable prototype, buildable backlog, first paying customer, and scale. An 11-phase sequence (Phase 0–10) covering durable principles, PRFAQ with named first-three customers, MVP scope with numbers, access-pattern-first data model, mock-data-spine clickable prototype, iterate on clicks, verify, bootstrap a buildable backlog with issue templates, plan path to first paying customer, plan path to scale. Calls the `tear-it-apart` antagonist at the PRFAQ gate and the MVP-scope gate. Triggers on "new product idea", "PRFAQ", "MVP scope", "start a prototype", "buildable backlog", "first paying customer", "scale plan", "product playbook", "playbook".
---

# Product Playbook

Take a product idea from "I think there's something here" through clickable prototype, buildable backlog, first paying customer, and scale. 11 phases (0 through 10). Each phase produces concrete **artifacts** that unlock the next phase. Skip nothing — weak upstream artifacts cause drift downstream.

Full reference (longer-form): `references/full-playbook.md`. Read it once per product if this is your first run.

## When to Use

- A new product idea is on the table and the user wants a disciplined path forward
- The user says "PRFAQ", "MVP scope", "build me a prototype", "let's bootstrap the backlog", "plan the path to first paying customer", "what does scale look like"
- You're about to write code for a green-field idea and want to make sure the upstream artifacts exist
- Use **instead of** ad-hoc product planning when a repeatable sequence is wanted

## Core Discipline

Every phase produces artifacts. The artifact is the deliverable, not the conversation. If an artifact is missing or weak, downstream phases will reveal it as drift — stop and fix the upstream artifact rather than papering over.

Two antagonist gates exist, both before the build: after `/product-2-vision` (PRFAQ) and after `/product-5-mvp-scope`. Both invoke the `tear-it-apart` skill in this plugin.

## Conventions

**Artifact location.** Artifacts always live as local markdown files under `./artifacts/` (relative to the user's current working directory). Period. The local markdown is canonical — it's what *you* (the project AI) read on every turn. Don't scatter artifacts across the repo, the home directory, or wherever feels intuitive — `./artifacts/` is where you (or a fresh agent) goes to find them.

**Toolchain integration.** When a command writes an artifact that has a natural home in a vendor tool (backlog → GitHub Issues / Jira / Linear / Asana; design docs → Confluence / Notion; design files → Figma; product plans → Aha; etc.), the command must:

1. Check `./artifacts/toolchain.md` (populated during `/product-1-kickoff`) to see whether the team has named a tool for this artifact type.
2. If a tool is named AND that vendor's MCP server is available in the session, offer to load the local artifact into the tool (e.g. "Want me to push these backlog items to your GitHub repo as issues?").
3. If the user accepts, perform the push as an **additive sync** — the local markdown remains the source of truth. Future edits happen locally first, then re-push.
4. If no toolchain file exists, no tool is named for this artifact type, or the MCP isn't available, just write the local markdown and move on.

This convention applies to every artifact-writing command. Don't build separate `-github`, `-jira`, `-notion` command variants — one command per phase, branching at the integration step.

**Cross-cutting log.** Every command appends a single line to `./artifacts/product-log.md` on successful completion: `YYYY-MM-DD HH:MM <TZ> — /product-N-name — <artifact path or external destination>`. Timestamps are local PST unless the user has specified otherwise. Every command bootstraps the log file with a header if it doesn't already exist — don't assume `/product-0-intro` ran first.

**Existing artifacts.** Before overwriting, infer from the user's request whether they want to continue iterating an existing file or start fresh. Ask only if intent is genuinely unclear. Never silently overwrite.

## The 11 Phases

### Phase 0 — Durable principles
Capture the non-negotiables (constraints, convictions) that will be true for the product's whole life. Not features.

- **Artifact:** `memory/feedback_<principle>.md` per principle. Body: rule + **Why:** + **How to apply:**.
- **Index:** each in `memory/MEMORY.md`.

Principles you can't articulate up front are the ones you'll accidentally negotiate away later. The *why* is what lets future-you handle edge cases the rule didn't anticipate.

### Phase 1 — Frame the opportunity in prose (PRFAQ)
Write the press release as if launching today, then a FAQ that pre-empts hard questions, then the one-pager.

- **Artifacts:** `PRFAQ.md`, `one-pager.md` (same idea, one screen, alternate taglines), `memory/project_<name>_overview.md`.
- **Outcome:** you can describe the product to a stranger in 60 seconds **and** you've named your first three target customers by name, role, and the specific reason each would say yes. If you can't name three real humans, the PRFAQ isn't grounded enough yet.

**Required FAQ questions** (write these into the PRFAQ's FAQ section):
- *Who are the first three customers?* Named. With the actual sentence you'd say to them.
- *Why now?* What changed in the world that makes this possible / necessary today?
- *What's the second-best alternative for these customers, and why does this win?*
- *What does "it worked" look like in 90 days?*
- *What does "it didn't work" look like, and what would you do then?*

**Gate — invoke `tear-it-apart` on the PRFAQ + one-pager.** POV lineup: Product, Customer, Sponsor, Market, Devil's Advocate. Resolve or explicitly accept every critical flaw before Phase 2. Vision-stage flaws compound through every later phase — this is the cheapest place to find them.

### Phase 2 — Bound the MVP with numbers
Decide what is *in* v0.1 and what is out. Force a number on every dimension (budget, geo, sources, architecture stages).

- **Artifact:** `memory/project_mvp_scope.md` with concrete numbers.
- **Outcome:** scope that can be defended with numbers, not adjectives. "$300/mo data budget" resists drift; "we'll keep it focused" doesn't.

**Gate — invoke `tear-it-apart` on the MVP scope.** POV lineup: Sponsor, Technical, Operator, Customer. Output: scope is signed off, or specific cuts/additions are identified before Phase 3.

### Phase 3 — Access-pattern-first data model
Don't design tables. List the questions you need to ask the data, then design the table that answers them.

- **Artifacts:** `single-table-design.md` (or equivalent) listing every access pattern as a numbered row *before* any table layout. `memory/project_single_table.md` captures the chosen design. `memory/project_<scheme>_slug.md` for any naming schemes.
- New access pattern later → add a row, check coverage. If no key serves it, that's real work — don't paper over.

### Phase 4 — Capture meta-feedback as you go
Throughout phases 0–3 (and forever after), when the user says something that should change your default behavior, save it as a feedback memory.

- **Artifact:** `memory/feedback_<topic>.md` per piece of durable guidance. Body: rule + **Why:** + **How to apply:**.
- Save both corrections **and** validated judgment calls. Saving only corrections makes you over-cautious.

### Phase 5 — Build the clickable prototype
A local URL the user can click through end-to-end in 10–20 minutes and react to with specifics. Mock data only — explicitly, "the last time we use mock data."

**Tech defaults that have worked:**
- Next.js 14 App Router with `output: "export"` (static, no server)
- Tailwind CSS, tight token set (3–4 colors, one serif, one sans)
- `localStorage` for state. No backend, no auth.
- TypeScript strict + `@/*` path alias (needs `baseUrl` in tsconfig)

**Build order:**
1. **5a — Mock data spine:** `lib/mock-data.ts`. ~5 richly textured records. Use `{{placeholder}}` convention in mock copy so the render layer substitutes without rewriting bodies.
2. **5b — Domain primitives:** `lib/<concept>.ts` per first-class concept. Each owns its types, defaults, persistence, render helpers.
3. **5c — State hook with deep-merge:** `useProfile()` loads from localStorage and deep-merges against `DEFAULT_PROFILE`. This is what lets you add fields later without breaking saved state.
4. **5d — Shared chrome:** `components/AppShell.tsx`.
5. **5e — Pages in user-encounter order:** landing → onboarding wizard → dashboard → detail view → settings.
6. **5f — Dynamic routes for static export:** server wrapper at `app/<thing>/[id]/page.tsx` exporting `generateStaticParams()`, client component at `app/<thing>/[id]/<Thing>Detail.tsx`. **Required** by `output: "export"`.

### Phase 6 — Iterate on the prototype with the user
After phase 5, every move is *responding to clicks*. Pattern:
1. User reacts to something they saw.
2. Identify: **copy**, **shape of data**, or **structure of experience**?
3. Make the smallest change that fully answers, then continue.

**Re-usable patterns:**
- **Context-aware copy:** when "this should vary by X," centralize in `lib/<x>-norms.ts`. Don't fork pages per X.
- **Convention-driven mocks:** new variability layer → new `{{placeholder}}`, render layer fills.
- **Aggregate default + per-instance override:** `profile.<thing>` for default, keyed map for overrides. UI shows "Use my default (<label>)" + override picker.
- **Surface the assumption, offer the override:** flag every guess inline with two-click change. No hidden defaults, no settings detour.

### Phase 7 — Verify and close the loop
Before declaring a phase done:
- Click through the full path in a browser. Type-check passing ≠ feature working.
- For each piece of feedback the user gave during the phase, confirm it's reflected.
- For each new principle/pattern discovered, write a `feedback_<topic>.md` memory.

### Phase 8 — Bootstrap the buildable backlog
The prototype proved *what* to build. Phase 8 turns that into a backlog a team (or you + AI agents) can execute. Auth, real backend, observability, and a real env are no longer deferred — they're the work.

**Artifacts:**
- Private git repo with sensible default branch + `.gitignore`.
- A minimal label schema (e.g. `mvp`, `ingestion`, `platform`, `design`, `legal:tos-review`).
- A milestone for the MVP delivery target.
- One issue per workstream, written from a consistent template.
- A `legal/` directory with pilot agreement + ToS-determination tracking.

**8a — Pre-build Q&A** (resolve before opening the first issue): auth provider, org model, billing at MVP, definition of "bad" outcome, env count, domain ownership, email infra, concierge-vs-pipeline, source build order, design timing, pilot scope, repo layout.

**8b — Workstream bucketing** (for a data-pipeline-shaped product, four buckets cover it): source ingestion, platform/infra, critical path, legal/operational.

**8c — Issue template** (the shape worth saving):
```
## Summary
## Source / Inputs / Outputs
## Architecture (or Provider options table)
## Open decisions
## Acceptance criteria
- [ ] mechanical checks
## Labels
## References
**Effort:** S | M | L | XL
```

**8d — Decisions-resolution loop:** author proposes a default in each ticket's "Open decisions"; reviewer batches answers; author updates tickets + writes resolution into the affected entity.

**8e — Bootstrap order:** init repo → create private GitHub org/repo (web UI; org-creation API is Enterprise-only) → move prototype to `/prototype` → labels + milestone → source issues → platform issues → critical-path issues → legal-track issues → pre-build Q&A pass → re-read with decisions resolved.

### Phase 9 — Plan the path to first paying customer
Between buildable backlog and recurring revenue is a sequence of customer conversations that should not happen ad-hoc. The first three names from Phase 1 become the central organizing principle.

**Artifacts:**
- `memory/project_<name>_first_three.md` — the named first three, with contact info, intro path, the exact sentence you'll say, expected objections, prepared answers. Updated weekly.
- `legal/pilot-agreement-draft.md` — drafted in Phase 8; in Phase 9, goes to counsel and comes back signable.
- `customers/<name>/onboarding-checklist.md` — per-pilot pre-flight.
- `pilot-status-log.md` — one row per pilot, weekly, max 5 minutes to write.
- A **pilot → paid go/no-go bar** defined *before* the first pilot starts.

**Outcome:** every customer conversation ends in "yes", "no with a reason worth a memory", or "not yet with a date". Drift between customer #1 and customer #3 surfaces as patterns.

### Phase 10 — Plan the path to scale
Once paying customers exist, "scale" stops meaning "build more features" and starts meaning "decide which motion turns this into the business the PRFAQ promised". Write provisionally; revise as evidence arrives.

**Artifacts:**
- `pricing-tiers.md` — the ladder beyond pilot pricing, with unit-economic math per tier.
- `sales-motion.md` — motions you'll pursue + explicit "we will not do X" list.
- `growth-thesis.md` — channel mix, leading indicators, disconfirming evidence.
- `org-plan.md` — hires gated on milestones, including the honest "stay solo + AI" alternative.
- `expansion-options.md` — second-product / adjacent-vertical candidates with kill criteria.
- `memory/feedback_kill_criteria.md` — when to stop. The most under-written part of any plan.

**Outcome:** when pilots produce evidence of PMF (or against it), you don't lose six weeks re-thinking from a blank page.

## Final Inventory

By end of Phase 8, project tree looks roughly like:

```
~/product/<name>/
├── PRFAQ.md
├── one-pager.md
├── MVP.md
├── single-table-design.md
├── .gitignore
├── legal/
│   ├── pilot-agreement-draft.md
│   └── source-tos-determinations.md
├── prototype/                          (from Phase 5; preserved for reference)
├── web/                                (the real app)
└── services/                           (backend services)
```

And on GitHub:
```
<org>/<repo>
├── labels:    mvp, <buckets>, legal:tos-review
├── milestone: "MVP <release>"
└── issues:    one per workstream from the four buckets
```

## Companion Skill

**`tear-it-apart`** — multi-POV antagonist invoked at the Phase 1 (PRFAQ) and Phase 2 (MVP scope) gates. Also usable standalone any time a doc, design, or plan needs stress-testing.

## Common Mistakes

- **Designing tables before listing access patterns.** Always patterns first.
- **Skipping the PRFAQ because "we know what we're building".** You don't, until you write it.
- **Naming first three customers as personas, not people.** They must be real names with real intro paths.
- **Forking pages per variant** instead of centralizing in `lib/<x>-norms.ts`.
- **Hiding default assumptions** instead of surfacing them with an override.
- **Declaring done from type-check.** Always click through.
- **Adding fields without deep-merge.** Saved localStorage state will break.
- **Skipping the Phase 1 and Phase 2 antagonist gates.** Cheapest place in the whole sequence to find a fatal flaw; most expensive place to skip the check.
- **Open decisions buried in chat** instead of inside the issue. They get re-litigated and lost.
- **Writing Phase 10 only when you need it.** By then you're too busy reacting to write it well.

## What This Playbook Doesn't Cover

- Production-scale operations after Phase 10 (treat that as a separate playbook, sized to your org).
- Detailed tech-stack opinions beyond the Phase 5 prototype defaults. Stack choices for Phase 8+ are out of scope; the playbook focuses on the *artifacts* that unlock the next phase, not the implementation details.
