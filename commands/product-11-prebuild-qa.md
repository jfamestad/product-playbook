---
description: Answer the 13 pre-build questions before engineering starts. Each question forces an explicit MVP decision (or a documented "deferred + workaround") so nothing critical surfaces mid-build. Writes ./artifacts/prebuild-qa.md.
---

# /product-11-prebuild-qa

You are walking the user through the 13 pre-build questions — the things that are cheap to decide *before* code starts and expensive to discover mid-build. Most are not technical-architecture decisions (those belong in `/product-9-access-patterns` and `/product-10-data-model`); these are the decisions that span product, ops, support, GTM, and engineering — the ones that fall through the cracks.

The discipline: every question gets an answer or an explicit defer-with-workaround. "We'll figure it out" is not an answer.

Some questions partly overlap with earlier phases (`/product-6-roles` already covered tenancy and sign-on). When that's the case, **confirm and extend** rather than re-decide — but still capture the decision here so a fresh reader of `prebuild-qa.md` doesn't have to chase across files.

---

## Step 1 — Intro the job to be done

Open with this orientation:

> **Pre-build Q&A** answers the 13 questions a senior engineer / operator would ask before approving the build start. Each one is cheap to decide now and expensive to discover mid-build. Most aren't pure-tech — they span product, ops, support, GTM, and engineering. Some overlap with `/product-6-roles` (tenancy, sign-on) — for those, confirm and extend rather than re-decide.
>
> Every question gets an answer or an explicit defer-with-workaround. "We'll figure it out" doesn't pass.
>
> Output: `./artifacts/prebuild-qa.md`.

## Step 2 — Read context

Strongly recommended (each one feeds multiple questions):
- `./artifacts/mvp-scope.md` — the IN/OUT lists, constraints
- `./artifacts/product-requirements.md` — features, prioritization
- `./artifacts/roles.md` — tenancy, sign-on, operational features
- `./artifacts/gtm-plan.md` — top-3 pilots, pricing v0.1
- `./artifacts/data-model.md` and `./artifacts/access-patterns.md` — if they exist already
- `./artifacts/principles.md` — keep alignment

None are strictly required to block, but if `mvp-scope.md` is missing, **warn**: pre-build Q&A without a locked scope produces hand-waving. Strongly recommend `/product-5-mvp-scope` first.

If `./artifacts/prebuild-qa.md` exists, infer from the user's request whether to continue iterating or start fresh. Ask only if intent is genuinely unclear. Never silently overwrite.

## Step 3 — Walk the 13 questions

For each question, walk the user through the prompts. Capture an answer in their voice. If the answer is "deferred," capture: workaround at MVP, re-eval trigger.

Be patient. Some questions take 30 seconds; some take 10 minutes of conversation. The point is the artifact is concrete when done.

### Q1 — Sign-on experience
*(Business-level only. The auth technology — Cognito, Auth0, Clerk, custom — is an engineering implementation detail decided later.)*

Prompts:
- How does a user sign in? Email+password, magic link, social (Google, Microsoft, Apple), enterprise SSO (SAML/OIDC)?
- One method at MVP or several?
- Account recovery: how does a user reset access?
- Cross-reference: confirm/extend `/product-6-roles` §6.8 if it exists.

Capture:
- Sign-on method(s) at MVP
- Account-recovery approach
- Deferred methods + re-eval trigger

### Q2 — Tenancy
*(Confirm and extend from `/product-6-roles` if it exists — don't re-decide.)*

Prompts (if `/product-6-roles` already answered these):
- Restate the tenant definition in one sentence so it's local to this file.
- Anything that's changed since `/product-6-roles` was written? If yes, update `roles.md` too.

If `/product-6-roles` doesn't exist (user skipped it), block — recommend running it first.

Capture:
- Tenant definition
- Multi-tenant infra vs single-tenant per customer
- Users-per-tenant model

### Q3 — Pricing model
*(The billing-requirements lens. The payment processor — Stripe, Paddle, Lago — is an implementation detail decided later.)*

Prompts:
- What does billing infrastructure need to support at MVP? Manual invoice? Self-serve credit card? Metered usage? Tier-based?
- What gets metered, and how is the meter computed?
- How does the customer see their usage or bill?
- Trial / free tier semantics: time-limited? Quota-limited? None?
- What happens at the end of a billing period or when a customer exceeds a tier?

Capture:
- Billing model at MVP (one paragraph)
- Meter definition (if metered)
- Customer-facing view of usage/bill
- Edge cases (end-of-period, over-limit)

### Q4 — Failure modes / "bad" outputs
*(Discovery: how could the product produce a wrong, harmful, or embarrassing result? What do we do when it does?)*

Prompts:
- What's the worst plausible output the product could produce? Walk through 3–5 specific scenarios.
- For each: how likely? How would we detect it? Who's affected and how badly?
- What's our mitigation — prevention, detection, response?
- Is there a class of inputs we refuse to handle?
- What's the recovery path when a customer reports a bad result?

For AI-heavy products, push on: hallucination, bias, confident-but-wrong, prompt injection, data leakage between tenants.

Capture:
- Top 3–5 failure modes (each: scenario, likelihood, detection, response)
- Refuse-to-handle list (if any)
- Customer-reported-bad-result recovery path

### Q5 — Testing & acceptance strategy
*(Including environments — env count and naming sit under testing.)*

Prompts:
- Environments at MVP: how many (e.g., local / staging / prod), what each is for, what data lives in each?
- Automated test coverage: what kind of tests are required to merge? Unit / integration / e2e?
- Manual QA: who, when, against what test plan?
- UAT with beta customers: structured (script, sign-off) or freeform (we watch, they react)?
- What does "passes" mean? What's the bar to ship to a real customer?

Capture:
- Environment count + purpose per env
- Required automated coverage
- Manual QA approach
- UAT approach
- Definition of "ready to ship"

### Q6 — Naming conventions
*(Dev AND prod components. Establishing this now prevents the "what's it called in code vs in the UI vs in the URL" drift.)*

Prompts:
- Customer-facing entity names — what does the user see? (E.g., "Workflow", "Run", "Lead".)
- Internal code names — what do we call them in code? (Same as customer? Different?)
- Services and infra: naming pattern for repos, services, S3 buckets, DynamoDB tables, queues, env vars.
- Environments: how is "prod" disambiguated from "staging" in resource names?
- Domains: production domain, staging domain, dev preview domain.

Capture:
- Customer-facing terminology (table form: term → meaning)
- Internal code terminology (where different from customer-facing)
- Resource naming pattern (one-line template per resource type)
- Domain plan per environment

### Q7 — Email & comms
*(Transactional email infra and the channels we use to talk to customers.)*

Prompts:
- Transactional email: what triggers an email at MVP? (Signup, password reset, invite, billing receipt, alert?)
- From-address: what does the customer see in their inbox? (`hello@<product>.com`? `notifications@<product>.com`?)
- Reply-to handling: do replies go anywhere? Monitored? Auto-responder?
- Outbound-only email infra (Postmark, SES, Resend) — pick category, processor is implementation detail.
- Other customer channels at MVP: in-app notifications? Slack/Discord? SMS? None?
- Status/incident comms: where do we tell customers when something breaks? (Status page, email, in-app banner, all-of-the-above, nothing?)

Capture:
- Email trigger list at MVP
- From-address + reply-to handling
- Email infra category (deferred specific vendor to implementation)
- Other channels at MVP
- Status/incident comms plan

### Q8 — Human-in-the-loop
*(How do humans improve what AI delivers? Where does AI hand off to a person, and what's the interaction shape? Cover both experience-improvement and risk-management lenses.)*

Prompts:
- Where does AI hand off to a human in the product's flows? Why there?
- What's the interaction shape at each handoff?
  - Review-and-approve (AI proposes, human gates)
  - Edit-the-draft (AI drafts, human refines and ships)
  - Conversational handoff (AI escalates a chat to a person)
  - Escalation on uncertainty (AI signals "I'm not sure" and routes to human)
  - Co-pilot (AI suggests inline; human stays in flow)
- Who is the human at each handoff — customer user, customer admin, our team?
- Experience-improvement lens: where does human judgment, taste, or domain expertise lift the output beyond what AI alone produces?
- Risk-management lens: where does a human gate catch bad outputs before they ship?
- For each handoff: is it always-on, threshold-triggered, customer-opt-in, or customer-required?

For AI-light products, this question is fast. For AI-heavy products, this is one of the most important decisions in the playbook — bad HITL design is the #1 source of "demo great, production broken" patterns.

Capture (table form):
- Per handoff: where in the flow / interaction shape / who's the human / trigger (always / threshold / opt-in / required) / lens (experience or risk or both)

### Q9 — Pilot / beta deal scope
*(What we're promising the first three customers, contractually or otherwise.)*

Prompts:
- Per top-3 customer, what did we commit to? Walk each one separately.
- SLA: uptime target? Response-time for support?
- Support response: hours, channels, on-call?
- Data handling: where is their data stored, who can see it, can they request export/deletion?
- Exit terms: if they want to leave, what do they get back? How long do we keep their data after?
- Pricing terms specific to this pilot: free? Discounted? Standard? With what duration?
- Anything in writing? Pilot agreement signed or template ready?

Capture (per customer):
- SLA commitments
- Support commitments
- Data handling commitments
- Exit terms
- Pricing terms
- Agreement status (signed / drafted / verbal / nothing)

### Q10 — Build sequence
*(Features + components in build order, with prioritization between them. Feeds `/product-12-backlog` ordering.)*

Prompts:
- Pull the P0 list from `product-requirements.md`. In what order do these get built?
- For each P0 feature, name the components it requires: data layer, auth, UI, integrations.
- What's the critical path through these — the longest dependency chain?
- What's the first thing a customer can see / use? (The "vertical slice" target.)
- Where could parallel workstreams run if more than one person is building?

Capture:
- Critical path (ordered list of features-and-components)
- Vertical-slice target (first thing a customer touches)
- Parallel workstreams (if any)
- Cut-order reminder (carried from `product-requirements.md`)

### Q11 — Repo organization
*(Soft requirement — many users running this playbook aren't technical. If the user shrugs, propose a sensible default and move on. The point is the artifact says *something* so the next person — human or AI — has somewhere to start.)*

Prompts:
- Single repo or multi-repo at MVP? (Monorepo simpler at this scale; multi-repo only if separate deployment cadences are needed.)
- Top-level layout: how do `/web`, `/services`, `/infra`, `/docs`, `/legal` map?
- Branch model: trunk-based with short-lived branches? PRs required to merge? CI gates?
- Where do AI agents / prototypes live? (Often `/prototype` or `/agents`.)
- Where do this playbook's artifacts live? (Convention: `./artifacts` at repo root.)

If the user is non-technical, propose:
- Monorepo, single GitHub repo, private
- `/web`, `/services`, `/infra`, `/artifacts`, `/legal` at top level
- Trunk-based with PRs, CI required to merge
- AI agents in `/agents`
- This playbook's outputs in `./artifacts`

Capture what was agreed (defaults or user's overrides).

### Q12 — Observability & on-call
*(What we monitor, who gets paged, how.)*

Prompts:
- What does "the product is working" look like in metrics? Name 3–5 SLIs (service-level indicators) we care about. (Examples: API p95 latency, workflow success rate, signup completion rate, error rate per route.)
- What are the SLO targets at MVP? "Best-effort during beta, no SLA" is a valid answer — capture it as the answer rather than the absence of one.
- Logging: structured logs to where? (CloudWatch, Datadog, Grafana, just files?)
- Metrics dashboard: do we have one at MVP, or do we tail logs?
- Alerting: what conditions trigger a page? What gets a Slack ping vs a phone call?
- On-call: who's on call? Always one human? Rotation? Business-hours-only at MVP?
- Customer-visible status: do we have a status page at MVP, or only internal monitoring?

Capture:
- SLI list (3–5)
- SLO targets (or "best-effort during beta")
- Log destination + retention
- Dashboard / monitoring tooling category (vendor is impl detail)
- Alerting rules (one-line each)
- On-call rotation at MVP
- Customer-visible status approach

### Q13 — Customer-facing docs / knowledge base
*(What exists at launch — for the customer to read, not just for our internal team.)*

Prompts:
- What does the customer need to be able to read at launch?
  - Quick-start / getting-started guide
  - Feature explainers
  - Pricing & billing FAQ
  - Data & privacy explainer
  - Terms of service / privacy policy
  - Troubleshooting / common errors
- Where do these live? (In-product help? Separate docs site? Notion/GitBook? GitHub wiki?)
- Who writes them? When?
- Search / navigability: how does a customer find an answer?
- Customer support escalation: when docs don't answer, what's the path? (Email, in-app chat, Discord?)
- Is there a changelog / release notes channel for ongoing comms?

For pilot-stage products, "docs" can mean a single page or a shared Notion. Capture what's actually planned, not what would be ideal.

Capture:
- Doc set at launch (list)
- Where it lives
- Author + timing
- Search/navigation approach
- Escalation path
- Changelog/release-notes plan

## Step 4 — Cross-reference any conflicts

After all 13 questions, scan for conflicts with upstream artifacts. Common ones:

- Q1 (sign-on) vs `/product-6-roles` §6.8 — flag if changed
- Q2 (tenancy) vs `/product-6-roles` §4 — flag if changed
- Q3 (pricing model) vs `/product-3-gtm` §5 — flag if MVP billing diverged from pricing v0.1
- Q9 (pilot deal scope) vs `/product-3-gtm` top-3 — flag if pilot commitments don't match the GTM ask

If any conflict, surface it: "this conflicts with X.md — should we update X.md too?" Don't silently let the artifacts diverge.

## Step 5 — Write `./artifacts/prebuild-qa.md`

Use this structure exactly:

```markdown
# Pre-build Q&A — <Product name>

*Draft date: YYYY-MM-DD*
*Source artifacts: mvp-scope.md, product-requirements.md, roles.md, gtm-plan.md, principles.md*

## Q1 — Sign-on experience
**Method(s) at MVP:** <...>
**Account recovery:** <...>
**Deferred methods + trigger:** <...>

## Q2 — Tenancy (confirmed from roles.md)
**Tenant definition:** <one sentence>
**Infrastructure:** <multi-tenant shared / single-tenant per customer>
**Users per tenant:** <one / many>

## Q3 — Pricing model
**Billing model at MVP:** <one paragraph>
**Meter (if metered):** <...>
**Customer-facing view:** <...>
**Edge cases:** <end-of-period / over-limit / etc.>

## Q4 — Failure modes / "bad" outputs
| Scenario | Likelihood | Detection | Response |
|---|---|---|---|
| <...> | <...> | <...> | <...> |

**Refuse-to-handle list:** <...>
**Customer-reported-bad-result recovery path:** <...>

## Q5 — Testing & acceptance strategy
**Environments:**
- <env name> — purpose, data
- ...

**Automated coverage required:** <...>
**Manual QA:** <who, when, against what>
**UAT:** <approach>
**Definition of "ready to ship":** <...>

## Q6 — Naming conventions
**Customer-facing terminology:**
| Term | Meaning |
|---|---|
| <...> | <...> |

**Internal code terminology (if different):**
| Internal name | Customer-facing name |
|---|---|
| <...> | <...> |

**Resource naming pattern:**
- Repos: `<pattern>`
- Services: `<pattern>`
- Buckets: `<pattern>`
- Tables: `<pattern>`
- Queues: `<pattern>`
- Env vars: `<pattern>`

**Domain plan per environment:**
- prod: `<...>`
- staging: `<...>`
- dev/preview: `<...>`

## Q7 — Email & comms
**Email triggers at MVP:** <list>
**From-address:** <...>
**Reply-to handling:** <...>
**Email infra category:** <category — vendor TBD in engineering>
**Other channels at MVP:** <...>
**Status/incident comms:** <...>

## Q8 — Human-in-the-loop
| Where in flow | Interaction shape | Human | Trigger | Lens |
|---|---|---|---|---|
| <...> | <review-and-approve / edit-the-draft / etc.> | <customer user / admin / our team> | <always / threshold / opt-in / required> | <experience / risk / both> |

## Q9 — Pilot / beta deal scope
### Customer 1 — <name>
- **SLA:** <...>
- **Support:** <...>
- **Data handling:** <...>
- **Exit terms:** <...>
- **Pricing terms:** <...>
- **Agreement status:** <signed / drafted / verbal / nothing>

### Customer 2 — <name>
<same structure>

### Customer 3 — <name>
<same structure>

## Q10 — Build sequence
**Critical path:**
1. <feature/component>
2. <feature/component>
3. ...

**Vertical-slice target (first customer-touchable thing):** <...>
**Parallel workstreams (if any):** <...>
**Cut order reminder:** see `product-requirements.md`.

## Q11 — Repo organization
**Repo strategy:** <monorepo / multi-repo + why>
**Top-level layout:**
- `/web` — <...>
- `/services` — <...>
- `/infra` — <...>
- `/artifacts` — playbook outputs
- `/legal` — pilot agreements, ToS, privacy
- `/agents` — AI agents / prototypes (if applicable)

**Branch model:** <trunk-based with PRs / git-flow / other>
**CI gates required to merge:** <...>

## Q12 — Observability & on-call
**SLIs (3–5):**
- <...>

**SLO targets:** <or "best-effort during beta, no SLA">
**Log destination + retention:** <...>
**Dashboard / monitoring category:** <category — vendor TBD in engineering>
**Alerting rules:**
- <condition> → <page / Slack / email>

**On-call at MVP:** <rotation / single human / business hours only>
**Customer-visible status:** <status page / none / in-app banner>

## Q13 — Customer-facing docs / knowledge base
**Doc set at launch:**
- <...>

**Lives at:** <...>
**Author + timing:** <...>
**Search/navigation:** <...>
**Escalation path when docs don't answer:** <...>
**Changelog / release notes plan:** <...>

---

## Conflicts surfaced
- <none / or: "Q3 pricing model conflicts with gtm-plan.md §5 — needs reconciliation by <owner> before build">
```

Read the final draft back to the user in summary form before writing — especially Q4 (failure modes), Q8 (HITL), Q9 (pilot commitments per customer), Q10 (critical path), and Q12 (SLIs + on-call). Confirm. Then write the file.

## Step 6 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with this header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append:

```
YYYY-MM-DD HH:MM PST — /product-11-prebuild-qa — ./artifacts/prebuild-qa.md
```

Use the actual current local PST time. If unsure of the timezone, ask once and remember.

## Step 7 — Offer toolchain integration

Read `./artifacts/toolchain.md` if it exists. Look at the **Docs / wiki** value.

If a docs/wiki tool is named (Confluence, Notion, Google Docs) AND its MCP is available, offer: "Want me to publish prebuild-qa.md to your <tool>?" Engineering and ops will reference these answers across the build — a wiki home pays off. If yes, push as additive sync and add a `published_url:` line near the top of the local file. Local markdown remains source of truth.

If no tool is named, "(not yet)", or MCP unavailable, skip silently.

See the playbook SKILL's "Toolchain integration" convention for the full pattern.

## Step 8 — Tell them what's next

Close with:

> Next:
> - `/product-12-backlog` — turn the locked scope + answered pre-build questions into a buildable backlog as local markdown.
> - Or `/product-7-stress-test ./artifacts/prebuild-qa.md` if any answer felt soft — especially Q4 (failure modes), Q8 (HITL), Q9 (pilot commitments), or Q12 (observability). Operator + Customer + Devil's Advocate POVs are sharpest here.
