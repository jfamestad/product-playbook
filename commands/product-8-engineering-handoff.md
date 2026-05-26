---
description: Write the product requirements that engineering uses to start work. Features with acceptance criteria, prioritization, known business dependencies. Writes ./artifacts/product-requirements.md.
---

# /product-8-engineering-handoff

You are producing the product requirements document — the brief engineering reads to start work. Internally we call this the engineering handoff; the file on disk is `./artifacts/product-requirements.md`.

Three jobs, all required:

1. **Clear what to build** — features with engineer-actionable acceptance criteria.
2. **Prioritization between features** — explicit tiers, critical path, cut order.
3. **Known business dependencies** — external services, vendor agreements, legal/compliance, customer obligations, pricing implementation, launch coordination. Each with status and owner.

What this is **not**:
- Not the architecture (that's `/product-9-access-patterns` and `/product-10-data-model`).
- Not the buildable backlog (that's `/product-12-backlog`).

This is the brief that informs both.

---

## Step 1 — Intro the job to be done

Open with this orientation:

> **Engineering handoff** produces the product requirements document — `./artifacts/product-requirements.md`. It's the brief engineering reads to start work. Three jobs: clear what to build (features with acceptance criteria), prioritization between features (tiers + critical path + cut order), and known business dependencies (each with status and owner).
>
> This isn't architecture (that's `/product-9-access-patterns` + `/product-10-data-model`) or the backlog (`/product-12-backlog`) — it's the brief those phases will draw from.
>
> Output: `./artifacts/product-requirements.md`.

## Step 2 — Read context

Required upstream artifacts:
- `./artifacts/mvp-scope.md` — the IN/OUT lists, constraints, success criteria, feedback collection design

If `mvp-scope.md` is missing, **block**: a requirements doc without a locked scope is fiction. Recommend running `/product-5-mvp-scope` first.

Strongly recommended:
- `./artifacts/PRFAQ.md` — for the WHY summary and customer context
- `./artifacts/gtm-plan.md` — for customer obligations, beta timing, pricing implementation requirements

Optional but useful:
- `./artifacts/one-pager.md` — condensed value statement
- `./artifacts/principles.md` — to keep requirements aligned with stated principles
- `./artifacts/stress-tests/mvp-scope-*.md` — any open block-and-tackle items from MVP stress-test

If `./artifacts/product-requirements.md` exists, infer from the user's request whether to continue iterating or start fresh. Ask only if intent is genuinely unclear. Never silently overwrite.

## Step 3 — Restate the build (the WHY)

Lead the document with a short context block so engineers understand what they're contributing to. Pull from upstream artifacts:

- **Customer:** one sentence from PRFAQ/GTM
- **Unit of value:** one sentence from GTM pricing
- **What the MVP delivers:** one paragraph from MVP scope's stands-on-its-own section
- **Success bar:** the concrete metrics from MVP scope
- **Constraints:** time / money / effort from MVP scope

This isn't a re-do of upstream artifacts — it's the orientation paragraph for someone who opens this doc cold.

## Step 4 — Features with acceptance criteria

For each item in the MVP scope IN list, expand to engineering-actionable detail. Walk through with the user.

### Per-feature template

```
### <Feature name>

**Description:** <Customer-facing one-paragraph description>

**Functional acceptance criteria:**
- <Criterion 1 — what does the user do, what should happen>
- <Criterion 2>
- ...

**Non-functional requirements:**
- **Latency:** <e.g., "p95 response < 2s">
- **Availability:** <e.g., "best-effort during beta, no SLA">
- **Scale:** <e.g., "supports 10 concurrent beta users at MVP">
- **Accessibility:** <e.g., "keyboard-navigable; WCAG AA aspirational">
- **Security:** <e.g., "auth required; no PII in logs">

**Instrumentation requirements:**
- <Specific events to emit — drawn from MVP scope's feedback collection design>
- <Counters to track>
- <Signals that feed the decisions documented in MVP scope>

**Effort estimate:** <Carried from MVP scope: S/M/L/XL or N weeks>
```

Acceptance criteria must be engineer-actionable. The MVP-scope "definition of done" is the product-level bar; here we sharpen it for engineering. Example:

- **Scope-level:** "User can submit feedback after a workflow run."
- **Engineering-actionable:** "After workflow run completes, user sees a feedback modal with 5-star rating + free-text field. Submitting fires event `feedback_submitted` with `{rating, comment, workflow_id, user_id}`. Modal can be dismissed without rating. Dismissal fires event `feedback_dismissed`. State persists per workflow run — modal does not re-appear for the same run."

Non-functional requirements often go unwritten at MVP and get demanded later under pressure. Capture them now even if loose. "Best-effort" is a valid answer; "we'll figure it out" is not.

Instrumentation requirements connect directly to the feedback collection design from MVP scope. If MVP scope said "we'll instrument event X to test assumption Y," every feature that's relevant to X carries that instrumentation requirement here.

## Step 5 — Prioritization

### 5.1 Tier per feature
Default scheme: **P0 (must ship)** / **P1 (should ship)** / **P2 (nice to ship)**. If the team uses MoSCoW (Must/Should/Could/Won't), explicit numbered ordering, or another scheme, use that — but be explicit about which scheme and what the tiers mean.

Assign every feature a tier. No feature gets to be unassigned.

### 5.2 Critical path
Identify which features depend on which. Draw the dependency chain in prose or a simple list:

```
Critical path:
- Feature A (P0) blocks Feature C (P0) blocks Feature E (P1)
- Feature B (P0) is independent
- Feature D (P1) requires Feature A (P0) completing
```

The longest chain through P0 features is the gating sequence for MVP ship.

### 5.3 Cut order
If we're forced to cut scope mid-build, what gets cut first? Decided now (sober), honored later (when emotional). Be explicit:

```
Cut order (first to last):
1. P2 features in dependency-leaf order
2. P1 features in dependency-leaf order
3. (Last resort) downgrade P0 features to manual fallback — list which P0s can degrade and how
```

Manual fallback options for P0 are particularly valuable to enumerate. Often a P0 has a "concierge mode" or "manual workaround" version that buys time at modest cost.

## Step 6 — Known business dependencies

This is where many handoffs are weakest and most expensive. Walk through every category. For each dependency, capture:

- **What it is** — specific
- **Category** — pick one: external API / vendor agreement / legal-compliance / customer obligation / pricing implementation / integration / launch coordination / data source
- **Status** — resolved / in-progress / blocked
- **Owner** — named human responsible for closing it
- **ETA** — date by which it must be resolved
- **Blocks** — which feature(s) depend on this

### Categories to walk through

**External APIs / data sources:**
- Auth provider (Cognito, Auth0, custom?)
- Payment processor (Stripe, etc.)
- AI / LLM providers (Bedrock, OpenAI, Anthropic — and their cost guarantees)
- Email / SMS / comms
- Data vendors (the data the MVP relies on — source agreements, terms, rate limits)
- Analytics / observability

**Vendor / partner agreements:**
- Beta customer agreements (signed? template ready?)
- Data licensing
- White-label or co-marketing arrangements

**Legal / compliance:**
- Terms of service
- Privacy policy
- DPA / data handling
- Industry-specific regs (HIPAA, GDPR, CCPA, FINRA, RESPA, etc.)
- Open-source license compatibility for chosen dependencies

**Customer obligations (carried from GTM top-3):**
- Promised onboarding dates per beta customer
- SLA / response time commitments
- Scope promises ("we said we'd have feature X for them")
- Pricing terms specific to that customer

**Pricing implementation:**
- What does billing infrastructure need to support at MVP? (Tier model? Metered? Manual invoice?)
- What gets metered and how is the meter computed?
- How does the customer see their usage / bill?

**Integrations:**
- Customer systems we integrate with at MVP (CRM, calendar, file storage, etc.)
- Any partner platforms

**Launch coordination:**
- Marketing site / landing page
- Public docs
- Support channel (email, intercom, slack?)
- Announcement timing

Don't pad. If a category doesn't apply to this MVP, skip it. But ask the question for each category — "is this resolved?" — so nothing slips through because it wasn't named.

## Step 7 — Out of scope (defer list)

Carry forward the OUT list from MVP scope. Engineers reading this doc need to know what NOT to build. Brief is fine — just the feature names with one-line "why deferred" reminders. The full re-evaluation-trigger detail stays in `mvp-scope.md`.

## Step 8 — Constraints to honor

Pulled forward and consolidated:

- **Ship by:** <date from MVP scope>
- **Money budget:** $X total / $Y per month
- **Effort budget:** N person-weeks
- **Tech stack:** <Any already-decided opinions — language, framework, infra. If undecided, say "to be decided in /product-7" and leave it open.>

## Step 9 — Open business questions for engineering input

Decisions where engineering needs product/business input before they can scope or build. Each item:

- **Question:** <specific>
- **Why it matters:** <what hinges on it>
- **Owner to close:** <named human, usually product>
- **By when:** <date>

Examples:
- "If a beta customer hits the rate limit, do we error or queue? Queueing requires job infra."
- "Should pricing meter resets be monthly or rolling 30-day?"
- "Are we OK auto-deleting failed workflow data after 30 days, or do we keep it forever?"

If the user can't answer right now, capture the question — don't paper over.

## Step 10 — Glossary

Domain terms engineers should understand. Especially:
- Customer-facing terms (what the user calls things)
- Internal terms (what we call them in code / on whiteboards)
- Where these diverge, note both columns so naming stays consistent across layers

Keep tight. Five to fifteen terms. Not an exhaustive dictionary.

## Step 11 — Write `./artifacts/product-requirements.md`

Use this structure exactly:

```markdown
# Product Requirements — <Product name>

*Draft date: YYYY-MM-DD*
*Source artifacts: PRFAQ.md, gtm-plan.md, mvp-scope.md*

## Context (the WHY)

**Customer:** <...>
**Unit of value:** <...>
**What the MVP delivers:** <...>
**Success bar:** <...>
**Constraints:** ship by <date> / $<X> budget / <N> person-weeks

---

## Features

### <Feature 1 name> [P0]
**Description:** <...>

**Functional acceptance criteria:**
- <...>
- <...>

**Non-functional requirements:**
- Latency: <...>
- Availability: <...>
- Scale: <...>
- Accessibility: <...>
- Security: <...>

**Instrumentation:**
- <event/counter 1>
- <event/counter 2>

**Effort estimate:** <S/M/L/XL>

### <Feature 2 name> [P0]
<same structure>

...

---

## Prioritization

**Scheme:** P0 (must ship) / P1 (should ship) / P2 (nice to ship)

**Critical path:**
- <Feature A> blocks <Feature C> blocks <Feature E>
- ...

**Cut order (first to last):**
1. <...>
2. <...>
3. <Last-resort P0 degradations: ...>

---

## Known business dependencies

| Dependency | Category | Status | Owner | ETA | Blocks |
|---|---|---|---|---|---|
| <...> | <...> | <...> | <...> | <...> | <...> |

---

## Out of scope (defer list)
- <Feature> — <one-line why deferred reminder>
- ...

---

## Constraints
- **Ship by:** <date>
- **Money budget:** <$X>
- **Effort budget:** <N person-weeks>
- **Tech stack:** <decisions or "TBD in /product-7">

---

## Open business questions for engineering input
- **Q:** <question> — **Why:** <...> — **Owner:** <...> — **By:** <date>
- ...

---

## Glossary
- **<Term>:** <definition> *(customer-facing)* / *(internal)*
- ...
```

Read the final draft back to the user in summary form before writing — especially the feature tiers, critical path, cut order, and the business-dependencies table. Confirm. Then write the file.

## Step 12 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with this header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append:

```
YYYY-MM-DD HH:MM PST — /product-8-engineering-handoff — ./artifacts/product-requirements.md
```

Use the actual current local PST time. If unsure of the timezone, ask once and remember.

## Step 13 — Offer toolchain integration

Read `./artifacts/toolchain.md` if it exists. Two destinations are relevant here:

- **Docs / wiki** (Confluence, Notion, Google Docs) → for the full product-requirements doc.
- **Issue tracker** (GitHub Issues, Jira, Linear, Asana) → for the feature tiers as an epic / parent issue (children get created later by `/product-12-backlog`).

If either is named AND its MCP is available, offer the corresponding push: "Want me to publish product-requirements.md to your <docs tool>?" and/or "Want me to create the MVP epic in your <issue tracker> so backlog items can be attached to it?" If yes, push as additive sync and add a `published_url:` / `epic_url:` reference near the top of the local file. Local markdown remains source of truth.

If no tool is named, "(not yet)", or MCP unavailable, skip silently.

See the playbook SKILL's "Toolchain integration" convention for the full pattern.

## Step 14 — Tell them what's next

Close with:

> Next:
> - `/product-9-access-patterns` — define how the data will be queried (which drives the data model).
> - Or `/product-7-stress-test ./artifacts/product-requirements.md` if prioritization felt soft, business dependencies look incomplete, or open questions feel load-bearing.
>
> Then: `/product-10-data-model`, `/product-11-prebuild-qa`, `/product-12-backlog`.
