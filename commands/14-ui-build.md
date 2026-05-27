---
description: Build the real production UI with mock data as a temporary backend. Spec the mock data 1:1 with data-model entities so the API swap is mechanical later. Offer to run the dev server for live feedback. Writes ./artifacts/ui-build.md.
---

# /14-ui-build

You are starting the **real UI build**. This is not a throwaway prototype — what gets shipped here is the production frontend. The only thing temporary is the data store behind it: mock data conforming 1:1 to the entities in `./artifacts/data-model.md`, which `/15-api-build` will swap for real APIs entity by entity.

The goal of this phase: a working UI in the named first-three's hands as fast as possible, instrumented so the signals defined in `/5-mvp-scope` §7.2 are actually captured.

This honors Principle 3 (mock data is a schema spec) and Principle 6 (working MVP > pre-build research). The mock is a schema, not a fiction. Every field in the mock will exist in a real API response when `/15-api-build` lands.

---

## Step 1 — Intro the job to be done

Open with:

> **UI build** is where the real UI ships. Mock data sits behind it as a temporary backend that conforms 1:1 to the entities in `./artifacts/data-model.md` — the API swap in `/15-api-build` will be mechanical. We'll spec what's in the UI shell, what's wired-to-mock vs stubbed-with-a-message, and how we'll instrument feedback. Then I'll offer to start the dev server so you (or a named first-three) can touch it live. Output: `./artifacts/ui-build.md`.

## Step 2 — Read context

Required:
- `./artifacts/design/index.md` — design direction the UI implements
- `./artifacts/data-model.md` — entity shapes the mock data must conform to
- `./artifacts/mvp-scope.md` — IN list (what flows must work) and §7.2 (signals we must collect)
- `./artifacts/toolchain.md` — frontend framework, deploy target, package manager

Helpful:
- `./artifacts/roles.md` — auth/tenancy/permissions affect UI state
- `./artifacts/backlog/` — UI-tagged items already enumerated
- `./artifacts/access-patterns.md` — informs which entities are read together (drives page composition)

If any required file is missing, stop and tell the user which upstream command to run. Don't start a UI without a data model — the whole "1:1 mock" property depends on it.

If `./artifacts/ui-build.md` already exists, infer intent: iterate (update what's shipped, add another flow) or start fresh? Ask only if unclear. Never silently overwrite.

## Step 3 — Define the UI shell scope

From `mvp-scope.md` IN list, decide which routes/pages/flows are in this build cycle. A good shell is the smallest thing that lets a first-three user complete one end-to-end workflow.

For each route/page:

- **Path:** `/signup`, `/dashboard`, `/projects/[id]`, etc.
- **Purpose:** what the user does here, in one sentence
- **Entities read:** which `data-model.md` entities power this view
- **Entities written:** what a form/action mutates
- **Auth state:** signed-out / signed-in / role-gated
- **Wired-to-mock or stubbed-with-a-message:** see Step 5

Confirm the route list with the user. Less is more — the goal is "one workflow end-to-end," not "every screen."

## Step 4 — Specify the mock dataset

For each entity in `./artifacts/data-model.md` that powers an in-scope route:

- **Mock record count:** how many fixtures (typically 3–20 per entity — enough to exercise empty/single/many states)
- **Field-by-field shape:** every field from the data model, with realistic example values
- **Relationships:** how mock records reference each other (foreign keys / GSI keys / nested IDs)
- **Edge cases included:** empty strings, max-length strings, null where allowed, unicode, long lists — so the UI handles them in the build, not the cutover
- **Storage form:** static JSON imported at build, in-memory store, MSW/handler module, etc. — pick one consistent with the framework named in `toolchain.md`

**The 1:1 rule.** Every field name and shape in the mock must match the data-model entity exactly. No extra UI-convenience fields. If the UI needs a derived value, derive it in a selector/hook — don't bake it into the mock. When `/15-api-build` swaps the data source, the only thing that changes is the fetch call.

Discuss the proposed mock dataset with the user before writing the artifact. They should be able to point at any entity and say "we need more variation here" or "this one can wait."

## Step 5 — Wired vs stubbed

For each route/flow, classify:

- **Wired to mock:** the UI reads/writes mock data and behaves end-to-end. This is the default for in-scope flows.
- **Stubbed with a message:** the route exists but renders "Coming soon — not in this cycle" or similar. Use this when a flow is in MVP scope but not in this build cycle, so users see the surface area without hitting a broken page.
- **Not implemented:** the route doesn't exist yet. Reserved for out-of-MVP routes.

Capture the classification per route. The user-touch plan in Step 6 has to be honest about what they'll actually be able to do.

## Step 6 — Instrumentation + user-touch plan

### 6.1 Instrumentation
Per signal in `mvp-scope.md` §7.2, decide:
- **Event name:** matches what you'll query later (`workflow_started`, `workflow_completed`, `feedback_submitted`)
- **Where it fires:** which component/handler
- **Where it lands:** PostHog / Segment / Mixpanel / a local file in dev / browser console for now (anything is better than nothing, but pick the one that survives to prod)
- **Reviewable how:** dashboard URL, query, or "user runs script after each session"

### 6.2 User-touch plan
- **Who:** one to three named users (pull from `gtm-plan.md` first-three; if none of them can touch it in the next 7 days, say so and pick a stand-in)
- **When:** target date for first touch (default: within 7 days of starting this phase — aggressive on purpose; see Principle 6)
- **How they access it:** localhost share session / Vercel preview link / dev environment URL / packaged build
- **What you'll watch:** observed-behavior signals from §6.1 first; conversation second
- **Decisions the signals will trigger:** continue / iterate / cut / promote a stubbed flow

If the user-touch plan slips by more than a week, that triggers the `time-to-first-user-touch` slip clause in `./artifacts/go-no-go.md` — flag it.

## Step 7 — Write the artifact

Path: `./artifacts/ui-build.md`

```markdown
# UI build

*Phase started: YYYY-MM-DD*

## Routes / pages in this cycle

| Path | Purpose | Entities read | Entities written | Auth | State |
|---|---|---|---|---|---|
| /... | ... | ... | ... | ... | wired / stubbed |

## Mock dataset

**Storage form:** <static JSON / in-memory / MSW / etc.>

**1:1 rule:** every field matches the data-model entity exactly. No UI-convenience fields baked in.

### Entity: <Name>
- Mock record count: <N>
- Edge cases included: <list>
- Field shape: <inline or link to fixture file>

### Entity: <Name>
...

## Instrumentation

| Signal | Event name | Where it fires | Where it lands | Reviewable |
|---|---|---|---|---|
| ... | ... | ... | ... | ... |

## User-touch plan

- **Who:** <named first-three or stand-in>
- **First-touch date:** <YYYY-MM-DD>
- **Access:** <preview URL / dev share / packaged>
- **Decisions the signals will trigger:** <list>

## What's shipped this cycle

- <Route /flow> — <wired/stubbed> — <status>
- ...

## What's deferred to next cycle

- <Route / flow> — <why deferred>

## Open questions

- <Any UI / mock-data decisions still open>
```

Confirm the structure with the user before writing — especially the route list, the mock dataset shape per entity, and the user-touch date. Then write.

## Step 8 — Append to the log

Append to `./artifacts/product-log.md`:

```
YYYY-MM-DD HH:MM PST — /14-ui-build — ./artifacts/ui-build.md
```

Use the actual current local PST time. Create the log file with a header if it doesn't exist yet.

## Step 9 — Offer to run the dev server

This is the highest-value moment in the phase. The artifact is a plan — the dev server is the feedback loop.

Detect the dev command from the project:

- **Node:** `package.json` → `scripts.dev` (e.g., `npm run dev`, `pnpm dev`, `yarn dev`, `bun dev`)
- **Next.js / Vite / Remix / etc.:** usually `<pkgmgr> dev` exposed via the script above
- **Make-driven repo:** `Makefile` → `dev` or `serve` target
- **Other:** ask the user

Confirm the detected command with the user. Then offer:

> Ready to start the dev server? I'll run `<command>` in the background, give you the URL, and you (or one of the first-three over a share session) can click through. Tell me what's broken or missing and we'll iterate before declaring this phase done.

If the user accepts:

1. Start the dev process in the background. Capture the local URL (typically `http://localhost:3000` or whatever the framework defaults to).
2. Tell the user the URL explicitly and that they can stop it any time.
3. Wait for the user's feedback. Capture whatever they observe — flows that work, flows that broke, UI nits, mock data that needs more variation.
4. For each issue raised, decide:
   - **Fix in this cycle:** make the change, hot-reload validates it, update the artifact's "what's shipped" section
   - **Defer to next cycle:** add to "what's deferred" with a one-line reason
   - **Promote to a stubbed flow:** if a deferred route blocks the workflow, build the stub now
5. When the user is satisfied — or when the user-touch date arrives, whichever comes first — finalize.

If the user declines (e.g., they want to spend more time planning before running anything), skip and proceed to Step 10. Note in the artifact that the dev server wasn't run this session.

**Rule:** don't claim this phase complete until either (a) the dev server has run and the user has touched the UI, or (b) the user has explicitly said "I'll run it myself later" and accepted that as the close.

## Step 10 — Offer toolchain integration

Read `./artifacts/toolchain.md` if it exists. Look at **Docs/wiki**, **Issue tracker**, and **Source control**.

| Tool named | Action |
|---|---|
| Confluence / Notion / Google Docs | Offer to push `ui-build.md` as a page (artifact stays canonical local) |
| Issue tracker (any) | Offer to mark backlog items now in-progress / done based on what shipped |
| GitHub / GitLab | Offer to open a PR with the cycle's UI work if commits exist on a branch |
| Nothing / "(not yet)" | Skip; local-only is fine |

The push only fires if the tool is named AND its MCP is available in the current session. Otherwise stay local.

If the user is using a deploy-preview tool (Vercel, Netlify, Cloudflare Pages) and a preview URL was generated, offer to add it to the artifact's user-touch plan.

## Step 11 — Tell them what's next

Branch on what the dev-server session surfaced:

- **UI is in users' hands and signals are landing:** Next is `/15-api-build` — replace the mock entity by entity with real APIs. The 1:1 mock data spec from this phase is what makes that swap mechanical.
- **UI is partial; another `/14-ui-build` cycle needed:** Re-run this command after the next route is shipped. The log will track each cycle.
- **Signals from the first-three indicate the flow is wrong:** Don't start API work yet. Iterate the UI / mock until the workflow signal looks right. This is the cheapest possible time to find that out.
- **First-three couldn't touch it in time:** Reference the `time-to-first-user-touch` clause in `go-no-go.md` and consider whether the slice was too thick.

Close with one concrete recommendation.
