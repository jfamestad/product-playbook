---
description: Make the explicit go/no-go decision to commit to building (or pivot, or kill) after the MVP-scope stress test. Forces a conscious commitment with named decider, dated decision, and pre-build commitments. Writes ./artifacts/go-no-go.md.
---

# /8-go-no-go

You are walking the user through the **go/no-go decision** — the explicit, dated, named-decider commitment to build (or not build) what's been specified.

The trap this phase prevents: drift from "the stress test surfaced some flaws" into "we started building" without anyone actually deciding. The stress test produces a recommendation (Proceed / Proceed-with-changes / Pause / Kill). The go/no-go is the **user's decision** to accept (or override) that recommendation and make it binding.

This is not a meeting summary. It's a commitment artifact. After this command, engineering can start with confidence that the upstream work is locked.

---

## Step 1 — Intro the job to be done

Open with:

> **Go/no-go** is the conscious commitment to build what we've specified — or to pivot, or to kill. The stress test surfaced flaws; this phase decides what to do with them. Output is a dated, named-decider commitment artifact. After this, engineering picks up the requirements doc with no ambiguity about whether we're actually doing this.
>
> Four outcomes possible: **Go**, **Go with changes** (specific cuts/additions), **Pause** (named blocker, named trigger to resume), **Kill** (what we learned, why we're stopping).
>
> Output: `./artifacts/go-no-go.md`.

## Step 2 — Read context

Required:
- `./artifacts/mvp-scope.md` — what we're committing to (or not)
- `./artifacts/stress-test-mvp-scope.md` — the antagonist's findings on that scope

If `stress-test-mvp-scope.md` is missing, **block**: making a go/no-go decision without a stress test is exactly the failure mode this phase prevents. Recommend `/7-stress-test ./artifacts/mvp-scope.md` first.

Strongly recommended:
- `./artifacts/stress-test-PRFAQ.md` — the gate-1 findings (so we're not silently re-litigating vision-stage flaws)
- `./artifacts/gtm-plan.md` — the named first-three and kill criteria
- `./artifacts/principles.md` — the bar to hold ourselves to
- `./artifacts/roles.md` — operational scope
- Any other stress-test files (e.g., `stress-test-PRFAQ.md`, `stress-test-gtm-plan.md`)

Optional:
- `./artifacts/toolchain.md` — for the publish step
- `./artifacts/one-pager.md` — for the value claim being committed to

If `./artifacts/go-no-go.md` exists, this is a re-decision (rare but valid — e.g., evidence shifted). Confirm with the user whether they want to amend, supersede, or start fresh. Never silently overwrite a prior commitment.

## Step 3 — Review the stress-test findings

Open `stress-test-mvp-scope.md`. Walk through it with the user, one section at a time:

1. **The antagonist's recommendation** — was it Proceed / Proceed-with-changes / Pause / Kill?
2. **Critical flaws raised** — for each: was it addressed in a subsequent scope edit, accepted-with-mitigation, or accepted-unmitigated?
3. **Load-bearing assumptions named** — for each: is the assumption documented as "we are betting on this", or has the user added a test to validate it during MVP?
4. **One sharp question per POV** — has each been answered, or explicitly noted as deferred?

For every flaw or assumption, the user must land in one of three states:
- **Resolved** — the scope was edited; the flaw no longer applies.
- **Accepted with mitigation** — the flaw stands; here's the mitigation (cheap fallback, manual workaround, monitoring trigger).
- **Accepted unmitigated** — the flaw stands; we're choosing to live with it; here's why the risk is acceptable.

"We'll figure it out" is not a state. If the user can't pick one of the three, the scope isn't ready and go/no-go is premature — recommend another `/5-mvp-scope` pass or another `/7-stress-test`.

## Step 4 — Make the decision

Ask the user directly: **Given everything above, what's the call?**

Four options:

### Option A — Go
Build it as specified. The scope is locked. Engineering picks up `product-requirements.md` next (`/9-engineering-handoff`).

### Option B — Go with changes
Build it, but with these specific changes from `mvp-scope.md`:
- Items added to IN: <list>
- Items moved to OUT/deferred: <list>
- Numbers revised: <list with before → after>
- Constraints relaxed/tightened: <list>

The amended scope must be re-written into `mvp-scope.md` (or appended as an addendum) **before** engineering handoff. Note in the go/no-go artifact that this happened.

### Option C — Pause
Don't build yet. Capture:
- **What's blocking** — the specific unknown that has to resolve before we'd commit.
- **What signal would unblock** — concrete event, date, or data point.
- **Who owns watching for it** — named human.
- **When we re-evaluate** — date or trigger.

Pause is a real, valid outcome. It is not "Kill" with extra steps. It is also not "Go but slow" — a Pause means no code starts on this scope until the named signal arrives.

### Option D — Kill
Stop. Don't build this. Capture:
- **What we learned** — the specific insight from the upstream work that made us pull the plug.
- **What was right about the original thesis** — partial credit matters; future-you may revisit.
- **What we'd do differently next time** — meta-feedback for the next product.
- **What happens to the team** — pivot to adjacent idea, return to discovery, etc.

Kill is the most valuable outcome the playbook can produce on a bad idea — the artifact captures why so the lesson isn't lost.

## Step 5 — Identify the decider

Every go/no-go has **one named human** accountable for the call. Not "the team." Not "the founders." One name.

- Who is it?
- What gives them the authority? (e.g., "Founder / sole funder," "Product lead with sign-off from CEO," "Pilot customer's check is what makes it real.")
- Are there any others who need to **co-sign** (not decide — co-sign)? Capture them separately.

In a solo-builder context, this is almost always the user themself. Capture it anyway. Future-you will want to know who made the call.

## Step 6 — Pre-build commitments

Before engineering starts, lock these commitments. They're cheap to set now and brutal to negotiate mid-build.

- **Time budget:** date we ship by, or weeks of effort budgeted. Pulls from `mvp-scope.md` — confirm or revise.
- **Time-to-first-user-touch:** target date for the first slice in a named first-three's hands — even if it's rough, internal-only, or behind a flag. Default aggressively: days, not weeks. With an AI team, "we're still building" past week 2 usually means the slice is too thick. If this date slips, that's a re-evaluation trigger.
- **Money budget:** total $ at risk for the MVP build. Pulls from `mvp-scope.md` — confirm or revise.
- **Scope-creep rule:** what's our policy when someone wants to add a feature mid-build? E.g., "any addition trades 1:1 with an IN item moving to OUT" or "no additions until pilot 1 ships."
- **Re-evaluation triggers during build:** what events force us to revisit go/no-go before MVP ships? E.g., "if we're > N% over time budget at week M," "if cost-to-build estimate jumps > X%," "if time-to-first-user-touch slips by more than a week."
- **Kill triggers during build:** the lines we won't cross. E.g., "if integration with source Y proves impossible," "if first beta customer pulls out before week N."

## Step 7 — Write the artifact

Create `./artifacts/go-no-go.md`. Use this structure:

```markdown
# Go/No-Go Decision — MVP

**Decision:** Go | Go with changes | Pause | Kill
**Date:** YYYY-MM-DD
**Decider:** <named human>
**Co-signers (if any):** <names>

## What we're committing to (or not)
<One-paragraph summary of the MVP scope being decided on. Reference mvp-scope.md.>

## Stress-test resolution
The MVP-scope stress test surfaced N critical flaws and M load-bearing assumptions. Resolution:

| # | Flaw / Assumption | Status | Notes |
|---|---|---|---|
| 1 | <flaw> | resolved / accepted-with-mitigation / accepted-unmitigated | <how / why> |
| 2 | ... |

## Decision rationale
<3–5 sentences. Why this call, given the evidence. Specifically: what about the resolved-flaw set + the accepted-unmitigated set makes Go (or Pause, or Kill) the right answer right now?>

## If Go / Go with changes — scope addendum
<Only present if Option B. List exact changes from the original mvp-scope.md. If a re-write of mvp-scope.md is needed, note that here and confirm it's been done.>

## Pre-build commitments
- **Time budget:** <date or weeks>
- **Time-to-first-user-touch:** <date — first slice into a named user's hands, even rough>
- **Money budget:** $<total>
- **Scope-creep rule:** <policy>
- **Re-evaluation triggers during build:** <list>
- **Kill triggers during build:** <list>

## If Pause — resume conditions
<Only present if Option C. What signal we're waiting for, who owns watching, when we re-evaluate.>

## If Kill — lessons
<Only present if Option D. What we learned, what was right, what we'd do differently, what's next for the team.>

## References
- `./artifacts/mvp-scope.md` (scope being committed to)
- `./artifacts/stress-test-mvp-scope.md` (gate-2 findings)
- `./artifacts/stress-test-PRFAQ.md` (gate-1 findings, if exists)
- `./artifacts/gtm-plan.md` (kill criteria referenced)
- `./artifacts/principles.md` (commitments tested against)
```

Read the final draft back to the user in summary form before writing — especially the decision, the decider, the rationale, and the pre-build commitments. Confirm. Then write the file.

## Step 8 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with this header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append:

```
YYYY-MM-DD HH:MM PST — /8-go-no-go — ./artifacts/go-no-go.md — <decision>
```

Include the decision (Go / Go-with-changes / Pause / Kill) inline in the log line so `/status` can surface it without re-opening the file.

Use the actual current local PST time.

## Step 9 — Offer toolchain integration

Read `./artifacts/toolchain.md` if it exists. Two destinations are relevant here:

- **Docs / wiki** (Confluence, Notion, Google Docs) → the go/no-go artifact is a high-signal decision record; it belongs next to other product docs.
- **Comms** (Slack, MS Teams, email) → if a decider made a Go call, the team may want a notification. Offer to draft a short announcement.

If a relevant tool is named AND its MCP is available, offer the corresponding push. Local markdown remains source of truth.

If no tool is named, "(not yet)", or MCP unavailable, skip silently.

See the playbook SKILL's "Toolchain integration" convention for the full pattern.

## Step 10 — Tell them what's next

Branch on the decision:

**If Go or Go with changes:**

> Decision locked. Next:
> - `/9-engineering-handoff` — package scope + roles + go/no-go commitments into the product requirements doc.
> - If Option B (Go with changes), confirm `./artifacts/mvp-scope.md` has been updated to reflect the changes — engineering reads scope, not this file.

**If Pause:**

> Decision locked. No engineering work starts until <named signal> arrives. Owner: <name>. Re-evaluate by: <date or trigger>.
>
> While paused, you can still:
> - Run `/7-stress-test` on any other artifact (the GTM plan often benefits)
> - Sharpen the named signal — what's the cheapest experiment that would produce it?

**If Kill:**

> Decision locked. The go/no-go artifact captures what we learned.
>
> Next:
> - Archive `./artifacts/` (move to `./artifacts-archive/<product-name>-killed-<date>/` so the next product starts clean)
> - Optionally run `/0-intro` on the next idea
> - Re-read the lessons section before naming the next first-three
