---
description: Draft the PRFAQ (press release + internal & external FAQ), iterate with user feedback, then generate the one-pager. Writes ./artifacts/PRFAQ.md and ./artifacts/one-pager.md.
---

# /product-2-vision

You are helping the user **work backwards from the customer** to articulate the product's vision. The artifact is a PRFAQ — a press release of the product *as if it already shipped*, paired with an FAQ that surfaces the questions a smart team would ask before approving the work. Once the PRFAQ is locked, you'll generate a tight one-pager derived from it.

This is the most important upstream artifact. A PRFAQ that survives an honest review almost never becomes a failed product. A PRFAQ that's hand-waved at this stage almost always does.

References this command draws from:
- Amazon's Working Backwards methodology (workingbackwards.com)
- *The PRFAQ Method* (theprfaq.com)
- Product School's PRFAQ guide

---

## Step 1 — Intro the job to be done

Open with this orientation:

> **Vision** is where you write the press release of your product as if it shipped today — then the FAQ that answers what a smart team would ask before approving it. This is *writing as thinking*: the act of forming the words forces the precision that bullet points let you skip. The press release is a forcing function to keep you customer-obsessed; the FAQ is a forcing function to confront the hard questions early.
>
> We'll work in this order:
> 1. **PRFAQ first** — press release, then internal FAQ, then external FAQ. Get a draft fast (hours, not days).
> 2. **Iterate with feedback** — at least one cycle where you read it aloud, mark what's weak, sharpen.
> 3. **One-pager** — derived from the locked PRFAQ. Same vision, condensed.
>
> Output: `./artifacts/PRFAQ.md` and `./artifacts/one-pager.md`.

Then check whether `./artifacts/PRFAQ.md` exists. If yes, infer from the user's request whether they want to **continue iterating** the existing draft or **start fresh**. Ask only if their intent is genuinely unclear. Never silently overwrite.

If `./artifacts/principles.md` exists, read it. The user's principles (especially customer-obsession and truth-seeking if they adopted them) inform how strict you should be in this command.

## Step 2 — Draft the Press Release

The press release section uses the structure validated by Amazon's Working Backwards practice. Walk the user through each component. For each, ask the focused question, capture the answer, and write the section. Don't accept hand-waving — the customer's voice and specific numbers belong here.

### 2.1 Headline
One sentence. The product name and the single biggest customer outcome.

> Q: What's the product called, and what's the single biggest outcome a customer gets from it? (One sentence. No marketing fluff.)

### 2.2 Subheading
One sentence. Names the specific customer segment and the key benefit.

> Q: Who is the customer (be specific — not "businesses" but "real-estate agents in the Sacramento metro who close 4–8 deals/year"), and what's the key benefit for *them*?

If the user says "everyone" or "all small businesses," push back. Working Backwards: precise segments win, vague segments lose.

### 2.3 Summary paragraph
2–4 sentences. Launch date (real or planned), what the product is, who it's for, why now.

> Q: When does it launch (or did it)? What does it do in 2–3 sentences? Why does this make sense to launch *now*?

### 2.4 Problem paragraph
The customer's pain *in their words*. No solution-talk yet.

> Q: What's the pain the customer feels today? Quote them if you can. What do they do today as a workaround? What does the pain cost them — in time, dollars, or missed opportunity?

If the user starts describing the product, redirect: "Hold the solution. What's the customer's pain *before* they hear about your product?"

### 2.5 Solution paragraph
How the product relieves the pain. What's different from what the customer would otherwise do (competitor, status quo, manual workaround).

> Q: How does the product solve the pain? What's the one thing it does that competitors and the status quo don't? Keep it concrete — what does the customer actually click or receive?

### 2.6 Customer quote
A *plausible* quote from a real archetype customer reacting to the launch. Specific name and role (can be a representative archetype — e.g., "Maria Chen, broker at Chen Realty").

> Q: Imagine your first happy customer. Who are they (name, role, company)? What's the one sentence they'd say about getting this product?

### 2.7 Company quote / leader quote
One sentence from the company (founder, lead) on why this product exists.

> Q: In one sentence, why does this product exist? (Not "we want to revolutionize X" — what's the real reason?)

### 2.8 Getting started / call to action
How does a customer try or buy it? Pricing, availability, sign-up path.

> Q: What does a customer do to try it? Where do they go? Is there a price, a trial, a waitlist?

## Step 3 — Draft the Internal FAQ

These are the questions a senior operator would ask before approving the work. **Required questions** (don't skip any):

1. **What's the market size and is it big enough to matter?** TAM, SAM, plausible-near-term opportunity. Numbers, not adjectives.
2. **Who are the competitors and what's our differentiation?** Name 2–4 real alternatives (including status quo / DIY). One sentence each on why we win.
3. **What are the top 3 risks that could kill this?** Be specific. "Tech risk" is not an answer. "If LLM call costs don't drop 40% in 18 months, gross margin caps at 15%" is.
4. **What are the top 3 assumptions that must be true?** Same precision standard. These become the things `/product-7-stress-test` will hammer at.
5. **What's the per-unit economics story?** What's the unit (lead, run, seat, deal)? What does each unit cost to serve? What does it sell for? When does this turn profitable per unit?
6. **What new capabilities do we need to build or buy?** What's not in our current stack/skillset that this requires?
7. **What does success look like in 6 months? In 18 months?** Concrete metrics, not "traction."
8. **What's the smallest version that proves the thesis?** This previews `/product-5-mvp-scope` — don't fully scope here, but name the cheapest experiment that disconfirms the riskiest assumption.

Ask each question. Capture answers in the user's voice. Don't let them skip with "TBD" — if they don't know, that's a flag worth recording explicitly ("Unknown — must resolve before MVP scope lock").

## Step 4 — Draft the External FAQ

These are the questions a customer would ask. **Required questions** (don't skip any):

1. **What is it?** One-paragraph explanation in customer terms.
2. **What does it cost?** A number, even if provisional. "Pricing TBD" is rarely acceptable — at minimum: "We're targeting $X/month with a free trial."
3. **How do I get help?** Support channel and response time expectation.
4. **Where can I get it?** URL, app store, sales contact — whatever the access path is.
5. **When can I get it?** Availability — geography, eligibility, timing.
6. **How does it work?** 2–4 sentence walkthrough of the customer's first 60 seconds with the product.

**Plus the playbook's required addition:**

7. **Who are the first three customers?** Name them. Real people or real archetypes with enough specificity that you'd recognize them on a sales call. If the user can't name three, that's the most important thing to fix before leaving this command — it tells you the segment isn't sharp enough to sell to.

## Step 5 — Iterate

Before writing the file, run at least one feedback pass:

1. Read the entire draft back to the user in plain prose.
2. Ask: "What's weak? What sounds like marketing copy that doesn't survive scrutiny? What FAQ answer is too vague?"
3. Capture the user's feedback and revise the relevant sections.
4. Repeat once more if anything substantial changed.

Goal: not perfection, but no obviously soft spots. Velocity over speed — keep moving, but don't lock something the user already knows is weak.

## Step 6 — Write `./artifacts/PRFAQ.md`

Use this structure exactly:

```markdown
# PRFAQ — <Product name>

*Draft date: YYYY-MM-DD*

## Press Release

### <Headline>

**<Subheading>**

<Summary paragraph>

<Problem paragraph>

<Solution paragraph>

> "<Customer quote>"
> — <Customer name, role, company>

> "<Company quote>"
> — <Founder/leader name, title>

**Getting started:** <Call to action / availability>

---

## Internal FAQ

### 1. Market size — is it big enough to matter?
<Answer>

### 2. Competition and differentiation
<Answer>

### 3. Top 3 risks
<Answer>

### 4. Top 3 must-be-true assumptions
<Answer>

### 5. Per-unit economics
<Answer>

### 6. New capabilities needed
<Answer>

### 7. Success metrics — 6 months / 18 months
<Answer>

### 8. Smallest version that proves the thesis
<Answer>

---

## External FAQ

### What is it?
<Answer>

### What does it cost?
<Answer>

### How do I get help?
<Answer>

### Where can I get it?
<Answer>

### When can I get it?
<Answer>

### How does it work?
<Answer>

### Who are the first three customers?
1. <Customer 1 — name or archetype, segment, why them>
2. <Customer 2 — name or archetype, segment, why them>
3. <Customer 3 — name or archetype, segment, why them>
```

## Step 7 — Generate the one-pager

Once the PRFAQ is locked, derive a one-pager. Same vision, condensed for skim-reading by an exec, investor, or new team member. Write to `./artifacts/one-pager.md`:

```markdown
# <Product name> — One-Pager

*Derived from PRFAQ, draft date: YYYY-MM-DD*

**Customer:** <Specific segment — one sentence>

**Pain:** <The pain in their words — one sentence>

**Product:** <What it does — one sentence>

**Differentiation:** <Why this wins over status quo / competitors — one sentence>

**Unit of value & pricing:** <The unit and the price>

**First three customers:**
1. <Customer 1>
2. <Customer 2>
3. <Customer 3>

**Top risk:** <The #1 thing that could kill this>

**Top assumption:** <The #1 thing that must be true>

**Success in 6 months:** <Concrete metric>
```

Confirm the one-pager with the user. Edit if anything reads weaker than the PRFAQ source.

## Step 8 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with this header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append:

```
YYYY-MM-DD HH:MM PST — /product-2-vision — ./artifacts/PRFAQ.md + ./artifacts/one-pager.md
```

Use the actual current local PST time. If unsure of the timezone, ask once and remember.

## Step 9 — Offer toolchain integration

Read `./artifacts/toolchain.md` if it exists. Look at the **Docs / wiki** value.

If a docs/wiki tool is named (Confluence, Notion, Google Docs) AND its MCP is available in this session, offer: "Want me to publish PRFAQ.md and one-pager.md to your <tool>?" If yes, push as additive sync and add a `published_url:` line near the top of each local file. Local markdown remains source of truth.

If no tool is named, the value is "(not yet)", or the MCP isn't available, skip silently. The local artifacts are enough.

See the playbook SKILL's "Toolchain integration" convention for the full pattern.

## Step 10 — Tell them what's next

Close with:

> Next, either:
> - `/product-7-stress-test ./artifacts/PRFAQ.md` — run the multi-POV antagonist on what you just built (recommended before going further).
> - `/product-3-gtm` — turn the first-three customers into a concrete go-to-market plan.

Recommend the stress-test first if any of the FAQ answers came out soft.
