---
description: Read-only status check. Reports where the product is in the playbook, what artifacts exist, what the log says happened, and what's next. Writes nothing.
---

# /product-status

You are giving the user a read-only snapshot of their product's progress through the playbook. **This command produces no artifact and appends nothing to the log.** It only reads and reports.

The job: answer "where am I?" in 10–20 seconds.

---

## Step 1 — Read the log

Read `./artifacts/product-log.md` if it exists.

- If missing: tell the user "No `./artifacts/product-log.md` found — this product hasn't started the playbook yet. Run `/product-0-intro` to orient, then `/product-1-kickoff` to begin." Stop here.
- If present: parse each line. Lines look like `YYYY-MM-DD HH:MM <TZ> — /product-N-name — <artifact path or note>`.

## Step 2 — Inventory the artifacts directory

List `./artifacts/` (one level deep, plus relevant subdirectories: `design-seed/`, `design/`, `backlog/`). Match each file against the expected outputs of each phase.

| Phase | Expected artifact(s) |
|---|---|
| 1 | `principles.md`, `toolchain.md` |
| 2 | `PRFAQ.md`, `one-pager.md` |
| 3 | `gtm-plan.md` |
| 4 | `design-seed/` (seed step), `design/index.md` (catalog step) |
| 5 | `mvp-scope.md` |
| 6 | `roles.md` |
| 7 | `stress-test-*.md` (any) |
| 8 | `go-no-go.md` |
| 9 | `product-requirements.md` |
| 10 | `access-patterns.md` |
| 11 | `data-model.md` |
| 12 | `prebuild-qa.md` |
| 13 | `backlog/index.md` + item files |

Note any artifact present without a corresponding log entry, and any log entry whose artifact is missing. Both are mild signals — surface them but don't gate on them.

## Step 3 — Report

Print a concise status block. Default shape:

```
# Product status

**Last log entry:** 2026-05-14 09:32 PST — /product-5-mvp-scope — ./artifacts/mvp-scope.md

## Phases completed
- ✅ /product-1-kickoff — principles.md, toolchain.md
- ✅ /product-2-vision — PRFAQ.md, one-pager.md
- ✅ /product-3-gtm — gtm-plan.md
- ✅ /product-5-mvp-scope — mvp-scope.md
- ⏭️  /product-4-design — skipped (no artifact)
- ⏳ /product-7-stress-test — not yet run on mvp-scope.md (recommended gate)

## Phases remaining (in order)
- /product-7-stress-test (gate)
- /product-6-roles
- /product-9-engineering-handoff
- /product-10-access-patterns
- /product-11-data-model
- /product-12-prebuild-qa
- /product-13-backlog

## Recommended next
Run `/product-7-stress-test ./artifacts/mvp-scope.md` — this is the natural antagonist gate after MVP scope is locked. POV lineup: Sponsor, Technical, Operator, Customer.

## Anomalies
- `./artifacts/scratch.md` exists but isn't a playbook artifact (probably fine, just noting).
```

Adapt for the actual state. Guidance:

- **Last log entry** — newest line from the log, verbatim.
- **Phases completed** — one row per phase that has either a log entry or the expected artifact(s). Use ✅ when both, ⚠️ when artifact exists without log entry (or vice versa).
- **Phases skipped vs not-yet-reached** — distinguish them. A phase is *skipped* if later phases have completed but it hasn't; *not yet reached* if it's just ahead in the natural order.
- **Stress-test gates** — call out explicitly:
  - After `/product-2-vision`: recommend `/product-7-stress-test ./artifacts/PRFAQ.md`
  - After `/product-5-mvp-scope`: recommend `/product-7-stress-test ./artifacts/mvp-scope.md`
  - If a gate hasn't been crossed, mark ⏳ and surface in "recommended next" if the user is at the gate.
- **Go/no-go gate** — after the MVP-scope stress test, the user must run `/product-8-go-no-go` before engineering handoff. If `stress-test-mvp-scope.md` exists but `go-no-go.md` doesn't, recommend it. If `go-no-go.md` exists, surface the decision verbatim (Go / Go-with-changes / Pause / Kill) in the status block — a Pause or Kill changes the recommended next step.
- **Phases remaining** — in playbook order, only the ones that aren't done.
- **Recommended next** — single concrete command with one short sentence of justification. Pick based on:
  - If a stress-test gate is open → run it.
  - If MVP stress-test is done but no go/no-go yet → recommend `/product-8-go-no-go`.
  - If go/no-go = Pause → tell the user what signal they're waiting for (from go-no-go.md) and stop recommending phase progression.
  - If go/no-go = Kill → tell the user the project is closed; suggest archiving `./artifacts/`.
  - Otherwise → the next phase in playbook order.
  - If everything is done → "Pick item 001 from `./artifacts/backlog/index.md` and start building."
- **Anomalies** — only include the section if there's something to flag (stray files, missing artifacts for logged phases, etc.). Omit the section header otherwise.

## Step 4 — Do NOT write anything

No file is created or modified. No log entry is appended. `/product-status` is a pure read.

If the user wants to record that they checked status, that's a memory-write decision they can make explicitly. Don't auto-log.
