---
description: Build the real APIs and replace the mock data layer entity by entity. Validation gate per entity against the mock baseline. Writes ./artifacts/api-build.md.
---

# /15-api-build

You are replacing the mock data layer behind the UI built in `/14-ui-build` with **real APIs**, entity by entity. The UI itself doesn't change — that's the whole point. Mock data was specified 1:1 with `./artifacts/data-model.md`; the API responses must match that same shape. The cutover is a fetch-call swap and a flag flip, not a redesign.

The goal of this phase: MVP is live on real data with the same UI the named first-three have already been using.

This honors Principle 3 (mock data is a schema spec — payoff happens here) and protects against schema drift, partial-cutover state, and "it worked on mock but not on real."

---

## Step 1 — Intro the job to be done

Open with:

> **API build** swaps the mock data behind the UI for real APIs, entity by entity. UI doesn't change — the mock was specified 1:1 with `data-model.md`, so each cutover is a fetch swap and a flag flip. We'll pick the wire-in order (smallest/lowest-risk first), define a validation gate per entity (real responses must match the mock shape exactly), and plan rollback per step. Output: `./artifacts/api-build.md`.

## Step 2 — Read context

Required:
- `./artifacts/ui-build.md` — what's wired vs stubbed, and where the mock data sits
- `./artifacts/data-model.md` — entity shapes the APIs must return
- `./artifacts/access-patterns.md` — drives which endpoints exist and which keys they take
- `./artifacts/toolchain.md` — tech stack for the API layer

Helpful:
- `./artifacts/roles.md` — auth, tenancy, permission rules the APIs enforce
- `./artifacts/product-requirements.md` — acceptance criteria per feature
- `./artifacts/backlog/` — API-tagged items already enumerated
- `./artifacts/mvp-scope.md` §7.2 — instrumentation still has to fire after cutover

If any required file is missing, stop and tell the user which upstream command to run. In particular, `/14-ui-build` must have shipped — there's no mock layer to replace if the UI isn't real yet.

If `./artifacts/api-build.md` already exists, infer intent: continue the cutover (next entity) or restart? Ask only if unclear. Never silently overwrite.

## Step 3 — Pick wire-in order

List every entity from `ui-build.md` that currently reads/writes mock data. For each, score:

- **Risk:** how bad if the cutover breaks? (a logged-out marketing page < a primary user workflow < a billing event)
- **Surface area:** how many routes / handlers touch this entity?
- **Dependencies:** does another entity's cutover have to land first?
- **External integrations:** does this API call out to a third party (payment, email, AI provider)?

Order smallest-risk × smallest-surface first. Get a quick win, validate the cutover ceremony, then move to higher-stakes entities.

A typical order:
1. Read-only reference data (catalog, lookup tables) — pure SELECT, no writes
2. User-owned read data (their dashboard, their projects list)
3. User-owned write paths (create/update/delete on their own data)
4. Cross-tenant / admin / billing paths — last, because the blast radius is highest

Discuss the order with the user before writing the artifact.

## Step 4 — Per-entity plan

For each entity, capture:

- **Endpoint(s):** path, method, auth requirement, response shape, error shape
- **Schema match:** confirm response shape is identical to the mock — same field names, same types, same nullability. If something has to change, that's a UI change too — flag it.
- **Access patterns covered:** which patterns from `access-patterns.md` does this endpoint serve
- **Validation gate:** what specific check passes before we flip the flag? Examples: "all 12 mock records have a corresponding real record returned by `GET /projects`," "diff of mock response vs real response is empty for the canonical fixture user."
- **Feature flag / toggle:** how the swap happens — env var, runtime flag, client-side toggle, build flag. Pick whichever fits the framework.
- **Rollback:** how to revert if the cutover fails post-flip (flip the flag back? deploy a previous build?) — and how fast.
- **Instrumentation parity:** the signals defined in `/5-mvp-scope` §7.2 still have to fire. Confirm the API path preserves them.

Discuss the plan with the user before writing.

## Step 5 — Cutover ceremony per entity

Document the repeatable sequence so the user (or AI team) knows exactly what to do per entity:

1. **Build the endpoint** against `data-model.md` and `access-patterns.md`
2. **Seed real data** matching the mock fixtures so the first-three see continuity
3. **Run the validation gate** — real response must match the mock baseline for the fixture user
4. **Flip the flag** for a single user or environment first (dev → staging → first-three → all)
5. **Watch the instrumentation** — signal volume should match what the mock layer was producing; deviation is a smoke signal
6. **Hold for N minutes/hours of normal usage**, then promote the flag to the next ring
7. **Remove the mock for this entity** once it's been on real for a full first-three usage cycle (default: 1 week)

If anything in steps 3–6 fails, **roll back to the mock immediately**, capture what broke, fix, retry. The mock isn't deleted until the entity has been on real long enough to trust.

## Step 6 — Write the artifact

Path: `./artifacts/api-build.md`

```markdown
# API build

*Phase started: YYYY-MM-DD*

## Wire-in order

| Order | Entity | Risk | Surface | Depends on | Status |
|---|---|---|---|---|---|
| 1 | <entity> | low/med/high | <N routes> | <prior entities or none> | planned / in-progress / live / rolled-back |

## Per-entity plan

### Entity: <Name>
- **Endpoint(s):** <path, method, auth>
- **Response shape:** matches mock (link to mock spec in ui-build.md §Mock dataset)
- **Access patterns covered:** <list from access-patterns.md>
- **Validation gate:** <specific check>
- **Feature flag:** <name + how to flip>
- **Rollback:** <how + how fast>
- **Instrumentation parity:** <events preserved>

### Entity: <Name>
...

## Cutover ceremony (applied to each entity)

1. Build endpoint
2. Seed real data matching mock fixtures
3. Run validation gate
4. Flip flag for first ring
5. Watch instrumentation
6. Hold then promote
7. Remove mock after one full first-three cycle on real

## Status log

| Date | Entity | Action | Outcome |
|---|---|---|---|
| YYYY-MM-DD | ... | flag flipped to staging | OK / rolled back: <reason> |

## What's still on mock

- <Entity> — <why deferred>

## What's live on real

- <Entity> — since <date>

## Open questions

- <Any cutover decisions still open>
```

Confirm the structure with the user before writing — especially the wire-in order and the first entity's validation gate. Then write.

## Step 7 — Append to the log

Append to `./artifacts/product-log.md`:

```
YYYY-MM-DD HH:MM PST — /15-api-build — ./artifacts/api-build.md
```

Use the actual current local PST time. Create the log file with a header if it doesn't exist yet.

Subsequent runs (each entity's cutover) append an additional line:

```
YYYY-MM-DD HH:MM PST — /15-api-build — <entity> live on real (or rolled back: <reason>)
```

## Step 8 — Offer to run the dev server with the flag

Detect the dev command the same way `/14-ui-build` did (`package.json` scripts, Makefile, etc.). Offer:

> Want me to start the dev server with the feature flag set so you can click through and confirm the swap looks identical to the mock? I'll run `<command>` with the flag, give you the URL, and you can verify behavior matches before we promote.

If the user accepts, start the process, give the URL, capture observations. Roll forward or roll back based on what they see.

If the user declines, proceed to Step 9. Note in the status log.

## Step 9 — Offer toolchain integration

Read `./artifacts/toolchain.md`. Likely destinations:

| Tool named | Action |
|---|---|
| Confluence / Notion / Google Docs | Offer to push `api-build.md` as a page |
| Issue tracker (any) | Offer to update API backlog items as they go live |
| GitHub / GitLab | Offer to open a PR for the entity's cutover commits |
| API gateway / observability dashboard | Offer to add the new endpoints to monitoring dashboards if a relevant MCP is available |

The push only fires if the tool is named AND its MCP is available. Otherwise stay local.

## Step 10 — Tell them what's next

Branch on cutover state:

- **First entity live and stable for ≥ 1 day on first-three:** Continue the wire-in order — re-run this command for the next entity. Each run updates the status log.
- **All entities live on real:** The MVP is fully real-backed. Recommend a final `/7-stress-test ./artifacts/api-build.md` to look for operational gaps (rate limits, error handling, runbook completeness) before broader rollout. Then move toward first paying customer.
- **A cutover rolled back:** Don't proceed to the next entity. Fix the issue, retry, then move forward. The status log captures the lesson.
- **UI signals from `/14-ui-build` indicate the underlying workflow is wrong:** Stop the API cutover. Go back to `/14-ui-build` (or earlier — `/5-mvp-scope` if the IN list itself was wrong). Don't pour API engineering into a workflow that's already broken.

Close with one concrete recommendation.
