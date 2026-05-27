---
description: Lock the crisp ICP + market definition, deep-dive the top 3 named beta customers (contacts, approach, value), sketch pricing v0.1 + ROI, and define kill criteria. Writes ./artifacts/gtm-plan.md.
---

# /3-gtm

You are helping the user turn a PRFAQ's first-three customers from "named on paper" into "contactable, approachable, sellable." This is the phase that separates a *good idea* from an *actionable idea with a path to a market*.

Three layers, all required:

1. **Crisp ICP + market definition** — locked at this stage. Sharp enough that you recognize a target customer on first contact.
2. **Top 3 deep-dive** — for each: contact name, approach path, what's in it for them, what's in it for us, named outreach owner, the specific ask.
3. **Pricing v0.1 + kill criteria** — forming pricing mature enough to seed an ROI forecast; explicit triggers for kill vs. pivot.

What this phase does **not** cover: how we reach the broader market at scale (channels, demand gen, tactics). That's deferred to a post-launch scale phase. Here, market = defined and crisp; first-three = how we sell them.

---

## Step 1 — Intro the job to be done

Open with this orientation:

> **GTM** is where we turn the PRFAQ's first-three from named-on-paper to contactable-and-sellable. We'll lock the crisp ICP and market sizing at the broad level, then go deep on the three specific humans we'll reach out to first — including who's making the contact, how, and what's in it for them. We'll also form pricing v0.1 (enough to seed an ROI forecast we can test against MVP economics) and write down kill criteria so we know in advance what "this isn't working" looks like.
>
> Scope note: this phase locks *who* the market is and *who* the first three are. The *how* of reaching the broader market at scale is deferred to a later, post-launch scale phase.
>
> Output: `./artifacts/gtm-plan.md`.

## Step 2 — Read context

Required upstream artifact: `./artifacts/PRFAQ.md`. If it doesn't exist, **block**: tell the user GTM depends on PRFAQ and recommend running `/2-vision` first. Don't proceed without it.

Optional but useful:
- `./artifacts/one-pager.md` — read for ICP/segment language consistency.
- `./artifacts/principles.md` — read so the artifact stays aligned with the user's stated principles (especially customer-obsession if adopted).
- `./artifacts/gtm-plan.md` — if present, infer from the user's request whether to continue iterating or start fresh. Ask only if intent is genuinely unclear.

Pull the three customers from the PRFAQ's `Who are the first three customers?` section. These are your starting point — they may get sharpened or, if PRFAQ named archetypes, converted to real people in Step 4.

## Step 3 — Market & ICP definition (locked crisp here)

Walk the user through these four blocks. Numbers, not adjectives.

### 3.1 Ideal Customer Profile (ICP) — locked here
Sharp enough that you'd recognize an in-target customer on first contact. Capture:

- **Firmographics / demographics:** company size? industry? revenue band? role title? team size? geography?
- **Situation:** what are they doing today? what tools? what workflow?
- **Trigger:** what event or pain causes them to start looking for something like this? (e.g., "they just lost a deal because their lead list went stale.")
- **Disqualifiers:** who looks ICP-shaped but isn't actually a fit, and why. Naming these prevents wasted outreach later.

> Q to user: Read the ICP back to me as one paragraph in plain English. If you can't, it isn't crisp yet.

### 3.2 Market sizing — TAM / SAM / SOM
Each with a number AND the assumption behind it.

- **TAM** (Total Addressable Market): how big is the universe of all possible buyers? Show the math.
- **SAM** (Serviceable Addressable Market): of TAM, how much can our product realistically serve given geography, language, product fit, regulatory? Show the cut.
- **SOM** (Serviceable Obtainable Market): of SAM, what's a realistic 18–36 month obtainable slice? Show the assumptions on penetration, sales motion, and competition.

If the user says "it's a huge market," push back. Ask: "What's the number? Where did it come from?" If they don't know, write it as "Unknown — must source" and move on. Don't let TAM-by-vibes survive into the artifact.

### 3.3 Why-now
What changed in the world that makes this market reachable today? Possible answers: a regulatory shift, a cost curve crossing a threshold (e.g., LLM inference costs), a new platform, a behavioral change, a new data source. If "nothing changed, we just decided to build it" — that's a risk to flag.

### 3.4 Deferred — explicitly call out
Write in the artifact: *"How we reach the broader market at scale (channels, demand gen, tactics) is deferred to a later scale phase. This phase locks who the market is and who the first three are."*

## Step 4 — Top 3 deep-dive (heart of the command)

This is the most important step in the command. For each of the three customers from the PRFAQ, walk through this checklist. If any answer is missing, surface it — the gap is the work.

### Per-customer template
For each customer (do all three; don't combine):

#### Customer N — <Name of the company / archetype>
- **Segment / why they're in the ICP:** one sentence connecting them to the ICP defined in Step 3.
- **Named contact at the customer:** the specific human you'll reach out to. Name, title, and *why them* (decision-maker? influencer? user with the pain? introducer to the buyer?). If unknown, that's a Step-4 deliverable: "Find a contact at <Company> by <date>."
- **Approach path:** how the first contact happens.
  - Warm intro? (From whom? Is the intro confirmed or just possible?)
  - Cold outreach? (Email? LinkedIn? Phone? Why this channel for this person?)
  - Mutual contact / referral? (Named, confirmed?)
  - Inbound? (Why would they find us — what triggers their search?)
- **Why us, from THEIR POV:** what's in it for them. Specific value, specific pain relieved. Quote-able if possible: *"This saves Maria 6 hours a week of manually re-qualifying stale leads."* Generic value ("makes them more productive") isn't enough — name the unit.
- **What's in it for us:** what we get from landing this customer beyond revenue. Learnings? Reference logo? Signal on segment? Co-development input? Be explicit — beta customers often pay in feedback, not cash, and we should be honest about which currency we're getting.
- **Our outreach owner:** the named person on our side who runs the relationship. Not "the team" — one human. If solo founder, it's the founder. If unclear, that's a gap to close.
- **The specific ask:** what are we asking them to do?
  - Beta participant (free, gives feedback)?
  - Paid pilot ($X for Y weeks)?
  - Design partner (deep collaboration, possibly equity-like terms)?
  - Reference call recipient (later sales asset)?
  - Be specific. "Be a customer" is not an ask.
- **Timing:**
  - Outreach date (when do we contact them?)
  - Decision window (how long do we give them to say yes/no?)
  - Readiness date (when are we ready to actually onboard them?)

If PRFAQ named archetypes (e.g., "Maria the broker") rather than real humans, **convert here**. Ask the user: "Who's the real Maria? Name a specific person you could text today." If they can't name three real people, flag this as a critical gap — the GTM plan isn't actionable until the names exist.

## Step 5 — Pricing v0.1 (forming, not locked)

Walk through these four blocks. Pricing won't fully lock until MVP, but it needs to be mature enough here to seed an ROI forecast we'll test against actual costs and market response.

### 5.1 Beta pricing
What do beta customers pay? Zero is a price — call it out explicitly. If non-zero, why this number? If zero, what *do* they "pay" (time, feedback, reference rights)?

### 5.2 Forward pricing & packaging
- **Unit of value:** what's the unit being sold? (seat? workflow run? lead? deal? GB? successful outcome?) The unit choice is more important than the dollar amount — it determines whether revenue scales with customer success.
- **Tier structure:** is there one tier? Three? Free → paid → enterprise? Don't over-engineer; one or two tiers is fine at v0.1.
- **Anchor numbers:** what's the price per unit? Per month? Annual?

### 5.3 Willingness-to-pay & cost-to-serve
- **Willingness-to-pay anchor:** what do customers tell you (or did similar products charge)? If purely guess, label it: "Estimated, not validated."
- **Cost-to-serve estimate:** what does it cost us to deliver one unit? Include infra, AI/LLM calls if relevant, support, any per-customer human time.
- **Implied margin at v0.1 pricing:** price minus cost. If negative, the unit economics don't work — surface this.

### 5.4 ROI sketch (for the customer)
What's the customer's ROI at the proposed price? Concrete:

> Example: "At $X/month, customer saves Y hours/month at their loaded rate of $Z/hour = $W of value. ROI multiple: W/X."

Don't accept ROI <2x without flagging it — anything below that is hard to defend at sales time.

## Step 6 — Kill criteria

Write down explicitly what "this isn't working" looks like. The point is to decide *now* (sober) what would trigger reconsideration later (when emotions are involved).

Required entries:

- **All 3 say no:** what's the signal we're reading from the no's? What's the pivot path?
- **1 yes, 2 no with segment-mismatch signals:** if two of the three reveal we're targeting the wrong segment, what's the pivot path?
- **Pricing floor:** at what price does the unit economics break? Below this, kill or restructure.
- **Outreach silence:** if N% of cold outreach is ignored within X weeks, what does that signal? Channel problem, message problem, or product problem?
- **Explicit kill-vs-pivot trigger:** the one numeric or qualitative threshold that, if hit, means kill the idea rather than pivot.

Keep it short and specific. Vague kill criteria are no kill criteria.

## Step 7 — Write `./artifacts/gtm-plan.md`

Use this structure exactly:

```markdown
# GTM Plan — <Product name>

*Draft date: YYYY-MM-DD*

## ICP & Market

### Ideal Customer Profile
**Firmographics / demographics:** <...>
**Situation:** <...>
**Trigger:** <...>
**Disqualifiers:** <...>

### Market sizing
- **TAM:** $X — <assumption / source>
- **SAM:** $Y — <cut from TAM and why>
- **SOM:** $Z — <obtainable slice over 18–36 months>

### Why now
<One paragraph>

> **Deferred:** market-scale acquisition (channels, demand gen, tactics) is deferred to a later post-launch scale phase. This phase locks the ICP and the first three.

---

## Top 3 First Customers

### Customer 1 — <Name>
- **Segment / why ICP:** <...>
- **Named contact:** <Name, title> — <why them>
- **Approach path:** <...>
- **Why us (their POV):** <...>
- **What's in it for us:** <...>
- **Our outreach owner:** <Named person>
- **The ask:** <Beta / paid pilot / design partner / etc.>
- **Timing:** outreach <date> → decision by <date> → onboard by <date>

### Customer 2 — <Name>
<same structure>

### Customer 3 — <Name>
<same structure>

---

## Pricing v0.1

### Beta pricing
<Zero / $X — why>

### Forward pricing & packaging
- **Unit of value:** <...>
- **Tier structure:** <...>
- **Anchor numbers:** <...>

### Willingness-to-pay & cost-to-serve
- **WTP anchor:** <...>
- **Cost-to-serve estimate:** <...>
- **Implied margin at v0.1:** <...>

### ROI sketch (customer)
<Worked example, target ROI multiple>

---

## Kill criteria

- **All 3 say no:** <signal + pivot path>
- **1 yes / 2 no with segment mismatch:** <signal + pivot path>
- **Pricing floor:** $<X> below which unit economics break
- **Outreach silence:** <threshold + signal>
- **Kill trigger:** <specific threshold>
```

Read the final draft back to the user in summary form before writing. Confirm. Then write the file.

## Step 8 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with this header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append:

```
YYYY-MM-DD HH:MM PST — /3-gtm — ./artifacts/gtm-plan.md
```

Use the actual current local PST time. If unsure of the timezone, ask once and remember.

## Step 9 — Offer toolchain integration

Read `./artifacts/toolchain.md` if it exists. Two destinations are relevant here:

- **Docs / wiki** (Confluence, Notion, Google Docs) → for the gtm-plan itself.
- **Customer support / CRM** (Salesforce, HubSpot, Zendesk) — if named, the first-three list is also a natural fit for opportunity records / contact records.

If a relevant tool is named AND its MCP is available, offer the push: "Want me to publish gtm-plan.md to your <docs tool>?" and/or "Want me to create contact/opportunity records for your first-three in your <CRM>?" If yes, push as additive sync and add a `published_url:` reference. Local markdown remains source of truth.

If no tool is named, "(not yet)", or MCP unavailable, skip silently.

See the playbook SKILL's "Toolchain integration" convention for the full pattern.

## Step 10 — Tell them what's next

Close with:

> Next, either:
> - `/7-stress-test ./artifacts/gtm-plan.md` — multi-POV antagonist (Customer + Sponsor POVs are sharpest here). Strongly recommended before committing outreach.
> - `/4-design` — translate the validated GTM and vision into design direction (user flows, IA, design language) before scoping the MVP.
> - Then `/5-mvp-scope` — define the defensible MVP scope with numbers, knowing who you're building for.

Recommend the stress-test first if any of these are true: ICP felt vague, top-3 contacts were not real humans, pricing had no anchor, or kill criteria were soft.
