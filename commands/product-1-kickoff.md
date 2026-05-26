---
description: Capture durable principles, team toolchain, and tech stack. Writes ./artifacts/principles.md and ./artifacts/toolchain.md.
---

# /product-1-kickoff

You are helping the user lock in three things at the start of this product:

1. **Durable principles** — the non-negotiables they won't drift away from once the pressure is on.
2. **Team toolchain** — the vendor tools (issue tracker, design tool, docs, comms, etc.) the team already lives in. Downstream commands consult this to offer MCP-based integrations.
3. **Tech stack** — language, frontend, backend, database, infra, AI providers. Downstream commands (engineering handoff, data model) consult this to keep recommendations aligned.

Artifacts produced: `./artifacts/principles.md` and `./artifacts/toolchain.md`. Once written, future commands reference them (the antagonist holds scope decisions accountable to principles; the backlog command offers to push to GitHub/Jira/Linear if named in toolchain).

---

## Step 1 — Intro the job to be done

Open with this orientation (concise — the user wants to get to the decisions):

> **Kickoff** is where we lock three things at the start of this product: your **durable principles** (the rules you'll hold yourself to under pressure), your **team toolchain** (so later phases can offer to load artifacts into your tools), and your **tech stack** (so engineering recommendations stay aligned). Two artifacts: `./artifacts/principles.md` and `./artifacts/toolchain.md`. Both short, both worth re-reading later.

Then check: do `./artifacts/principles.md` or `./artifacts/toolchain.md` already exist? If yes, infer from the user's request what they want — if they said "redo my principles" or "starting over," proceed fresh; if they said "let's continue" or context implies resuming, open the file and walk through changes. **Ask only if the user's intent is genuinely unclear.** Never silently overwrite.

## Step 2 — Walk the 7 example principles

Present them one by one. For each, give the principle, what it means, and how it shows up in practice. Then ask three things: **adopt as-is, edit, or drop?**

The first 5 are the playbook's core. The last 2 (customer-obsession and truth-seeking) come from the Working Backwards / PRFAQ tradition and are especially useful if vision and PRFAQ work is ahead. Treat them as defaults to surface, not mandates.

### Principle 1 — Artifacts > conversation
**What it means:** Every phase produces a named file. If the file doesn't exist, the phase isn't done. Talking through it doesn't count.

**How it applies:** A "we discussed scope" message gets rejected. `./artifacts/mvp-scope.md` with numbers gets accepted. The artifact is the unit of progress.

**Why it matters:** Resists "we covered that in chat" drift three weeks later when nobody can find the decision.

→ Adopt as-is, edit, or drop?

### Principle 2 — Numbers > adjectives
**What it means:** Defensible commitments use numbers (dollars, counts, percentages, dates). "Focused" / "lean" / "soon" decay; "$300/mo data budget, 3 metros, ship by July 15" doesn't.

**How it applies:** Scope docs and pricing artifacts get rejected if they're adjective-only. The antagonist's job at the MVP-scope gate is to find every adjective and demand the number behind it.

**Why it matters:** Forces the actual constraint to surface. "We'll keep it focused" usually means "we haven't decided."

→ Adopt as-is, edit, or drop?

### Principle 3 — Mock data is a schema spec
**What it means:** Every field in the prototype's mock data maps 1:1 to a real entity at MVP. The prototype isn't a sketch — it's the spec. Cutover from mock → real is mechanical when this holds.

**How it applies:** Prototype fields and data-model fields are reconciled before engineering handoff. If the prototype shows `lead.score` but the data model has no `score`, one of them is wrong.

**Why it matters:** Kills the "prototype was just for show, we'll figure out the real schema later" failure mode.

→ Adopt as-is, edit, or drop?

### Principle 4 — Antagonist gates are cheap
**What it means:** A 30-minute multi-POV review at PRFAQ and MVP-scope is the highest-ROI time in the whole sequence. Finding a fatal flaw at PRFAQ costs hours; finding it post-launch costs the company.

**How it applies:** `/product-7-stress-test` is gated *into* the flow at phase 2 and phase 5. It can also run ad-hoc on any artifact.

**Why it matters:** Don't let people skip it because "we already talked through the risks." Talked-through ≠ POV-rotated.

→ Adopt as-is, edit, or drop?

### Principle 5 — Surface assumptions, give an override
**What it means:** When the playbook (or your product) picks a default — pricing tier, geo scope, source order — flag it explicitly as a guess and make it easy to change in one or two steps.

**How it applies:** Slash commands that pick defaults call them out: "Defaulting to PST; override?" / "Assuming 3 metros; change?" The user always sees what was decided *for* them.

**Why it matters:** Hidden defaults rot into load-bearing assumptions nobody remembers making.

→ Adopt as-is, edit, or drop?

### Principle 6 — Customer obsession (start from the pain, not the solution)
**What it means:** Every artifact begins from a real customer's actual pain in their actual words. The product is a means to relieve the pain, not a clever idea looking for a market.

**How it applies:** The PRFAQ's problem paragraph must be written in the customer's voice. If you can't name the customer, the segment, and quote a sentence of their frustration, the PRFAQ isn't ready. The antagonist will flag any solution that arrives before the problem is sharp.

**Why it matters:** This is the #1 thing Working Backwards / Amazon's PRFAQ tradition optimizes for. Products that drift away from a specific customer pain are products that get cut.

→ Adopt as-is, edit, or drop?

### Principle 7 — Truth-seeking over selling
**What it means:** Reviews exist to find what's wrong, not to get approval. The most valuable PRFAQ review surfaces the assumption that, if false, kills the idea — and that's a *good* outcome, not a setback.

**How it applies:** When you present a PRFAQ, MVP scope, or pricing model, frame the ask as "where am I wrong?" not "approve this." The `/product-7-stress-test` command is built around this: its job is to expose flaws while they're still cheap to fix.

**Why it matters:** Selling-mode reviews produce false confidence. Truth-seeking reviews produce products that survive contact with customers.

→ Adopt as-is, edit, or drop?

## Step 3 — Capture additions

Ask: **What other principles do you want to add?** These are the ones specific to *this* product or *this* team. Examples to prime if they're stuck:

- "No vendor lock-in without a written exit plan"
- "All AI outputs disputable by the user in two clicks"
- "Pricing changes require 30 days notice to existing customers"
- "Every customer-facing string is review-able by a non-engineer before shipping"

Don't force additions. If they say "the 5 are enough," that's a valid answer.

## Step 4 — Write the principles artifact

Create `./artifacts/principles.md`. Use this structure:

```markdown
# Principles

The non-negotiables for this product. Re-read before any scope decision, pricing decision, or shortcut.

## 1. <Principle name>
<One-line statement>

**Why:** <Why this matters>
**How it applies:** <Concrete behaviors this enables or prevents>

## 2. <Principle name>
...
```

Only include principles the user adopted (with their edits) plus any they added. Drop the ones they dropped. Do not include this file's commentary or the "Adopt/edit/drop?" prompts.

Confirm headings with the user before writing: "Lock this in?" If yes, write. If no, iterate.

## Step 5 — Capture the team toolchain

Now switch gears. Tell the user:

> Next, let's capture your team's toolchain. Downstream commands will offer to load artifacts into these tools — for example, the backlog command will offer to push items to GitHub Issues if that's where your team tracks work. Skip any category that doesn't apply yet. "TBD" is a valid answer.

Walk these categories. One short question each — don't drag it out. If the user has nothing for a category, mark it `(not yet)`.

- **Issue tracker / backlog** — GitHub Issues, Jira, Linear, Asana, Shortcut, none yet?
- **Project planning** — Asana, Linear, Aha, Jira, Notion, none?
- **Design** — Figma, Sketch, Claude Design, none?
- **Docs / wiki** — Confluence, Notion, Google Docs, none?
- **Comms** — Slack, Discord, MS Teams, email-only?
- **Email infra** — SES, Postmark, SendGrid, Resend, Gmail, none yet?
- **Customer support** — Zendesk, Intercom, HelpScout, none yet?
- **Source control** — GitHub, GitLab, Bitbucket?

## Step 6 — Capture the tech stack

Same approach — short questions, "TBD" is fine. Defaults vary by user; don't assume.

- **Primary language** — Python, TypeScript, Go, Ruby, other?
- **Frontend framework** — Next.js, Remix, SvelteKit, plain React, none yet?
- **Backend pattern** — serverless (Lambda), containers (ECS/k8s), traditional server, monolith?
- **Database** — DynamoDB, Postgres, MySQL, SQLite, MongoDB, undecided?
- **Infra-as-code** — CDK, Terraform, Pulumi, manual, undecided?
- **Cloud / hosting** — AWS, GCP, Vercel, Fly, Render, undecided?
- **AI providers** — Anthropic (Bedrock, direct), OpenAI, local models, none yet?
- **CI/CD** — GitHub Actions, CircleCI, GitLab CI, none yet?

## Step 7 — Write the toolchain artifact

Create `./artifacts/toolchain.md`:

```markdown
# Toolchain & stack

What this team uses today. Consulted by downstream commands when offering tool integrations or making stack-aligned recommendations.

## Team toolchain
- **Issue tracker:** <value or "(not yet)">
- **Project planning:** <value>
- **Design:** <value>
- **Docs / wiki:** <value>
- **Comms:** <value>
- **Email infra:** <value>
- **Customer support:** <value>
- **Source control:** <value>

## Tech stack
- **Language:** <value>
- **Frontend:** <value>
- **Backend pattern:** <value>
- **Database:** <value>
- **Infra-as-code:** <value>
- **Cloud / hosting:** <value>
- **AI providers:** <value>
- **CI/CD:** <value>

## Notes
<Any caveats — e.g., "evaluating Linear vs Jira", "stack decisions pending /product-9-engineering-handoff">
```

Confirm with the user before writing.

## Step 8 — Append to the log

Append two lines to `./artifacts/product-log.md`:

```
YYYY-MM-DD HH:MM PST — /product-1-kickoff — ./artifacts/principles.md
YYYY-MM-DD HH:MM PST — /product-1-kickoff — ./artifacts/toolchain.md
```

Use the actual current local PST time. Create the log file with a header if it doesn't exist yet (see `/product-0-intro` for the header format).

## Step 9 — Tell them what's next

Close with: "Next: `/product-2-vision` to draft the PRFAQ."
