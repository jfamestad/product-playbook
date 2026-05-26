# Product development playbook

A repeatable sequence for taking a new idea from "I think there's something here" to a clickable prototype that a real customer can react to. Distilled from the realleads.ai build (May 2026).

Each phase lists the **artifacts** produced and the **outcome** that artifact unlocks. Skip nothing — every artifact later in the chain assumes the earlier ones exist. If something earlier is weak, the work downstream will reveal it as drift.

---

## Phase 0 — Capture the durable principles

Before any code, write down the things that will be true about the product for its whole life. These are not features. They are constraints and convictions.

**Artifacts:**
- `memory/feedback_<principle>.md` — one per non-negotiable principle (e.g. "lead exclusivity is a product principle"). Frontmatter: `type: feedback`. Body: the rule, **Why:** (origin / motivation), **How to apply:** (when it kicks in).
- `memory/MEMORY.md` index entry per principle.

**Outcome:** A short list of statements that survive scope changes. When a later decision conflicts with one of these, you stop and re-examine — you don't silently violate it.

**Examples we've validated:**
- *"Lead exclusivity is a product principle"* — within a vertical, a lead has one assignee. Bends the data model, the pricing model, the customer pitch.
- *"Bad lead = AI failure, not non-conversion."* Creditable only when the AI hallucinated, mismatched criteria, or produced a broken brief. A real lead that didn't close isn't creditable. Bends the feedback UX, the credit ledger, the model-improvement loop.
- *"Forward-compatible data shapes ship before the system that needs them."* Prices in cents, credit policy as a first-class field on pricing, even before Stripe lands. Avoids painful schema migrations.
- *"Mock data is a schema spec."* Every field in the prototype's mock records maps to a real entity at MVP. The cutover is mechanical when this is true.

**Learning:** Principles you can't articulate up front are the ones you'll accidentally negotiate away later. Write them down, with the *why*. The why is what lets future-you handle edge cases the rule didn't anticipate.

---

## Phase 1 — Frame the opportunity in prose

Write the press release / FAQ and the one-pager before designing anything.

**Artifacts:**
- `PRFAQ.md` — press release written as if launching today + a FAQ that pre-empts the hard questions.
- `one-pager.md` — the same idea compressed to one screen, with alternate taglines.
- `memory/project_<name>_overview.md` — one-paragraph project foundation, indexed in MEMORY.md.

**Outcome:** You can describe the product to a stranger in 60 seconds. Words you find yourself reaching for repeatedly become the canonical phrasing (and later, UI copy). And — **you've named your first three target customers by name, role, and the specific reason each would say yes.** If you can't name three real humans, the PRFAQ isn't grounded enough yet; iterate before moving on.

**Required FAQ questions.** The PRFAQ's FAQ section must answer at least these — they're the questions that expose vague thinking when left unwritten:
- *Who are the first three customers?* Named. With the actual sentence you'd say to them.
- *Why now?* What changed in the world that makes this possible / necessary today?
- *What's the second-best alternative for these customers, and why does this win?*
- *What does "it worked" look like in 90 days?*
- *What does "it didn't work" look like, and what would you do then?*

**Learning:** Tag alternate phrasings explicitly (e.g. "alternate 'Connect the dots' with 'Focus on the human touch'"). You'll iterate on copy, and tracking the alternates keeps you from re-litigating the same paragraph every session.

**Gate — `tear-it-apart` on the PRFAQ + one-pager.**
Before you call the vision artifacts "done", run the antagonist over them. This is the cheapest place in the whole sequence to find a fatal flaw, and the most expensive place to skip the check. POV lineup:
- **Product:** is the value proposition crisp and singular? Is the launch experience credible? What's the "I get it" moment?
- **Customer:** would the named persona actually pay this price for this benefit? What's the first question they'd ask that the FAQ doesn't answer?
- **Sponsor:** is the financial story coherent? Does the unit-economics paragraph survive a one-page memo to a board?
- **Market:** what does the second-best alternative do for this customer today, and why does this win?
- **Devil's Advocate:** what is the single most credible reason this should not exist?

Output: a sharpened PRFAQ + one-pager. If any POV produces a critical flaw the team can't answer in a paragraph, that's a finding worth a memory before Phase 2 begins. *Do not* paper over it — vision-stage flaws compound through every later phase.

---

## Phase 2 — Bound the MVP

Decide what is *in* v0.1 and, more importantly, what is out. Force a number on every dimension.

**Artifacts:**
- `memory/project_mvp_scope.md` — geography, trigger sources, budget ceilings, target architecture stages. Frontmatter: `type: project`.
- A 4-stage (or however-many-stage) architecture sketch named in the memory.

**Outcome:** A scope that can be defended with numbers ("$300/mo data budget", "two trigger sources", "Sac/Placer/EDC + CA"). Stops the project from quietly expanding.

**Learning:** Budgets and counts are the only scope language that resists drift. "We'll keep it focused" doesn't survive a single conversation; "$300/mo" does.

**Gate — `tear-it-apart` on the MVP scope.** POV lineup:
- **Sponsor:** does the scope produce evidence the bet is worth scaling? What is the kill criterion?
- **Technical:** are the budget ceilings achievable on the chosen stack at the stated volume?
- **Operator:** who runs this in production, and at what hours? Where does the pager go?
- **Customer:** is the in-scope value enough that a pilot would actually use it daily?

Output: scope is signed off, or specific cuts/additions are identified before Phase 3 starts.

---

## Phase 3 — Design the data model around access patterns

Don't design tables. Design the questions you need to ask the data, then design the table that answers them.

**Artifacts:**
- `single-table-design.md` (or equivalent) listing every access pattern as a numbered row before any table layout.
- `memory/project_single_table.md` — the chosen design: keys, GSIs, entity prefixes.
- `memory/project_<scheme>_slug.md` — any indexing schemes that need a name (e.g. trigram slug, prefix encoding).

**Outcome:** A data design defended by the access patterns it serves, not by aesthetic preference. Adding a new pattern later is mechanical: add a row, see if existing keys serve it.

**Learning:** When you "confirm coverage" of a new access pattern, do it by **adding a row to the access patterns table** and matching it to existing keys. If no match, you've found real work — don't paper over it.

---

## Phase 4 — Capture meta-feedback as you go

Throughout phases 0–3 (and forever after), when the user says something that should change your default behavior, save it.

**Artifacts:**
- `memory/feedback_<topic>.md` for each piece of durable guidance. Body: the rule, **Why:**, **How to apply:**.

**Outcome:** The next conversation starts with the previous one's lessons already loaded. The user does not repeat themselves.

**Learning:** Save both corrections *and* validated judgment calls. Saving only corrections makes you over-cautious. The format is identical: rule + why + how to apply.

Examples we landed on:
- "Lead exclusivity is a product principle" (durable constraint)
- "Surface assumptions, give an override path" (collaboration default)

---

## Phase 5 — Build the clickable prototype

The prototype's job is to make the vision visual so the next conversation can be about *the experience*, not *the explanation*. Use mock data — explicitly, "the last time we use mock data."

**Tech defaults that worked:**
- Next.js 14 App Router with `output: "export"` (static site, no server required).
- Tailwind CSS with a tight token set (3–4 colors, one serif, one sans).
- `localStorage` for state persistence. No backend, no auth.
- TypeScript strict mode with `@/*` path alias (requires `baseUrl` in tsconfig).

**Artifacts, in this order:**

### 5a. Mock data file (the spine)
- `lib/mock-data.ts` — a small number of richly textured records (5 was right for us). Each record carries enough detail that the eventual UI doesn't need to invent.
- **Convention placeholders** in mock copy: `{{signature}}`, `{{phone}}`, `{{close}}`, etc. The render layer substitutes. Lets you change voice/objective/close without rewriting bodies.

### 5b. Domain primitives
- `lib/<concept>.ts` for each first-class concept that has its own shape and behavior (e.g. `voice-profile.ts`, `closes.ts`, `vertical-norms.ts`).
- Each primitive owns: its types, its defaults, its persistence, its rendering helpers.

### 5c. State hook with deep-merge
- `useProfile()` (or equivalent) — loads from localStorage, deep-merges against `DEFAULT_PROFILE`. Critical: this is what lets you *add fields later* without breaking saved state.

### 5d. Shared chrome
- `components/AppShell.tsx` — header, nav, "restart onboarding" link. Built once, used everywhere.

### 5e. Pages, in the order a user encounters them
1. Landing (one-pager content, two CTAs: "start onboarding" + "skip to demo dashboard")
2. Onboarding wizard (criteria → ICP → voice → close default → review)
3. Dashboard (scoreboard + live queue)
4. Lead detail (brief + outreach pack)
5. Settings (edit everything, reset prototype)

### 5f. Dynamic routes for static export
- Server wrapper at `app/leads/[id]/page.tsx` exports `generateStaticParams()`.
- Client component at `app/leads/[id]/LeadDetail.tsx` does the actual UI work.
- This split is **required** by `output: "export"`. Failing to do it produces a 500 with a clear error message.

**Outcome:** A local URL the user can click through end-to-end, in 10–20 minutes, and react to with specifics ("this should vary by industry", "add a mail channel", "I want this overridable per lead").

**Learning:** The prototype is a *reaction surface*. The user's feedback during click-through is the most valuable input in the whole sequence. Build it cheap enough that you can rebuild parts of it without ceremony.

---

## Phase 6 — Iterate on the prototype with the user

After phase 5, all subsequent work is *responding to clicks*. The pattern that repeats:

1. User reacts to something they saw ("minimum deal size should vary by industry").
2. Identify whether the reaction is about **copy**, **shape of data**, or **structure of experience**.
3. Make the smallest change that fully answers, then keep going.

**Re-usable patterns we found:**

### Context-aware copy
When the user says "this should vary by X," centralize the variation in one file (`lib/<x>-norms.ts`) and reference it from every place the copy appears. Don't fork pages per X.

### Convention-driven mocks
When a new layer of variability is needed (e.g. close objectives), introduce a `{{placeholder}}` in mock copy and let the render layer fill it. Don't rewrite mock bodies — extend the template language.

### Aggregate default + per-instance override
For any setting the user might want to set globally *and* override locally:
- Store the default on `profile.<thing>` (persisted with the profile).
- Store overrides keyed by instance (e.g. `realleads.prototype.leadCloses.v1`).
- UI shows "Use my default (<label>)" + the override picker. Same control on the aggregate page sets only the default.

This pattern repeated 3+ times — it's the right default for "user wants control at two levels."

### Surface the assumption, offer the override
When you guess at a default — vertical, tone, objective, close type — flag the guess inline ("(your default)") and let the user change it in two clicks. Do not hide assumptions; do not require a settings detour.

---

## Phase 7 — Verify and close the loop

Before declaring a phase done:
- Click through the full path in a browser. Type-check passing ≠ feature working.
- For each piece of feedback the user gave during the phase, confirm it's reflected.
- For each new principle or pattern you discovered, write a feedback memory.

**Artifacts:**
- New `memory/feedback_<topic>.md` entries for any patterns worth carrying to the next product.

**Outcome:** Next idea starts with this idea's lessons already in scope. The playbook gets stronger over time.

---

## Phase 8 — Bootstrap the buildable backlog

The prototype proved *what* to build and *that it's worth building*. Phase 8 turns that into a backlog a team (you + AI agents + future humans) can actually execute. Auth, real backend, observability, and a real env are no longer deferred — they're the work.

**Artifacts:**
- A private git repo with a sensible default branch + `.gitignore`.
- A label schema you'll actually use (e.g. `mvp`, `ingestion`, `enrichment`, `platform`, `design`, `legal:tos-review`). Keep it minimal — labels you don't filter on are noise.
- A milestone for the MVP delivery target.
- One issue per workstream, written from a consistent template.
- A `legal/` directory with the pilot agreement draft + the ToS-determination tracking doc.

### 8a. Pre-build Q&A (do this before opening the first issue)

Before writing tickets, force answers on the questions that change architecture. The ones that mattered for realleads:

1. **Auth provider?** (Cognito / Auth0 / Clerk / custom)
2. **Org model?** Single-user default org, multi-user with invites, both?
3. **Billing at MVP?** (Stripe now / no payment yet / placeholder)
4. **What does "bad" mean for the product?** Refunds, credits, SLAs — define before building the loop.
5. **One env or many?** Dev only, dev+prd, dev+staging+prd. Affects DNS, IaC stack layout, CI/CD.
6. **Domain ownership** — who owns the apex, where is DNS managed, what's the cert story?
7. **Email infra** — direct from SES on day one, or a warmed provider (Postmark/Resend) for the pilot weeks then SES cutover?
8. **Concierge or pipeline first?** Hand-curated leads for the first N customers vs. automation from day one.
9. **Source build order** — parallel (all sources at once) or serial (one at a time)?
10. **Design at MVP** — full design pass before code, or iterate from the prototype?
11. **Pilot scope** — which vertical, how many customers, what's the success bar?
12. **Repo layout** — keep prototype in tree (as `/prototype`), or remove it?

Capture answers as you go in `memory/project_<name>_decisions.md`. Disagreement on any of these *after* tickets are open costs a multiple of disagreement *before*.

### 8b. Workstream bucketing

For an MVP with a data-pipeline shape, four buckets cover the work:

1. **Source ingestion** — one issue per data source.
2. **Platform / infra** — backend, frontend, CI/CD, design system, shared services (auth, address normalization, billing scaffolding).
3. **Critical path** — the pipeline stages that turn raw data into the customer-facing artifact (routing, briefing/enrichment, delivery, feedback loop).
4. **Legal / operational** — ToS review, pilot agreement, subprocessor list, privacy notice.

For each bucket, write the issues in *one sitting* if possible. Cross-bucket dependencies surface immediately when the issues are all in front of you.

### 8c. Issue template (the shape that emerged)

Every MVP issue follows this template. The shape repeats so much that it should be a saved template in the repo's issue config:

```
## Summary
What this delivers and why it's needed now.

## Source / Inputs / Outputs
The concrete I/O. What goes in, what comes out, where it's stored.

## Architecture (or Provider options table for multi-vendor decisions)
Library locations, Lambda/service boundaries, table writes, cost ceiling.

## Open decisions
Specific one-liners the user can resolve in a batch pass — DO NOT block on these to write the ticket.

## Acceptance criteria
- [ ] Concrete, mechanical checks. Each item is either done or not done.

## Labels
mvp, <bucket>

## References
Links to the source-of-truth docs (PRFAQ, MVP scope, data model) + cross-issue dependencies.

**Effort:** S | M | L | XL
```

**Conventions worth keeping:**
- "Open decisions" lives *in the ticket*. The author proposes a default; the reviewer either agrees with "go" or counters. Don't lose decisions in chat.
- Acceptance criteria are checkboxes, written so a reviewer (or an agent) can verify them mechanically.
- Effort is t-shirt-sized only — S/M/L/XL — not story points. Use the rough shape, not false precision.

### 8d. Decisions-resolution loop

Tickets get written before all decisions are made. To prevent stalls:
1. Author proposes a default for each open question in the ticket.
2. Reviewer batches their answers ("1. yes, 2. cents, 3. unique, ...") rather than answering inline.
3. Author updates tickets + writes the resolution into the affected entity (data model, runbook, ADR).

This is the *only* way to keep ticket-writing velocity high enough that the backlog is ready when build starts.

### 8e. Bootstrap order (what to do today)

1. `git init` + initial commit of all the docs (PRFAQ, one-pager, MVP scope, data model, business plan).
2. Create the private GitHub org + repo (web UI for orgs — API path is Enterprise-only).
3. Move the prototype to `/prototype` so `/web` is free for the real build.
4. Set up labels + milestone.
5. Write source-ingestion issues (one per source).
6. Write platform issues (backend infra, frontend MVP, CI/CD, design kickoff).
7. Write critical-path issues (the pipeline stages).
8. Write legal-track issues (ToS review, pilot agreement draft).
9. Run the pre-build Q&A pass — resolve open decisions.
10. Re-read the issues with the decisions resolved. Some will get smaller. A few may dissolve entirely.

**Outcome:** A buildable backlog. Each issue is a self-contained unit of work with enough context to hand to an agent or a contractor.

**Learning:**
- The prototype's mock-data file is the schema spec for the data model. Doing the prototype well makes the backlog easier to write.
- "Open decisions" inline in the ticket is the single most effective discipline. Without it, decisions get re-litigated in 4 different threads and lose to chat-channel entropy.
- Workstream bucketing is more important than ordering within a bucket. Cross-bucket dependencies are real; intra-bucket ones are usually parallelizable.

---

## Phase 9 — Plan the path to first paying customer

The build is in flight; that's necessary but not sufficient. Between a buildable backlog and recurring revenue is a sequence of *customer conversations* that should not happen ad-hoc. The first three names you wrote in Phase 1 now become the central organizing principle.

**Artifacts:**
- `memory/project_<name>_first_three.md` — the named first three target customers, with: contact info, intro path (warm vs cold, who introduces), the exact sentence you'll say (the "ask"), expected objections, and your prepared answers. Updated weekly.
- `legal/pilot-agreement-draft.md` — already drafted in Phase 8; in Phase 9, it goes to counsel and comes back signable.
- `customers/<name>/onboarding-checklist.md` — per-pilot pre-flight: criteria conversation script, voice capture method, expectations doc, "what success looks like" in 30/60/90 days.
- `pilot-status-log.md` — one row per pilot, updated weekly. Status, last touchpoint, blockers, next decision needed. Max 5 minutes to write.
- A **pilot → paid go/no-go bar**: the explicit conditions under which a pilot converts. Defined *before* the first pilot starts, not after.

**Outcome:** Every conversation with a target customer is structured. Each ends with one of: a "yes", a "no with a reason worth a memory", or a "not yet" with a date. Drift between customer #1 and customer #3 surfaces as patterns, not noise.

**Learning:**
- The first three are pilot *leverage*. They're not "customers" in the recurring-revenue sense; they're information sources. Treat their feedback as research output, not as a feature-request queue.
- A "no" with a reason is more valuable than a "yes" without a reason. Capture both, in identical format.
- The hard transition is *pilot → paid*, not *stranger → pilot*. Define the bar early; revisit it monthly.
- Stop pitching new prospects until the first three are either signed or have given a clean "no". You learn more from depth than width here.

---

## Phase 10 — Plan the path to scale

Once the first paying customers exist, "scale" stops meaning "build more features" and starts meaning "decide which motion turns this into the business the PRFAQ promised". Don't wait until you need the plan to write it — write it provisionally now, revise as evidence arrives.

**Artifacts:**
- `pricing-tiers.md` — the ladder beyond pilot pricing. Tiers based on volume, vertical, or feature gates. Includes the unit-economic math for each tier.
- `sales-motion.md` — self-serve, inside sales, channel, referral. One section per motion you'll actually pursue; an explicit "we will not do X" list for the motions you're choosing not to do.
- `growth-thesis.md` — the channel mix that gets you from N customers to 10N. What's the bet, what's the leading indicator that it's working, what does disconfirming evidence look like?
- `org-plan.md` — who you need to hire, in what order, gated on which milestones. Includes the "can we stay solo + AI?" alternative honestly compared.
- `expansion-options.md` — second-product candidates, adjacent verticals, adjacent geos. Each with kill criteria and the earliest signal that would justify exploration.
- `memory/feedback_kill_criteria.md` — durable rules for *when to stop*. The most under-written part of any plan; force it.

**Outcome:** When pilots produce evidence of PMF (or against it), you don't lose six weeks re-thinking from a blank page. You execute against pre-built plans and revise them in place.

**Learning:**
- Most "scale" plans don't survive contact with pilots — but having them written down is what makes the *revision* mechanical instead of paralyzing.
- "We'll know it's working when X" without "we'll know it's not when Y" is half a plan. Kill criteria are the load-bearing half.
- Write Phase 10 *before* you need it. By the time you need it, you'll be too busy reacting to write it well.

---

## Stack of artifacts (final inventory)

By end of phase 8, a fresh project directory looks roughly like:

```
~/product/<name>/
├── PRFAQ.md
├── one-pager.md
├── MVP.md                              (scope, budgets, timeline)
├── single-table-design.md              (or other data-model doc)
├── bet-risks-business-plan.md          (optional: thesis, sub-bets, kill criteria)
├── .gitignore
├── legal/
│   ├── pilot-agreement-draft.md
│   └── source-tos-determinations.md    (per phase 8 legal track)
├── prototype/                          (clickable prototype from phase 5; preserved for reference)
│   └── ...                             (Next.js static export, as in phase 5)
├── web/                                (the real app, to be built post-phase-8)
└── services/                           (backend services, post-phase-8)
```

And on GitHub:
```
<org>/<repo>
├── labels:    mvp, ingestion, enrichment, platform, design, legal:tos-review
├── milestone: "MVP <release-name>"
└── issues:    one per workstream from the four buckets
              (sources / platform / critical-path / legal)
```

And in memory:

```
~/.claude/projects/<project-slug>/memory/
├── MEMORY.md
├── project_<name>_overview.md
├── project_mvp_scope.md
├── project_admin_metrics.md          (or other domain-specific)
├── project_single_table.md
├── project_<scheme>_slug.md          (any naming schemes)
├── feedback_<principle>.md × N
└── feedback_<collaboration>.md × N
```

---

## What phases 0–7 deliberately don't include

- **Auth / accounts.** Prototype is local and not real.
- **A real backend.** localStorage is the database through phase 7.
- **Production observability.** Phases 0–7 are about *whether to build this*, not how to operate it.
- **Tests beyond click-through.** Add tests when the code outlives a single sprint of iteration. Not before.

These are not omissions — they're scope discipline for the *prototype* track. **Phase 8 is where they all show up as real work** — backend, auth, observability, tests, and the legal track that comes with selling to real customers.

## Phases 9 and 10 are forward-looking

Phases 9 and 10 are drafted from current best-guess; they will be revised once the realleads build has actually produced a paying customer and reached scale (or hit kill criteria). Treat them as scaffolding to revise, not gospel to follow.
