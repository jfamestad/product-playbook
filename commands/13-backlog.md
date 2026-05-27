---
description: Turn the resolved upstream artifacts into a buildable backlog of work items as local markdown. Then offer to push to the team's issue tracker (GitHub Issues, Jira, Linear, Asana) if named in toolchain.md and the vendor MCP is available. Writes ./artifacts/backlog/.
---

# /13-backlog

You are converting the resolved upstream artifacts into a **buildable backlog** — discrete work items a developer (human or AI) can pick up and execute without re-deriving context. The backlog is the bridge from "we know what we're building" to "we're building it."

The backlog lives as local markdown under `./artifacts/backlog/` — one file per work item plus an index. That's canonical. If the team uses a vendor issue tracker, this command then offers to push the items into it via the vendor's MCP (additive sync; local markdown remains source of truth).

---

## Step 1 — Intro the job to be done

Open with:

> **Backlog** turns the upstream artifacts (MVP scope, product requirements, data model, prebuild-qa) into discrete work items a developer can pick up and execute. The shape: one markdown file per item under `./artifacts/backlog/`, plus an index. Each item has a summary, acceptance criteria, dependencies, and a size. After we write the local backlog, I'll check your toolchain — if you track work in GitHub Issues / Jira / Linear / Asana and the MCP for it is available, I'll offer to push the items into it. The local markdown stays canonical either way.

## Step 2 — Read context

Required:
- `./artifacts/mvp-scope.md` — what's IN
- `./artifacts/product-requirements.md` — the requirements doc from `/9-engineering-handoff`
- `./artifacts/prebuild-qa.md` — the 13 questions answered

Helpful:
- `./artifacts/roles.md` — drives auth/tenancy/permissions items
- `./artifacts/data-model.md` and `./artifacts/access-patterns.md` — drives schema items
- `./artifacts/design/index.md` — drives UI items
- `./artifacts/principles.md` — keep alignment
- `./artifacts/toolchain.md` — drives the integration offer in Step 7

If any required file is missing, stop and tell the user which upstream command to run. Don't fabricate a backlog from thin air.

If `./artifacts/backlog/` already exists, infer intent: continue iterating (add/edit items) or start fresh (archive then rebuild)? Ask only if unclear.

## Step 3 — Decompose into work items

Walk the requirements doc and turn each into one or more work items. Useful buckets to organize by:

- **Infrastructure & setup** — repo init, CI/CD, env scaffolding, secrets, IaC stack
- **Data layer** — table(s), indexes, seed data, access-pattern helpers
- **Auth & tenancy** — sign-on, org/tenant model, permission middleware
- **Core features** — one item per feature/flow from the requirements doc
- **Operational** — admin views, observability, monitoring, on-call runbook
- **Customer-facing polish** — onboarding, empty states, error pages, docs
- **Legal / compliance** — ToS, pilot agreement, privacy basics

A good work item is:
- **Independently completable** — has a clear "done" without waiting on a half-built sibling
- **Bounded** — fits in a single PR (or a short series). XL items get split.
- **Testable** — acceptance criteria are mechanical, not aspirational

Discuss the proposed list with the user before writing. They should be able to point at any item and say "this one's first" or "this one we don't need yet."

## Step 4 — Size and order

For each item, set:

- **Size:** S (≤ half day) / M (1–2 days) / L (3–5 days) / XL (split it)
- **Priority:** P0 (blocks MVP) / P1 (in MVP) / P2 (post-MVP nice-to-have)
- **Depends on:** other item slugs that must complete first

Then propose an order. Default heuristic: infrastructure → data layer → auth → first end-to-end feature (vertical slice) → remaining features → operational → polish. Surface the order as an explicit list and let the user reorder.

## Step 5 — Write the local backlog

Create `./artifacts/backlog/` with this shape:

```
./artifacts/backlog/
├── index.md                       # ordered list of all items, with status
├── 001-<slug>.md                  # one file per item, zero-padded number
├── 002-<slug>.md
└── ...
```

**index.md** format:

```markdown
# Backlog

Local canonical backlog for <product name>. Each row links to a detailed item file. Status is tracked here; details (acceptance criteria, decisions, references) live in the item file.

## Order

| # | Item | Bucket | Size | Priority | Status | Depends on |
|---|---|---|---|---|---|---|
| 001 | [Init repo + CI](001-init-repo-ci.md) | Infra | S | P0 | todo | — |
| 002 | [DynamoDB single table](002-dynamodb-single-table.md) | Data | M | P0 | todo | 001 |
| ... |

## Buckets
- Infra & setup: 001, 002, 005
- Data layer: 003, 004
- ...

## Notes
- Items 001–015 = MVP scope
- Items 016+ = post-MVP backlog
```

**Each item file** format:

```markdown
# 001 — Init repo + CI

**Bucket:** Infrastructure & setup
**Size:** S
**Priority:** P0
**Depends on:** —
**Status:** todo

## Summary
<One paragraph: what gets built, why it's a discrete item>

## Inputs / Outputs
- **Inputs:** <upstream artifacts or other items>
- **Outputs:** <what exists in the repo when this is done>

## Acceptance criteria
- [ ] <mechanical, checkable>
- [ ] <one per criterion>

## Open decisions
- <Any unresolved choices the implementer needs the user to decide first. None? Say "None.">

## Notes / references
- <links to relevant requirements sections, ADRs, design files>
```

Write all items. Confirm the count and structure with the user before moving on.

## Step 6 — Append to the log

Append to `./artifacts/product-log.md`:

```
YYYY-MM-DD HH:MM PST — /13-backlog — ./artifacts/backlog/ (N items)
```

Use the actual current local PST time. Create the log file with a header if it doesn't exist yet.

## Step 7 — Offer toolchain integration (this is the convention in action)

Read `./artifacts/toolchain.md` if it exists. Look at the **Issue tracker** value.

| Tool named | MCP available in session | Action |
|---|---|---|
| GitHub Issues | `gh` CLI or GitHub MCP | Offer to create issues in the repo (ask for owner/repo) |
| Jira | Atlassian MCP | Offer to create issues in the project (ask for project key) |
| Linear | Linear MCP | Offer to create issues in the team (ask for team) |
| Asana | Asana MCP | Offer to create tasks in the project (ask for project) |
| Notion | Notion MCP | Offer to create a database with one row per item |
| Anything else | Vendor MCP if available | Offer the equivalent push |
| Nothing / "(not yet)" | n/a | Tell them: "No issue tracker named in toolchain.md — we'll stay local. Re-run this step any time after you pick one." |
| Named but MCP unavailable | — | Tell them which MCP would be needed; offer to skip |

If the user accepts the push:

1. Confirm the destination (repo, project, team, etc.).
2. For each item in order, create the corresponding issue/task. Include the full body from the item markdown.
3. Add a `tracker_url:` line to the YAML/header of each local item file as it's pushed, so future re-syncs know what's already there.
4. Update `./artifacts/backlog/index.md` with a final "Pushed to: <tool> on <date>" line.
5. Append another log line: `YYYY-MM-DD HH:MM PST — /13-backlog — pushed N items to <tool>`.

**Rule:** the local markdown is canonical. Future edits happen locally first, then re-push. Never edit the tracker as the source of truth.

If the user declines or no tool is named, skip — that's fine. The local backlog is enough to ship from.

## Step 8 — Tell them what's next

Close with the appropriate next step:

- If MVP backlog is set and they're ready to build: "You're ready. Pick item 001 and start. Re-run `/status` any time to check progress."
- If they want a stress test on the backlog: "Want me to run `/7-stress-test` on the backlog before you start? Cheap insurance."
- If the toolchain push needs to be re-run later (e.g., they haven't picked a tracker yet): "When you pick an issue tracker, update `./artifacts/toolchain.md` and re-run `/13-backlog` — it'll skip the local writes and go straight to the push."
