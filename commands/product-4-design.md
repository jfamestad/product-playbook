---
description: Seed Claude Design with the project's PRFAQ + GTM + principles as a verbatim-quoted brief, then integrate the handoff bundle back into the project. Writes ./artifacts/design-seed/ and (after Claude Design returns results) ./artifacts/design/index.md.
---

# /product-4-design

You are helping the user produce the design direction for the product. The actual design work happens in **Claude Design** ([claude.ai/design](https://claude.ai/design)) — an Anthropic product that takes prompts + files and produces interactive HTML mockups, flows, and design language, then ships a **handoff bundle** to Claude Code for integration.

This skill has two jobs:

1. **Seed Claude Design well** — produce `./artifacts/design-seed/` containing verbatim quotes from upstream artifacts with commentary on how each fits the design problem. Don't paraphrase the customer's voice; don't lose the source links. Claude Design works best when fed sharp inputs, not summaries of summaries.
2. **Integrate the results back** — once the user is satisfied in Claude Design and ships the handoff bundle to Claude Code, help catalog what arrived in `./artifacts/design/`, capture key decisions, and surface open questions.

**Iteration is the user's call.** Claude Design supports inline comments, direct edits, and adjustment knobs (spacing/color/layout). The user iterates until they feel ready — there's no "right" number of rounds. This skill helps them start strong and finish cleanly.

---

## Step 1 — Intro the job to be done

Open with this orientation:

> **Design** is where we turn the validated vision and GTM into concrete design direction. The actual design work happens in Claude Design ([claude.ai/design](https://claude.ai/design)) — Anthropic's design product. This skill does two things:
>
> 1. Builds the **seed package** (`./artifacts/design-seed/`) — verbatim quotes from your PRFAQ, GTM plan, and principles with commentary, plus a brief telling Claude Design what to focus on.
> 2. After you iterate in Claude Design and ship the handoff bundle to Claude Code, helps you **catalog the results** in `./artifacts/design/` so the design is a first-class artifact alongside the others.
>
> You decide when to stop iterating. Claude Design is interactive — inline comments, direct edits, adjustment knobs. Iterate until you're ready, then come back here to integrate.
>
> Outputs: `./artifacts/design-seed/` (now) and `./artifacts/design/index.md` (after integration).

## Step 2 — Read context

Required upstream artifacts:
- `./artifacts/PRFAQ.md` — the narrative, problem in customer voice, customer quote, getting-started
- `./artifacts/gtm-plan.md` — ICP, top-3 customer situations, pricing/unit-of-value

If either is missing, **block**: a design brief without a validated customer and validated narrative produces fiction. Recommend `/product-2-vision` then `/product-3-gtm` first.

Strongly recommended:
- `./artifacts/principles.md` — any principles that bind design choices (especially customer-obsession, surface-assumptions-give-override)
- `./artifacts/one-pager.md` — condensed value statement, useful as a one-screen anchor for visual direction

Optional:
- `./artifacts/stress-tests/PRFAQ-*.md` — any open Customer or Product POV concerns that design should resolve

If `./artifacts/design-seed/` already exists, infer from the user's request whether to continue iterating or start fresh. Ask only if intent is genuinely unclear. Never silently overwrite.

If `./artifacts/design/` already exists with results from a prior integration, ask before adding new entries vs. starting a fresh integration log.

## Step 3 — Walk the design brief decisions

Before extracting quotes, lock the design-specific decisions. Walk through with the user. Numbers and specifics where possible.

### 3.1 Primary user
One sentence — pulled forward from the ICP, but sharpened for the design lens. Who is at the keyboard? What's their context (laptop at desk? phone between meetings? tablet on the road?).

### 3.2 Primary flows (3–7)
List the user-facing flows the design must cover. Each one named in user-language, not feature-language. Example:
- "Sign up and connect first data source"
- "Run a workflow and see the result"
- "Give feedback on a result"
- "Invite a teammate"

Don't enumerate every screen — name the *flows*. Claude Design will compose the screens.

### 3.3 Devices & form factor
- Web desktop? Mobile web? Native app? Tablet? All?
- If "all," call out the primary — the one we optimize for.

### 3.4 Accessibility target
- WCAG AA aspirational? Required? Beta-only "best-effort"?
- Keyboard navigation a hard requirement?
- Color-blindness considerations?

If the user shrugs, default to "WCAG AA aspirational; keyboard-navigable; tested against the common color-vision deficiencies." Surface the default, allow override.

### 3.5 Brand opinions (if any)
- Existing brand assets / colors / fonts? (Provide paths or links if so.)
- Tone: serious / playful / clinical / warm?
- References: any products whose feel we're chasing? (Linear's calm? Notion's flexibility? Stripe's clarity?)

If "no opinions yet — surprise me," capture that explicitly. Claude Design will generate a starting design language for you to react to.

### 3.6 Explicit do-not list
What design moves should Claude Design *not* make? Examples:
- "No gamification, no streaks, no badges — our user is a serious operator."
- "No marketing-site-style hero with stock photo of smiling team."
- "No dark patterns around pricing or cancellation."

Few items is fine. The point is to head off generic AI-design defaults before they land.

### 3.7 Out-of-scope for this round
What we're explicitly NOT designing in this phase. Examples: admin/back-office UI, marketing site, email templates. Carrying this forward keeps the brief focused.

## Step 4 — Extract verbatim quotes + commentary

For each source artifact, pull the passages that bear on design and write short commentary on what each implies. **Quote verbatim.** Don't paraphrase the customer's voice — designers (human or AI) lose information every time it's rewritten.

For each quoted passage, capture:
- **Quote** (verbatim, with leading `>` markdown)
- **Source** (path + section)
- **Design implication** (one or two sentences on what this means for the design)

### From PRFAQ
Pull: headline, summary, problem-in-customer-voice, customer quote, getting-started. Each likely bears on visual tone, hero copy, onboarding tone, dashboard messaging.

### From GTM plan
Pull: ICP paragraph, top-3 customer situations (especially trigger + pain), pricing/unit-of-value. The ICP shapes voice/density; the trigger shapes the first-screen narrative; the unit-of-value shapes what the dashboard centers on.

### From principles
Pull any principle that constrains design — most commonly:
- Customer obsession → design from the pain, not from features
- Surface assumptions, give override → no hidden defaults; every guess is visible with a two-click change path
- Numbers > adjectives → dashboards show numbers, not vibes

### From one-pager (if present)
Pull the value-prop sentence and any tagline candidates. These often become hero copy.

## Step 5 — Write the seed package

Create `./artifacts/design-seed/` with these files:

### `./artifacts/design-seed/brief.md`

```markdown
# Design brief — <Product name>

*Brief date: YYYY-MM-DD*
*For: Claude Design ([claude.ai/design](https://claude.ai/design))*
*Sources: PRFAQ.md, gtm-plan.md, principles.md, one-pager.md*

## What to make
<One paragraph: the product, the customer, the moment of value. Pulled forward from the PRFAQ summary — quoted verbatim where possible.>

## Primary user
<From Step 3.1>

## Primary flows
1. <Flow 1>
2. <Flow 2>
3. ...

## Devices
<From Step 3.3>

## Accessibility
<From Step 3.4>

## Brand opinions
<From Step 3.5 — or "no opinions yet — surprise me with a starting point we can react to.">

## Do not
- <Item 1>
- ...

## Out of scope for this round
- <Item 1>
- ...

## Companion files in this folder
- `from-prfaq.md` — verbatim PRFAQ quotes with design commentary
- `from-gtm-plan.md` — verbatim ICP + top-3 + pricing quotes with design commentary
- `from-principles.md` — design-binding principles with commentary
- `from-one-pager.md` — value statement (if one-pager exists)
- `sources.md` — paths to original artifacts in this repo

Read the companion files for the verbatim source material and how each fits.
```

### `./artifacts/design-seed/from-prfaq.md`

```markdown
# Verbatim quotes from PRFAQ

*Source: ../PRFAQ.md*

## Headline
> <verbatim>

**Design implication:** <one or two sentences>

## Summary
> <verbatim>

**Design implication:** <...>

## Problem (customer's voice)
> <verbatim>

**Design implication:** <...>

## Customer quote
> <verbatim>

**Design implication:** <...>

## Getting started
> <verbatim>

**Design implication:** <...>
```

### `./artifacts/design-seed/from-gtm-plan.md`

```markdown
# Verbatim quotes from GTM plan

*Source: ../gtm-plan.md*

## ICP
> <verbatim ICP paragraph>

**Design implication:** <...>

## Top-3 customers — situations
### Customer 1 — <name>
> <verbatim situation + trigger + pain>

**Design implication:** <...>

### Customer 2 — <name>
<same>

### Customer 3 — <name>
<same>

## Pricing / unit of value
> <verbatim>

**Design implication:** <...>
```

### `./artifacts/design-seed/from-principles.md`

```markdown
# Verbatim principles that bind design

*Source: ../principles.md*

## <Principle name 1>
> <verbatim rule + Why + How to apply>

**Design implication:** <...>

## <Principle name 2>
<same>
```

Include only principles that actually affect design choices. Don't pad.

### `./artifacts/design-seed/from-one-pager.md`

(Only if `./artifacts/one-pager.md` exists.)

```markdown
# Verbatim quotes from one-pager

*Source: ../one-pager.md*

## Value statement
> <verbatim>

**Design implication:** <...>

## Tagline candidates
> <verbatim>

**Design implication:** <...>
```

### `./artifacts/design-seed/sources.md`

```markdown
# Source artifacts in this repo

Raw links for Claude Design to fetch the originals if needed:

- `../PRFAQ.md`
- `../gtm-plan.md`
- `../principles.md`
- `../one-pager.md` (if exists)
- `../mvp-scope.md` (if exists — typically not yet at this phase)

These are the canonical sources. The companion `from-*.md` files in this folder are extracted quotes with commentary, not summaries — for context efficiency.
```

Read the brief back to the user in summary form before writing — especially the primary flows, devices, accessibility target, brand opinions, and do-not list. Confirm. Then write the files.

## Step 6 — Hand off to Claude Design

Once the seed is written, tell the user exactly how to start in Claude Design:

> **Hand off to Claude Design:**
>
> 1. Open [claude.ai/design](https://claude.ai/design).
> 2. Upload the contents of `./artifacts/design-seed/` (or zip the folder and drag it in). Claude Design accepts text, DOCX, PPTX, XLSX, and images — markdown files work fine pasted in or attached.
> 3. Paste `brief.md` as the opening prompt, or say: *"Use these files to design <product name>. Start with the primary flows listed in `brief.md`."*
> 4. Let Claude Design produce a first pass.

Surface assumptions: if the user has a custom Claude Design workspace, an existing design system, or a codebase Claude Design should read, mention it now — those go in as additional inputs at the start.

## Step 7 — Iterate freely

Tell the user how Claude Design iteration works, then make explicit that they decide when to stop:

> **Iterate in Claude Design:**
>
> - **Inline comments** on specific elements — point at a button, ask for changes.
> - **Direct edits** to text, copy, labels.
> - **Adjustment knobs** for spacing, color, layout — live tweaks without re-prompting.
> - **Voice** if you prefer talking through changes.
> - **Codebase or design-system pointers** if you want results that match an existing visual language.
>
> Common iteration loop:
> 1. Review the first pass against the primary flows in `brief.md`. Anything that's not pulling weight, cut.
> 2. Sharpen one flow at a time. Don't try to perfect everything in one round.
> 3. When something is close, use the knobs (spacing/color/type) rather than re-prompting from scratch.
> 4. Stop iterating when the design answers the question *"does this make the customer's pain go away in a way I'd be proud to show them?"* The answer is yours to call.

This step writes nothing. It's empowerment.

## Step 8 — Request the handoff bundle when ready

When the user is ready, they request a handoff bundle from Claude Design. This is the package that ships to Claude Code for integration into the repo.

Tell the user:

> **When you're ready to integrate:**
>
> 1. In Claude Design, ask: *"Package this as a handoff bundle for Claude Code, targeting `./artifacts/design/` in my project."*
> 2. Claude Design will produce a bundle containing the flows, mockups, design tokens, generated HTML/components, and a handoff README.
> 3. Pass the bundle to Claude Code with: *"Integrate this Claude Design handoff bundle into `./artifacts/design/`."* Claude Code will unpack the files into the project.
> 4. Once integration is complete, come back and run `/product-4-design` again — it'll detect the new `./artifacts/design/` contents and move to Step 9 (the catalog step).

The catalog step (Step 9) only runs when `./artifacts/design/` exists and contains integration output.

## Step 9 — Catalog the integrated results

Run this step only when `./artifacts/design/` exists with handoff-bundle output. (If the user invokes `/product-4-design` after integration, detect this from the file presence and jump here.)

Walk the user through producing `./artifacts/design/index.md`. List:

- **What arrived** — file inventory by type (flows, mockups/images, HTML prototype, design tokens, components, handoff README)
- **Key design decisions** — read the handoff README and ask the user to confirm/correct: the design language (color, type, spacing scale), the navigation pattern, the empty/loading/error patterns, any opinionated UX choices Claude Design made
- **Open questions** — anything the design assumed that needs validation, anything that contradicts an upstream artifact, anything deferred
- **What the design covers** — which of the `brief.md` primary flows are designed; which remain
- **What the design does NOT cover** — explicit, to prevent silent gaps when MVP scope is locked

Write `./artifacts/design/index.md`:

```markdown
# Design index

*Integration date: YYYY-MM-DD*
*Source: Claude Design handoff bundle*
*Seed: ../design-seed/brief.md*

## What arrived
| File / folder | Type | Notes |
|---|---|---|
| <path> | <flows / mockup / HTML / tokens / components / readme> | <...> |
| ... | ... | ... |

## Key design decisions
- **Color:** <e.g., "single-accent palette, blue-600 primary, neutral-50 background">
- **Type:** <e.g., "Inter for UI, monospace for data">
- **Spacing scale:** <e.g., "4 / 8 / 12 / 16 / 24 / 32 / 48">
- **Navigation pattern:** <e.g., "sidebar with collapsing sections">
- **Empty / loading / error patterns:** <e.g., "skeleton loaders; empty states with one-line copy + primary action">
- **Other opinionated choices:** <...>

## Flows covered (from brief.md)
- [x] <Flow 1>
- [x] <Flow 2>
- [ ] <Flow 3 — not in this pass>
- ...

## Flows NOT covered
- <Flow / area> — <why deferred>

## Open questions
- <Q 1 — what's unresolved, who needs to answer, by when>
- ...

## Source bundle README
See `<path>` for Claude Design's own handoff notes.
```

Confirm the catalog with the user before writing.

## Step 10 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with this header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append the appropriate line depending on which step the user reached this session:

```
YYYY-MM-DD HH:MM PST — /product-4-design — ./artifacts/design-seed/ — seed package written; iterate in claude.ai/design
```

Or, after the catalog step:

```
YYYY-MM-DD HH:MM PST — /product-4-design — ./artifacts/design/index.md — handoff bundle integrated
```

Use the actual current local PST time. If unsure of the timezone, ask once and remember.

If the user runs both steps in one session (rare but possible), append both lines in order.

## Step 11 — Offer toolchain integration

Only relevant after the catalog step (Step 9 done) — there's nothing useful to push from the seed alone.

Read `./artifacts/toolchain.md` if it exists. Two destinations are relevant here:

- **Design** (Figma, Sketch) → if the team uses a dedicated design tool AND its MCP is available, offer to import the visuals from the Claude Design handoff into the design tool (or at minimum link the bundle from a frame).
- **Docs / wiki** (Confluence, Notion) → for `./artifacts/design/index.md` itself, so the design direction is discoverable next to other product docs.

If a relevant tool is named AND its MCP is available, offer the corresponding push and add a `published_url:` reference near the top of the local index. Local markdown remains source of truth.

If no tool is named, "(not yet)", or MCP unavailable, skip silently. The Claude Design canvas + local catalog are enough.

See the playbook SKILL's "Toolchain integration" convention for the full pattern.

## Step 12 — Tell them what's next

Branch on which step the user reached:

**If they only wrote the seed (Step 5 done, Step 9 not yet):**

> Next:
> - Open [claude.ai/design](https://claude.ai/design) and upload `./artifacts/design-seed/` as described in Step 6.
> - Iterate as long as you need. When you're ready, request the handoff bundle and integrate via Claude Code.
> - Then run `/product-4-design` again — it'll detect the integrated results and produce the catalog (`./artifacts/design/index.md`).

**If they integrated and cataloged (Step 9 done):**

> Next:
> - `/product-5-mvp-scope` — define the defensible MVP scope with numbers, now that the design direction is concrete.
> - Or `/product-7-stress-test ./artifacts/design/index.md` if the design surfaced choices that feel load-bearing (Customer + Product POVs are sharpest here).
