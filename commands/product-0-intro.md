---
description: Orient the user in the product-playbook process — phases, principles, what to expect. No artifact written.
---

# /product-0-intro

You are orienting a builder who is about to walk through the product-playbook process. They may be solo, may have a small team, may be working with an AI team. Your job is to leave them knowing:

1. What the playbook is for
2. The 12 phases and what each produces
3. The 5 principles that govern the work
4. How to use the slash commands
5. What to do next

This command produces **no artifact**. It only orients.

---

## Step 1 — Read the user's current state

Before launching the orientation, check what's already in `./artifacts/`. If anything exists, note it. The orientation should mention it ("I see you already have a PRFAQ — we can resume at phase 3") rather than ignoring it.

If no `./artifacts/` directory exists yet, that's fine — they're starting fresh.

## Step 2 — Deliver the orientation

Walk through these five sections in order. Be concise. The whole thing should fit on a screen.

### What this playbook is for

A repeatable sequence for taking a product idea from "I think there's something here" to first paying customer and beyond. Sized for one or two builders plus an AI team. Not a heavyweight enterprise PDLC. Every phase produces a concrete artifact — if you don't have the artifact, the phase isn't done.

### The 12 phases

| # | Command | What you walk out with |
|---|---|---|
| 0 | `/product-0-intro` | (this) orientation |
| 1 | `/product-1-kickoff` | Durable principles + team toolchain + tech stack |
| 2 | `/product-2-vision` | PRFAQ + one-pager (PRFAQ first, then iterate with feedback, then the one-pager) |
| 3 | `/product-3-gtm` | Named first-three customers + GTM plan |
| 4 | `/product-4-design` | Design direction — user flows, IA, design language |
| 5 | `/product-5-mvp-scope` | Defensible MVP scope with numbers |
| 6 | `/product-6-roles` | Solution roles, user groups, permissions, operational features |
| 7 | `/product-7-stress-test` | Multi-POV antagonist review of any prior artifact |
| 8 | `/product-8-engineering-handoff` | Product requirements doc (`product-requirements.md`) for engineering |
| 9 | `/product-9-access-patterns` | Access patterns that will drive the data model |
| 10 | `/product-10-data-model` | Single-table design defended by access patterns |
| 11 | `/product-11-prebuild-qa` | The 13 pre-build questions answered |
| 12 | `/product-12-backlog` | Buildable backlog as local markdown — offers to push to your issue tracker if named |
| — | `/product-status` | Read the log; see where you are |

`/product-7-stress-test` runs at two natural gates — after `/product-2-vision` (PRFAQ) and after `/product-5-mvp-scope`. It can also run ad-hoc on any artifact.

### The 5 principles

These govern every phase. `/product-1-kickoff` will walk you through them and let you adopt, edit, or add.

1. **Artifacts > conversation.** Every phase produces a named file. If the file doesn't exist, the phase isn't done.
2. **Numbers > adjectives.** "$300/mo data budget, 3 metros, ship by July 15" resists drift. "Lean and focused" doesn't.
3. **Mock data is a schema spec.** Every field in the prototype maps 1:1 to a real entity at MVP. The cutover is mechanical when this holds.
4. **Antagonist gates are cheap.** A 30-minute multi-POV review at PRFAQ and MVP-scope is the highest-ROI time in the whole sequence.
5. **Surface assumptions, give an override.** When the playbook (or your product) picks a default, flag it as a guess and let the user override in two clicks.

### How the commands work

Each command does three things in order:
1. **Intros** the job to be done so you know why you're here
2. **Guides** you through the decisions that need to be made
3. **Processes** your responses into an artifact under `./artifacts/`

Every command appends a line to `./artifacts/product-log.md` with the timestamp (local PST) and the artifact produced. Run `/product-status` any time to see where you are.

### What to do next

If `./artifacts/` is empty: run `/product-1-kickoff` to capture your principles.

If `./artifacts/principles.md` exists: run `/product-status` to see where you left off, then continue from there.

## Step 3 — Do NOT write an artifact

This command produces no file. Do not create `./artifacts/intro.md` or anything similar. The orientation lives in this conversation.

## Step 4 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with a header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append a single line:

```
YYYY-MM-DD HH:MM PST — /product-0-intro — orientation delivered
```

Use the actual current local PST time. If you cannot determine the timezone, ask the user which timezone to use and remember it for future log entries.
