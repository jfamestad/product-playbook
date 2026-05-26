---
description: Enumerate every read pattern in the app, define the partitioning/tenancy model, identify security-related queries, and lock a cross-tenant access prevention plan. Writes ./artifacts/access-patterns.md.
---

# /product-10-access-patterns

You are helping the user enumerate **every way the application will retrieve data**, before any data model is drawn. The data model in `/product-10` will be *defended by* access patterns — not aesthetics, not whatever schema feels clean. If a model doesn't serve a real access pattern, it doesn't belong.

This phase also covers two cross-cutting concerns:

1. **Partitioning / tenancy model** — what's a tenant, how partitions map to it, hot-partition risk
2. **Security-related queries and cross-tenant access prevention plan** — the schema-level + runtime + test coverage strategy to ensure a user cannot read data they don't own

Team preferences (override per project if needed):
- NoSQL with single-table design when possible
- Specialized stores for patterns that don't fit single-table (search, geo, vectors, blobs)
- **No full table scans** in the hot path. If a pattern requires a scan, redesign the pattern, the key structure, or use a specialized store

---

## Step 1 — Intro the job to be done

Open with this orientation:

> **Access patterns** is where we enumerate every read the app will perform, name the partitioning/tenancy model, and lock the plan that keeps tenants from reading each other's data. The output is the spec that the data model has to satisfy — not the other way around.
>
> Three jobs:
> 1. Enumerate every read pattern, categorized and detailed.
> 2. Define the partitioning/tenancy model.
> 3. Identify security-related queries and lock a cross-tenant access prevention plan.
>
> Defaults: NoSQL single-table when it fits, specialized stores when it doesn't, **no full table scans** in the hot path.
>
> Output: `./artifacts/access-patterns.md`.

## Step 2 — Read context

Required upstream artifact:
- `./artifacts/product-requirements.md` — the feature list and acceptance criteria. Every access pattern derives from something here.

If missing, **block**: enumerating reads without a feature spec produces fiction. Recommend `/product-9-engineering-handoff` first.

Optional but useful:
- `./artifacts/PRFAQ.md` — for the broader context and any user-facing claims that imply reads (e.g., "search across all your leads")
- `./artifacts/mvp-scope.md` — to honor what's IN vs deferred (don't enumerate patterns for deferred features)
- `./artifacts/principles.md` — to keep the work aligned with stated principles
- `./artifacts/gtm-plan.md` — for tenant model implications (e.g., one beta customer per tenant vs. multi-user-per-tenant)

If `./artifacts/access-patterns.md` exists, infer from the user's request whether to continue iterating or start fresh. Ask only if intent is genuinely unclear. Never silently overwrite.

## Step 3 — Inventory entities

Walk the feature list and name the *things* data revolves around. This isn't the data model — it's just naming the nouns.

For each entity, capture:
- **Name** (User, Lead, Workflow, Account, etc.)
- **One-line definition**
- **Ownership** — does it belong to a tenant? a user? an org? nobody (global reference data)?

> Q to user: For each entity, who *owns* it — meaning, who is the natural authorization boundary? If an entity is global / reference (e.g., a list of US states), say so.

The ownership column seeds Step 4 (partitioning) directly. If you can't say who owns an entity, you can't safely partition or authorize it.

## Step 4 — Partitioning & tenancy model

Decide the tenancy model **once**. It applies to every pattern that follows. Capture:

### 4.1 Tenant definition
What is a tenant in this product?

- **Org-level tenancy** — multiple users belong to one org; org is the boundary
- **User-level tenancy** — every user is their own tenant; no sharing
- **Team-level tenancy** — teams within an org are sub-tenants
- **No tenancy** — single-tenant SaaS, or pure public data product
- **Hybrid** — e.g., user-owned data with optional org-level sharing

Pick one and be specific. "We'll figure out tenancy later" is the most expensive answer.

### 4.2 Partition strategy
How does tenancy map to physical/logical partitioning?

- **Tenant ID in partition key** — e.g., `pk=ORG#<id>` so cross-tenant queries are structurally impossible
- **Tenant ID in sort key prefix** — e.g., `sk=ORG#<id>#LEAD#<lead_id>`
- **Hash-distributed within tenant** — for high-volume tenants, distribute writes across N synthetic partitions per tenant
- **Separate tables per tenant** — heavy isolation; usually overkill for MVP
- **Separate databases per tenant** — for regulated/enterprise; usually post-MVP

For NoSQL / DynamoDB single-table: the partition strategy is the spine of the design. Get it right here.

### 4.3 Hot-partition risk
Are any tenants likely to dominate read or write volume? (One whale customer with 100× the activity of others.) If yes, what's the mitigation?

- Hash-distribute writes across N synthetic partitions for that tenant
- Cache at the application layer
- Move that tenant to a dedicated table / cluster
- Accept the risk if MVP scale is small

### 4.4 Cross-tenant rare-but-real cases
Some patterns legitimately cross tenant boundaries. Enumerate them now so they don't get bolted on insecurely later:

- **Admin** — internal team querying across tenants for support
- **Billing** — aggregation of usage across all tenants for invoicing
- **Support** — read-access to a specific tenant by an internal user
- **Platform analytics** — aggregate metrics across tenants for product decisions

For each, note: *who* runs the query, *with what privilege*, and *how it's audited*.

## Step 5 — Enumerate access patterns per feature

Walk every feature from `product-requirements.md`. For each feature, ask: **what reads does this feature trigger?**

Categories to check for each feature:
- Page load(s) — what queries fire when this page renders?
- User actions — what reads happen on button clicks, form submits, etc.?
- Background jobs — what scheduled or event-driven reads occur?
- API endpoints — what does each endpoint return, and from where?
- Reports / dashboards — what aggregations or list views are computed?
- Search / discovery — what search or filter operations are exposed?
- Notifications — what reads happen when generating an alert/email?

Capture each pattern with a plain-English name. Examples:
- "Get all leads for the authenticated user in the last 30 days, sorted newest first"
- "Search workflows by name for the user's tenant"
- "Aggregate count of leads by status for the dashboard tile"

Don't worry about classification yet — that's Step 6. Just enumerate.

Be thorough. A missed access pattern at this stage becomes a data model retrofit at `/product-8` and a query refactor at build time.

## Step 6 — Categorize each pattern

Tag each pattern with its retrieval type:

- **Key lookup** — single item by ID
- **Range query** — items in a sorted range (date range, alphabetical, etc.)
- **Sort by attribute** — list sorted by something other than primary key
- **Filter by attribute** — list where attribute matches / is in set
- **Text search** — full-text or keyword search on content
- **Semantic search** — vector/embedding-based similarity search
- **Aggregation** — count, sum, group-by
- **Search + filter / faceted** — combined search and filter (e.g., "search leads, faceted by status and region")
- **Geo** — radius, bounding box, polygon
- **Recent N** — top N by recency
- **Graph traversal** — connected entities, paths
- **Other** — describe

A pattern can be tagged with multiple categories (e.g., "search + filter").

## Step 7 — Per-pattern detail

For each pattern, fill out:

- **Pattern name** + plain-English description
- **Trigger** — user action / page load / job / API call
- **Input parameters** — what gets passed in
- **Output shape** — single item / list / paginated list / aggregate value
- **Volume / frequency** — reads per second or per day (rough order of magnitude is fine)
- **Latency requirement** — e.g., "sub-100ms for UI," "batch OK," "<500ms p95"
- **Consistency requirement** — eventual OK, or strong consistency needed
- **Recommended store + key structure** — be specific:
  - For DynamoDB single-table: which table, `pk=<...>`, `sk=<...>`, which GSI if used, what GSI keys
  - For OpenSearch / search store: which index, which fields searched
  - For S3: bucket and key pattern
  - For cache: layer + TTL
- **Tenant scope** — which tenant boundary applies, or explicitly "cross-tenant by design (admin/billing/support/etc.)"
- **Authorization check** — *how* the boundary is enforced for this pattern (e.g., "tenant ID always taken from authenticated session and included in PK; impossible to query other tenants")
- **Full-table-scan avoidance** — explicitly: what's the path that avoids scanning? If the pattern would naively scan, redesign or pick a different store.

Don't accept "we'll figure out the keys later." The whole point of this phase is locking the keys.

## Step 8 — Security-related queries

Tag patterns by security category. A pattern can have multiple tags.

### 8.1 Auth surface
Patterns involved in authentication: login, password reset, MFA, session validation, OAuth callbacks, API key validation. These typically don't carry a tenant ID *yet* (they establish identity) — they're the patterns that *produce* the tenant scope used by everything else.

### 8.2 Permission checks
Patterns for RBAC, role assignment, capability checks: "Can user X perform action Y on resource Z?" These often touch a role/permission entity that is itself a partition concern.

### 8.3 Cross-tenant by design
Admin, support, billing aggregation, platform analytics. The patterns that *legitimately* cross tenant boundaries. Each should be explicitly named and accountable.

### 8.4 Tenant-strict
The default for everything else. These patterns **must never** cross a tenant boundary. The cross-tenant access prevention plan (Step 10) targets this category.

### 8.5 PII / sensitive data
Patterns that touch regulated data (PII, PHI, payment data, etc.). Track separately because they often have additional constraints: encryption at rest with specific key management, audit logging of every read, data residency requirements, retention limits.

For each pattern in 8.5, note the data category and any specific compliance constraint (GDPR, HIPAA, CCPA, PCI, RESPA, etc.).

## Step 9 — Store selection summary

Group patterns by store. For each store:

- **Store choice** (DynamoDB single-table / OpenSearch / S3 Vectors / S3 / Redis / Postgres / etc.)
- **Patterns served** — list the patterns this store handles
- **Why this store fits** — one-line justification
- **Key structures** — for the main store, full PK/SK and GSI key designs
- **Data sync requirements** — if this store is denormalized from another (e.g., search index from DynamoDB), how does it stay in sync? Streams? Outbox pattern? Batch?

If the design lands on more than 3–4 stores at MVP, push back: more stores = more sync complexity = more operational toil. Is each one earning its place?

## Step 10 — Audits

### 10.1 Full-table-scan audit
For every pattern in Step 7, confirm the scan-avoidance path. If any pattern still requires a scan in the hot path, list it explicitly:

```
- Pattern: <name>
  - Scan reason: <why this would scan>
  - Resolution: <key redesign / denormalization / secondary store / accept-and-document>
```

If a scan is unavoidable and the volume is small enough to accept (e.g., an admin list of < 1000 entities), document it explicitly — don't paper over it.

### 10.2 Cross-tenant access prevention plan
This is a security artifact. Four layers, all required:

**Schema-level enforcement.** How the partition design makes cross-tenant access structurally impossible (or limited to the cross-tenant-by-design list from 4.4). State it concretely:

> Example: "All tenant-strict entities have `pk` that starts with `ORG#<org_id>`. The application-layer query builder accepts a `tenant_id` from the authenticated session and prefixes it onto every PK. There is no code path that constructs a query PK without a tenant_id, and a runtime assertion verifies this. The only patterns that omit the prefix are the cross-tenant-by-design patterns in 4.4, each of which uses a separate elevated-privilege query builder."

**Runtime enforcement.** Middleware / handler-level pattern that:
- Extracts tenant ID from the authenticated session/token (not from the request body or URL)
- Injects it into every query
- Validates query results before returning (each result item's tenant ID matches the session tenant ID)
- Logs and rejects any mismatch

**Test coverage.** Named negative tests:
- "Authenticated as tenant A, attempt to fetch a known tenant-B resource by ID — expect 404 or 403"
- "Authenticated as tenant A, attempt to list with filter that would match tenant B records — expect zero results"
- "Authenticated as tenant A, attempt to write to tenant B's partition — expect deny"
- Per cross-tenant-by-design pattern: "Authenticated as non-admin, attempt the admin pattern — expect 403"

These tests must exist and run on every build. Named explicitly in the artifact.

**Audit logging.** What gets logged for security-relevant events:
- Every cross-tenant-by-design query (admin/support/billing) — who, when, what query, what tenant
- Every rejected cross-tenant attempt (potential attack signal)
- Every privilege escalation (role grant/revoke)
- Where the logs go and how long retained

**Privileged escape hatches.** For the cross-tenant-by-design patterns: how is the privilege granted (role? feature flag? separate admin app?), how is it audited, how is it revoked?

## Step 11 — Write `./artifacts/access-patterns.md`

Use this structure exactly:

```markdown
# Access Patterns — <Product name>

*Draft date: YYYY-MM-DD*

## Entities

| Entity | Definition | Ownership |
|---|---|---|
| <...> | <...> | <tenant / user / org / global> |

---

## Partitioning & tenancy model

**Tenant definition:** <...>

**Partition strategy:** <...>

**Hot-partition risk:** <...>

**Cross-tenant rare-but-real cases:**
- <Pattern name> — runs as <role>, audited via <mechanism>
- ...

---

## Access patterns

### <Pattern 1 name>
- **Description:** <...>
- **Trigger:** <...>
- **Inputs:** <...>
- **Output shape:** <...>
- **Volume / frequency:** <...>
- **Latency requirement:** <...>
- **Consistency:** <...>
- **Category:** <key-lookup / range / search / aggregation / ...>
- **Recommended store + keys:** <e.g., DynamoDB main table, `pk=ORG#<id>` `sk=LEAD#<created_at>#<id>`>
- **Tenant scope:** <tenant-strict / cross-tenant-by-design / auth-surface / etc.>
- **Authorization check:** <how the boundary is enforced>
- **Full-table-scan avoidance:** <the path>

### <Pattern 2 name>
<same structure>

...

---

## Security-related queries

### Auth surface
- <pattern names>

### Permission checks
- <pattern names>

### Cross-tenant by design
- <pattern names> — each with role/audit notes

### Tenant-strict
- (default — all patterns not in the above three)

### PII / sensitive
- <pattern name> — <data category> — <compliance constraint>

---

## Store selection

### <Store 1 — e.g., DynamoDB single-table>
- **Patterns served:** <list>
- **Why this store:** <...>
- **Key structure:**
  - Main table: `pk=<...>` `sk=<...>`
  - GSI1: `gs1pk=<...>` `gs1sk=<...>` — serves <patterns>
  - GSI2: <...>
- **Sync requirements:** <if any>

### <Store 2 — e.g., OpenSearch>
- **Patterns served:** <list>
- **Why this store:** <...>
- **Indexes:** <name + fields searched>
- **Sync from primary:** <streams / outbox / batch>

...

---

## Audits

### Full-table-scan audit
- All patterns confirmed scan-free: <yes / list exceptions>
- Accepted scans (documented): <list with justification>

### Cross-tenant access prevention plan

**Schema-level enforcement:** <...>

**Runtime enforcement:** <middleware/handler pattern>

**Test coverage (required negative tests):**
- <test name 1>
- <test name 2>
- ...

**Audit logging:**
- <events logged + destination + retention>

**Privileged escape hatches:**
- <pattern> — granted via <mechanism>, audited via <mechanism>, revoked via <mechanism>
```

Read the structure back to the user in summary form before writing — especially the partitioning decision, the security category counts, and the cross-tenant prevention plan. Confirm. Then write.

## Step 12 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with this header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append:

```
YYYY-MM-DD HH:MM PST — /product-10-access-patterns — ./artifacts/access-patterns.md
```

Use the actual current local PST time. If unsure of the timezone, ask once and remember.

## Step 13 — Tell them what's next

Close with:

> Next:
> - `/product-11-data-model` — draw the data model defended by these access patterns.
> - Or `/product-7-stress-test ./artifacts/access-patterns.md` if the cross-tenant prevention plan, hot-partition story, or security-query inventory feels soft. The Technical + Operator + (security) POVs are the right lineup.
