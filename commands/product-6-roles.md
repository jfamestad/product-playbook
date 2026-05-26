---
description: Decide solution roles, user groups/tenancy, permissions, and operational features for the MVP. Repeatable conversation framework — each area asks "in MVP or deferred?", and "deferred + manual workaround" is a valid answer. Writes ./artifacts/roles.md.
---

# /product-6-roles

You are helping the user decide what role structure, tenancy model, permissions, and operational features the MVP needs. The trap this phase prevents: silent assumption that "of course we need an admin panel / audit log / SSO / etc." Those are real features with real cost. Each one must earn its place at MVP — or be explicitly deferred with a manual workaround.

This phase is **a conversation, not a checklist**. Four areas, each walked with the same pattern:

1. What's the question?
2. What does the user want at MVP?
3. If they want it: what's the minimum that ships?
4. If they don't want it: what's the manual workaround, and what's the re-eval trigger?

"Out at MVP" is a valid, expected answer for most rows. The artifact records both what's IN and what's OUT (with the workaround) so nothing is silently assumed.

Outputs feed downstream:
- `/product-7-stress-test` may attack the role/tenancy model
- `/product-9-engineering-handoff` carries the in-scope features forward as engineering work
- `/product-10-access-patterns` derives security queries and tenancy model from this
- `/product-11-data-model` derives the org/user/role entities from this

---

## Step 1 — Intro the job to be done

Open with this orientation:

> **Roles & operational scope** decides what role structure, tenancy model, permissions, and operational features the MVP actually needs. Four areas — roles, groups/tenancy, permissions, operational features — each walked with the same pattern: in-MVP or deferred-with-manual-workaround. "Deferred" is a valid answer for most rows; the point is to make every decision conscious so nothing is silently assumed.
>
> The artifact this produces feeds the stress test, the engineering handoff, the access-patterns phase (which derives security queries from your tenancy model), and the data model (which derives org/user/role entities from it).
>
> Output: `./artifacts/roles.md`.

## Step 2 — Read context

Required upstream artifact:
- `./artifacts/mvp-scope.md` — the IN/OUT lists; constraints

If missing, **block**: deciding roles without a locked scope produces over-built fiction. Recommend `/product-5-mvp-scope` first.

Strongly recommended:
- `./artifacts/gtm-plan.md` — the top-3 customers shape what "tenant" actually means. A solo operator and an enterprise team imply very different role models.
- `./artifacts/principles.md` — any principles that bind operational choices (e.g., surface-assumptions-give-override often implies more visible operational controls)

Optional:
- `./artifacts/PRFAQ.md` — for narrative context
- `./artifacts/design/index.md` — if design exists, it may have already shown admin UI patterns

If `./artifacts/roles.md` exists, infer from the user's request whether to continue iterating or start fresh. Ask only if intent is genuinely unclear. Never silently overwrite.

## Step 3 — Roles conversation

The question to answer: **who logs in, and do we need more than one type of role at MVP?**

Walk these questions in order:

### 3.1 Who logs in at MVP?
List every type of human who will have a login at MVP. Examples:
- The customer's primary user (e.g., the operator running the workflow)
- A teammate of the primary user
- The customer's manager / approver
- Our internal team (support, sales) — covered later under operational features, not here
- A read-only stakeholder

For each: name the role in user-language, not technical-language. "Sales rep" not "User". "Hiring manager" not "Reviewer".

### 3.2 Do we need more than one role at MVP?
Default: **start with one role.** Multi-role at MVP is expensive — UI, permission checks, role-assignment flow, role-changing flow. If the top-3 customers can use the product with everyone as the same role, ship that.

Push back if the user reflexively wants Admin + Member + Viewer. Ask:
- "At MVP, does any top-3 customer need to *prevent* one of their teammates from doing something?"
- "Could we ship with everyone as a Member and add roles later when a paying customer asks?"

If the answer is "yes, role separation is load-bearing at MVP," capture exactly which customer and which capability needs to be separated. That's the only justification for multi-role at MVP.

### 3.3 Scope of roles — global vs per-group
Only ask if multi-role is in scope. Two patterns:
- **Global role:** "Maria is an Admin." Single role across the whole tenant.
- **Per-group role:** "Maria is an Admin of Team A and a Viewer of Team B." Role attached to a group membership.

Per-group is significantly more complex. Default to global at MVP unless a top-3 customer explicitly needs per-group separation.

### 3.4 Capture
For each in-scope role:
- Name (user-language)
- One-sentence description of who this person is
- Why they need to be a separate role (the capability that distinguishes them)

## Step 4 — Groups & tenancy conversation

The question to answer: **what's a tenant, and how do users join one?**

This is the load-bearing decision for everything downstream. Get it right here or pay later in `/product-10-access-patterns` and `/product-11-data-model`.

### 4.1 What is a tenant?
Define it in plain English. Common shapes:
- **Per-customer-org:** each customer company is one tenant. Users belong to one org. Default for B2B.
- **Per-workspace:** like Notion — a user can belong to multiple workspaces. Each workspace is a tenant.
- **Per-user (single-tenant-per-user):** each user is their own tenant. Default for B2C and prosumer.
- **Per-project:** like a board in Trello — finer grain than workspace.

Pick one. If the user wants to defer the decision, **push back** — the data model and security queries can't be defined without this.

### 4.2 Multi-tenant at MVP or single-tenant per customer?
Two flavors:
- **Multi-tenant infrastructure:** one shared database with tenant-scoped queries. Standard B2B SaaS default.
- **Single-tenant per customer:** one instance per customer. Higher ops cost; sometimes required for compliance or by enterprise contract.

Multi-tenant infrastructure is almost always right for MVP. Capture which one and why.

### 4.3 How do users join a tenant?
Walk the options:
- **Invite only:** existing user emails an invite to a new user.
- **Self-serve sign-up + create tenant:** new user signs up, creates their own tenant.
- **Self-serve sign-up + join existing tenant by domain:** e.g., anyone with `@acme.com` joins the Acme tenant.
- **SSO provisioning:** users appear via SSO/SCIM (typically deferred — enterprise feature).

Pick the minimum that lets the top-3 customers actually onboard. Often: self-serve + invite at MVP, SSO deferred.

### 4.4 Can a user be in multiple tenants?
- B2B per-customer-org: usually no (one work email, one org).
- Workspace model: yes, common.

Cheaper at MVP: a user is in exactly one tenant. If multi-tenancy-per-user is required, capture which customer needs it.

### 4.5 Capture
- Tenant definition (one sentence)
- Multi-tenant infra vs single-tenant per customer (and why)
- Join paths in scope (with each path's why)
- Users-per-tenant model (one or many)

## Step 5 — Permissions matrix

Only build this if multi-role is in scope from Step 3. Otherwise: write "All users are Members. Members can do everything in their tenant. No permission matrix at MVP."

### 5.1 List the capabilities
Capability = a thing a user can do, in user-language. Examples:
- "Create a workflow"
- "Run a workflow"
- "Invite a teammate"
- "Change billing plan"
- "Export data"
- "Delete the tenant"

Pull these from `mvp-scope.md` (the IN list of features). Each in-scope feature implies one or more capabilities.

### 5.2 Map capabilities × roles
Build a simple matrix:

```
| Capability | Member | Admin |
|---|---|---|
| Create a workflow | ✓ | ✓ |
| Invite a teammate |   | ✓ |
| Change billing plan |   | ✓ |
```

Keep it tight — at MVP, the matrix should fit on one screen. If the matrix is sprawling, that's a signal multi-role isn't ready and should be deferred.

### 5.3 Edge cases — capture explicitly
- **Default role for new invitees:** what role does a new user get when invited? (Usually Member.)
- **Role assignment:** who can change another user's role? (Usually Admin.)
- **Last-admin protection:** can the last Admin remove themselves? (Usually no — explicit guard.)
- **Tenant creator:** the user who created the tenant — what role? (Usually Admin, immutable.)

## Step 6 — Operational features

Walk each operational feature. For each: **in MVP or deferred?** If deferred: **what's the manual workaround, and what's the re-eval trigger?**

The pattern per feature:

```
### <Feature name>
**Status:** IN at MVP / Deferred
**If IN:** what's the minimum that ships?
**If deferred:**
  - Manual workaround: <what we do by hand, who does it, how often>
  - Re-eval trigger: <the signal that says "build this now">
```

Walk these features. Skip ones that genuinely don't apply, but ask the question for each so nothing slips through silently.

### 6.1 Admin / operator UI (for our team)
A UI for our team to see all tenants, all users, support a customer, debug an issue.

Default at MVP: **deferred.** Workaround: direct DB queries + scripts + our customer-success engineer talks to customers. Re-eval trigger: "we have >N tenants and DB queries no longer scale" or "we hired a non-engineering support person."

### 6.2 Billing portal (for customer admins)
Customer-facing UI to see plan, usage, invoices, change plan, update payment method.

Default at MVP: depends heavily on the GTM pricing model. If pricing is manual-invoice during beta, the portal is deferred. If self-serve credit-card at MVP, some portal is required.

Workaround if deferred: manual invoice + Stripe link in email + we change plans on behalf of customer when asked.

### 6.3 Audit log
A record of who-did-what, queryable by customer admin (and/or our team).

Default at MVP: **internal-only** — we log structured events to CloudWatch / equivalent, no customer-facing audit UI. Re-eval trigger: first customer asks for SOC 2 or asks to see audit log.

Distinguish three levels:
- **Logged for ops** (always at MVP): structured events to logs for our debugging.
- **Queryable by us** (often at MVP): tagged so we can grep when a customer asks "what happened?"
- **Customer-facing audit UI** (usually deferred): in-app screen the customer admin can view.

### 6.4 Support impersonation / assume-user
Lets our support engineer "log in as" a customer user to reproduce an issue, see what they see, take an action on their behalf.

Default at MVP: **deferred.** Workaround: customer screen-shares with us; we ask them to perform the action. Re-eval trigger: "we have >N tenants and screen-share is no longer feasible."

If IN: must include audit logging of impersonation events. Don't skip this — impersonation without audit is a compliance landmine.

### 6.5 Data export
Customer can export their data — formats: CSV, JSON, full archive.

Default at MVP: depends on regulatory scope and customer expectation. Often **deferred** with a manual workaround (we run a query, send a file). Re-eval trigger: first customer asks for self-serve export, or first regulatory request (GDPR data portability).

### 6.6 Account / tenant deletion
Customer can delete their tenant and all associated data.

Default at MVP: **manual.** Workaround: customer emails us, we run a script. Re-eval trigger: GDPR/CCPA scope, or customer needs self-serve deletion before signing.

Capture the deletion semantics: hard delete, soft delete + N-day retention, archive-then-purge. This decision propagates to `/product-11-data-model` lifecycle decisions.

### 6.7 Org settings
- SSO (SAML, OIDC)
- SCIM provisioning
- IP allowlist
- Custom domain
- Configurable session timeout
- Per-tenant data residency

Default at MVP: **all deferred** unless a top-3 customer explicitly named one as a deal-blocker. Each of these is weeks of work. Re-eval trigger per item: a paying customer makes it a hard requirement.

### 6.8 Sign-on experience
The business-level sign-on decision (not the technology — that's an implementation choice for engineering). Capture:
- Email + password? Magic link? Google/Microsoft social? Enterprise SSO?
- Default at MVP: pick one that covers the top-3 customers; defer the rest.
- Account recovery: how does a user reset access?

This decision propagates into `/product-10-access-patterns` (auth queries) and `/product-12-prebuild-qa` (where the technology choice is made).

## Step 7 — Out-of-scope (defer list)

Consolidate everything explicitly deferred from Steps 3–6 into one list. For each:

- **Feature** — what it is
- **Workaround at MVP** — what we do instead
- **Re-eval trigger** — the specific signal that says "build this now"
- **Estimated cost when we do build it** — rough effort (S/M/L/XL) so future-us can plan

This list is the single most important defense against scope creep during build. When someone says mid-build "should we add SSO?", the answer is on this list: deferred, customer didn't ask for it, trigger is X.

## Step 8 — Downstream references

Call out explicitly what this artifact pushes downstream — so the user sees the chain and so the later phases can pull from a known section.

```
## Feeds downstream

- **`/product-7-stress-test`** (if invoked on this artifact): Customer + Sponsor + Operator POVs are sharpest. Watch for "this tenancy model breaks at customer #2" and "this operational deferral becomes a fire at first scale event."
- **`/product-9-engineering-handoff`**: in-scope operational features become engineering work items with prioritization tier.
- **`/product-10-access-patterns`**:
  - Tenancy model → partitioning strategy
  - Roles + capabilities → permission-check queries
  - Sign-on choice → auth queries
  - Multi-tenant decision → cross-tenant prevention plan
- **`/product-11-data-model`**:
  - Tenant entity (Org, Workspace, etc.)
  - User entity + tenant membership
  - Role entity (if multi-role)
  - Audit log entity (if customer-facing)
  - Deletion semantics → lifecycle decisions per entity
```

## Step 9 — Write `./artifacts/roles.md`

Use this structure exactly:

```markdown
# Roles, tenancy & operational scope — <Product name>

*Draft date: YYYY-MM-DD*
*Source artifacts: mvp-scope.md, gtm-plan.md, principles.md*

## Roles

**Scope:** single-role / multi-role-global / multi-role-per-group
**Justification:** <why this scope at MVP, tied to specific top-3 customer need>

### In-scope roles at MVP
| Role | Who they are | Distinguishing capability |
|---|---|---|
| <Name> | <one sentence> | <why they exist as a separate role> |

---

## Tenancy

**Tenant definition:** <one sentence>
**Infrastructure:** multi-tenant shared / single-tenant per customer
**Why:** <...>

**Join paths in scope:**
- <Path 1> — <why>
- ...

**Users per tenant:** one tenant per user / multiple tenants per user
**Why:** <...>

---

## Permissions matrix

<If single-role: "All users are Members. Members can do everything in their tenant. No permission matrix at MVP.">

<If multi-role:>
| Capability | <Role 1> | <Role 2> | ... |
|---|---|---|---|
| <Capability 1> | ✓ |   |   |
| ...            |   |   |   |

**Edge cases:**
- Default role for new invitees: <...>
- Role assignment authority: <...>
- Last-admin protection: <...>
- Tenant creator role: <...>

---

## Operational features

### Admin / operator UI
**Status:** <IN / Deferred>
<IN: what ships> / <Deferred: workaround + re-eval trigger>

### Billing portal
**Status:** <IN / Deferred>
<...>

### Audit log
**Status:** <Logged for ops / Queryable by us / Customer-facing UI>
<...>

### Support impersonation
**Status:** <IN / Deferred>
<...>

### Data export
**Status:** <IN / Deferred>
<...>

### Account / tenant deletion
**Status:** <Manual / Self-serve>
**Deletion semantics:** <hard / soft + N-day retention / archive-then-purge>

### Org settings (SSO, SCIM, IP allowlist, custom domain, session timeout, data residency)
**Status:** all deferred unless noted below.
<List any that are IN, with the top-3 customer who required it.>

### Sign-on experience
**Method at MVP:** <email+password / magic link / Google / Microsoft / SSO>
**Other methods deferred:** <list>
**Account recovery:** <approach>

---

## Out-of-scope (defer list)

| Feature | Workaround at MVP | Re-eval trigger | Est. cost to build |
|---|---|---|---|
| <...> | <...> | <...> | <S/M/L/XL> |
| ... | ... | ... | ... |

---

## Feeds downstream

- **`/product-7-stress-test`**: Customer + Sponsor + Operator POVs sharpest here.
- **`/product-9-engineering-handoff`**: in-scope operational features become engineering work items.
- **`/product-10-access-patterns`**:
  - Tenancy model → partitioning strategy
  - Roles + capabilities → permission-check queries
  - Sign-on choice → auth queries
  - Multi-tenant decision → cross-tenant prevention plan
- **`/product-11-data-model`**:
  - Tenant, User, Role, Audit-log entities (those that are in scope)
  - Deletion semantics → lifecycle decisions per entity
```

Read the final draft back to the user in summary form before writing — especially the role scope, tenant definition, the deferred-features list, and any operational features that are IN. Confirm. Then write the file.

## Step 10 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with this header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append:

```
YYYY-MM-DD HH:MM PST — /product-6-roles — ./artifacts/roles.md
```

Use the actual current local PST time. If unsure of the timezone, ask once and remember.

## Step 11 — Offer toolchain integration

Read `./artifacts/toolchain.md` if it exists. Look at the **Docs / wiki** value.

If a docs/wiki tool is named (Confluence, Notion, Google Docs) AND its MCP is available, offer: "Want me to publish roles.md to your <tool>?" The permission matrix in particular tends to get referenced by engineering and ops, so a wiki home for it pays off. If yes, push as additive sync and add a `published_url:` line near the top of the local file. Local markdown remains source of truth.

If no tool is named, "(not yet)", or MCP unavailable, skip silently.

See the playbook SKILL's "Toolchain integration" convention for the full pattern.

## Step 12 — Tell them what's next

Close with:

> Next:
> - `/product-7-stress-test ./artifacts/mvp-scope.md` — the natural antagonist gate after scope + roles. Lineup: Sponsor, Technical, Operator, Customer. The Operator POV will hammer at the operational-deferrals list, which is exactly the cheap-to-fix-now feedback you want.
> - Or `/product-7-stress-test ./artifacts/roles.md` directly if the tenancy model, permission matrix, or operational-deferrals list felt soft on its own. Customer + Sponsor + Operator POVs are sharpest.
> - Then `/product-9-engineering-handoff` to package scope + roles for build.
