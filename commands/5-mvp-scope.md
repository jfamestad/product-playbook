---
description: Lock the MVP scope — IN list, explicit OUT/defer list, stands-on-own test, path-to-vision sanity check, feedback collection design, success and kill criteria. Writes ./artifacts/mvp-scope.md.
---

# /5-mvp-scope

You are helping the user lock the MVP scope. The MVP is a **proper subset** of the long-term product vision — it will NOT have all the promised features. It must:

1. Stand on its own (deliver value as-is, not be a half-product)
2. Not paint into a corner for the long-term vision
3. Be optimized for collecting customer feedback (its whole point)

The discipline of this phase is **numbers, not adjectives**. "Focused" / "lean" / "small" decay; "$Y/month data budget, 3 metros, 5 beta customers, ship by July 15" doesn't. The artifact must use numbers wherever a count, a dollar, or a date can substitute for a vague word.

This is the second of the playbook's two natural antagonist gates. After this command, the standard next step is `/7-stress-test ./artifacts/mvp-scope.md`.

---

## Step 1 — Intro the job to be done

Open with this orientation:

> **MVP scope** is where we lock what we WILL build, what we will NOT build, and what bar the MVP must clear to be worth shipping. The MVP is a proper subset of the long-term vision — it won't have everything we promised. What it must do: stand on its own, not paint us into an architectural corner, and be optimized to collect the customer feedback we need to decide what comes next.
>
> The discipline here is numbers. "Lean and focused" is what we say when we haven't decided. We'll force every adjective into a count, a dollar, or a date.
>
> Output: `./artifacts/mvp-scope.md`.

## Step 2 — Read context

Required upstream artifacts:
- `./artifacts/PRFAQ.md` — for the long-term vision the MVP is a subset of, and the assumptions to test
- `./artifacts/gtm-plan.md` — for the ICP, first three, pricing v0.1, and kill criteria

If either is missing, **block**: an MVP scope without a vision to subset or named customers to ship to is theatre. Recommend running the missing command(s) first.

Optional but useful:
- `./artifacts/one-pager.md` — for the condensed value statement to test the stands-on-own claim against
- `./artifacts/principles.md` — to keep the scope aligned with the user's stated principles (especially numbers > adjectives if adopted)

If `./artifacts/mvp-scope.md` exists, infer from the user's request whether they want to continue iterating or start fresh. Ask only if intent is genuinely unclear. Never silently overwrite.

## Step 3 — The IN list (what we WILL build)

Walk the user through each item in scope. For each, capture:

- **Name** — short, memorable.
- **One-sentence description** — what it does in customer terms.
- **Why in MVP** — which assumption from the PRFAQ's Internal FAQ does it test, OR what slice of customer value does it deliver. Every IN item should map to one or the other (ideally both). If it maps to neither, ask: "Why is this in?"
- **Definition of done** — concrete enough that two people would agree it's done or not done.
- **Effort estimate** — t-shirt (S/M/L/XL) or week count. Rough is fine; precision comes in engineering handoff.

After listing the items, lock the **scope-constraints-with-numbers** block:

- **Total IN items:** N
- **Geographic scope:** N metros / N countries / etc.
- **Source / vertical count:** N
- **Beta customer target:** N customers (should match or be ≤ the GTM top-3 plus any expansion)
- **Time to ship:** by <date>
- **Money budget:** $X total / $Y per month
- **Effort budget:** N person-weeks

If the user says "we'll see" or "as many as we need," push back. Pick a number. The number can be revised, but the scope must not be unbounded.

## Step 4 — The OUT list (what we will NOT build / explicit defer)

This list is as important as the IN list. Vague defer = guaranteed scope creep mid-MVP. Walk through the long-term vision (PRFAQ + one-pager) and, for each feature that won't be in MVP, capture:

- **Feature name** — what's being deferred.
- **Why deferred** — pick one or more: cost / complexity / signal-needed / not-core-to-MVP-value / blocked-on-prior-work / can-be-faked-in-MVP.
- **Re-evaluation trigger** — what specific event would cause us to reconsider this feature? Examples: "after 10 beta customers ask for it," "after we hit $X MRR," "after we know whether segment Y is right," "in v1.1 if churn is < N%." Don't write "later" — that's not a trigger.

Aim to defer aggressively. The bar for IN is "the MVP cannot deliver value without this." If a feature passes that bar, it's IN; if it doesn't, it's OUT. Middle ground is where MVPs balloon.

## Step 5 — Stands-on-its-own test

Two checks:

### 5.1 The customer-sentence test
What's the single sentence a beta customer would use, three weeks in, to describe what they got? Write it. If the sentence contains words like "limited," "missing," "eventually," "preview" — the MVP isn't standing on its own; it's a demo with the value cut out. Sharpen the IN list or revisit defer until the sentence is a positive value claim, not a hedge.

### 5.2 The anti-pattern check
Look at the IN list and ask: are we shipping the value, or are we shipping the infrastructure that's only valuable *with* the deferred features? Examples of the failure mode:

- Shipping an auth + dashboard but deferring the data that makes the dashboard useful.
- Shipping the pipeline but deferring the user-facing thing the pipeline feeds.
- Shipping a workflow that requires manual data entry while deferring the integrations that make data entry unnecessary.

If you spot the anti-pattern, reorganize. The MVP must ship the slice that delivers value — even if it ships *less* of the supporting infrastructure.

## Step 6 — Path-to-vision sanity check

A quick pass (deeper architectural work happens in `/10-access-patterns` and `/11-data-model`). For each major scoping choice in the IN list:

- Does adding deferred feature X later require a rewrite, or is it purely additive?
- Are there data-model, integration, or auth choices that would make later features painful?

You don't need to solve the architectural problem here — just **flag** the corner-painting risks so they get explicit attention in the architecture phases. Capture a short "watch-out" list:

> Example: "MVP uses single-tenant data; multi-tenant will require migration. Flagged for /11-data-model."

If no corner-painting risks are visible, write that explicitly. Don't manufacture risks for completeness.

## Step 7 — Feedback collection design (prominent)

The MVP's whole point is to collect the customer feedback needed to decide what comes next. Design this deliberately. Walk through:

### 7.1 Assumptions under test
From the PRFAQ Internal FAQ, which assumptions does this MVP validate or falsify? List them. These are the "what would have to be true" load-bearing assumptions. Examples:

- "Customers will pay $X/month for this unit of value."
- "We can reach segment Y via channel Z."
- "Cost-to-serve scales sublinearly with customer count."

### 7.2 Signals collected
For each assumption, what signal would update our belief?

- **Instrumented metrics:** name the specific events / counters / dashboards.
- **Customer interviews:** cadence (every 2 weeks? after each beta cohort?), interview owner, sample interview questions.
- **In-app feedback:** mechanism (NPS prompt? feedback widget? scheduled check-in?).
- **Churn / cancellation signals:** what we capture when someone leaves.
- **Willingness-to-pay signal:** how do we test pricing during beta? Direct ask? Price test? Both?

### 7.3 Collection ownership
Named person on our side who runs feedback collection. Not "the team." One human accountable for synthesizing signal → decisions.

### 7.4 What the feedback decides
Be explicit about what decisions the collected feedback will inform:
- **Continue** as planned to v1.1
- **Pivot** — what would trigger a pivot, to what?
- **Kill** — what would trigger a kill (reference GTM kill criteria + MVP-specific extensions in Step 8)
- **Promote a deferred feature** — which OUT-list items become candidates if X feedback emerges?

## Step 8 — Success & kill criteria

### 8.1 Success
Concrete metrics for end-of-MVP / end-of-beta. Examples that pass the numbers > adjectives bar:

- "5 of 5 beta customers complete one full workflow."
- "3 of 5 beta customers commit to a paid plan at $X/month."
- "Cost-to-serve per active beta customer stays under $Y/month."
- "Customer interview scores: average ≥ N on the question 'would you be disappointed if this went away?'"

Don't accept "users love it" or "we get traction." Pick numbers.

### 8.2 Kill criteria
Reference and extend the kill criteria from `/3-gtm`. Add MVP-specific ones:

- **Cost-to-serve floor:** at $X/customer/month, unit economics break. Below this margin, kill the unit economics (kill the product, restructure pricing, or restructure costs).
- **Time overrun:** if MVP isn't shipped by <date> + N weeks, kill or descope further.
- **Money overrun:** if MVP spend exceeds $X, kill or descope.
- **Engagement floor:** if N of M beta customers stop using it within X weeks, signal the MVP isn't delivering the promised value.

Kill criteria should be the kind of thing that, written down sober, will be honored when emotional. Vague kill criteria are no kill criteria.

## Step 9 — Write `./artifacts/mvp-scope.md`

Use this structure exactly:

```markdown
# MVP Scope — <Product name>

*Draft date: YYYY-MM-DD*

## Constraints (numbers, not adjectives)

- **Total IN items:** N
- **Geographic scope:** <...>
- **Source / vertical count:** <...>
- **Beta customer target:** N
- **Time to ship:** by <date>
- **Money budget:** $X total / $Y per month
- **Effort budget:** N person-weeks

---

## IN — What we will build

### <Item 1 name>
- **Description:** <one sentence>
- **Why in MVP:** <assumption tested + value delivered>
- **Definition of done:** <concrete>
- **Effort estimate:** <S/M/L/XL or N weeks>

### <Item 2 name>
<same structure>

...

---

## OUT — What we will NOT build (explicit defer)

### <Deferred feature 1>
- **Why deferred:** <reason>
- **Re-evaluation trigger:** <specific event>

### <Deferred feature 2>
<same structure>

...

---

## Stands-on-its-own

**Customer-sentence test:**
> "<The sentence a beta customer would use, 3 weeks in.>"

**Anti-pattern check:**
<Pass / fail + notes if fail>

---

## Path-to-vision sanity check

Watch-outs flagged for later architecture phases:
- <Risk 1 — flagged for /product-N>
- <Risk 2 — flagged for /product-N>

(Or: "No corner-painting risks identified.")

---

## Feedback collection design

### Assumptions under test
- <Assumption 1>
- <Assumption 2>
- <Assumption 3>

### Signals collected
- **Instrumentation:** <named events / counters>
- **Interviews:** <cadence, owner, sample questions>
- **In-app feedback:** <mechanism>
- **Churn signal:** <what we capture>
- **WTP signal:** <how we test pricing>

### Collection ownership
<Named person>

### Decisions the feedback informs
- **Continue:** <trigger>
- **Pivot:** <trigger and to what>
- **Kill:** <trigger>
- **Promote deferred feature:** <which OUT items + what feedback would trigger promotion>

---

## Success criteria (end-of-MVP)
- <Metric 1 with number>
- <Metric 2 with number>
- <Metric 3 with number>

## Kill criteria (extends GTM)
- **Cost-to-serve floor:** $<X>
- **Time overrun:** <threshold>
- **Money overrun:** <threshold>
- **Engagement floor:** <threshold>
- **Other MVP-specific triggers:** <...>
```

Read the final draft back to the user in summary form before writing — especially the IN/OUT lists and the constraint numbers. Confirm. Then write the file.

## Step 10 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with this header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append:

```
YYYY-MM-DD HH:MM PST — /5-mvp-scope — ./artifacts/mvp-scope.md
```

Use the actual current local PST time. If unsure of the timezone, ask once and remember.

## Step 11 — Offer toolchain integration

Read `./artifacts/toolchain.md` if it exists. Look at the **Docs / wiki** value.

If a docs/wiki tool is named (Confluence, Notion, Google Docs) AND its MCP is available, offer: "Want me to publish mvp-scope.md to your <tool>?" If yes, push as additive sync and add a `published_url:` line near the top of the local file. Local markdown remains source of truth.

If no tool is named, "(not yet)", or MCP unavailable, skip silently.

See the playbook SKILL's "Toolchain integration" convention for the full pattern.

## Step 12 — Tell them what's next

Close with:

> Next:
> - `/6-roles` — define solution roles, user groups, and permissions before the stress test.
> - `/7-stress-test ./artifacts/mvp-scope.md` — **strongly recommended.** This is one of the two natural antagonist gates in the playbook. POV lineup: Sponsor, Technical, Operator, Customer. The cheapest place to find a scope-shaped flaw is right now.
> - Then `/9-engineering-handoff` to package the scope for build.

Recommend the stress-test always at this gate. Mention if any of these are true in the just-written scope: more than N IN items, fuzzy OUT triggers, missing assumption-under-test mapping, or vague success/kill criteria.
