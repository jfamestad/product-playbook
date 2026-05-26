---
description: Draw the data model defended by the access patterns — entity ER view, physical single-table layout with example object instances, query implementations, lifecycle decisions, and migration plan for deferred features. Writes ./artifacts/data-model.md.
---

# /product-11-data-model

You are helping the user produce the data model that the application will be built on. The model is **defended by the access patterns** from `/product-9` — if a structural choice doesn't serve a real access pattern, it doesn't belong.

The artifact has two complementary views:

1. **Entity view (ER)** — abstract relationships. What data exists, how entities relate.
2. **Physical view (single-table)** — actual stored rows with example instances, key schema, GSI keys. The thing the app reads/writes.

Plus three more sections:

3. **Query implementations** — for every access pattern in `/product-9`, show the actual query.
4. **Lifecycle decisions per entity** — TTL, archival, deletion, tenant-offboarding.
5. **Migration plan for deferred features** — for each item in the MVP OUT list, classify as additive or migration-required, with a plan that makes sense.

Team preferences (override per project):
- NoSQL single-table when it fits (DynamoDB-style)
- Abstract key names: `pk`, `sk`, `gs1pk`, `gs1sk`, `gs2pk`, `gs2sk`, etc.
- Entity prefixes: `USER#`, `ORG#`, `LEAD#`, etc.
- Specialized stores when single-table doesn't fit (search, vectors, blobs)

---

## Step 1 — Intro the job to be done

Open with this orientation:

> **Data model** is where the access patterns become a concrete schema. The artifact has two views — an entity-level ER view for shared mental model, and a physical single-table view that's what actually gets read/written. Plus query implementations (one per access pattern), lifecycle decisions per entity, and a migration plan for the deferred features so we know which can be added later without re-architecting.
>
> The model is defended by the access patterns. If a structural choice doesn't serve one, it doesn't belong.
>
> Output: `./artifacts/data-model.md`.

## Step 2 — Read context

Required upstream artifact:
- `./artifacts/access-patterns.md` — the patterns the model must satisfy

If missing, **block**: a data model without enumerated access patterns produces fiction. Recommend `/product-10-access-patterns` first.

Useful:
- `./artifacts/product-requirements.md` — entity context, feature constraints
- `./artifacts/mvp-scope.md` — OUT list for the migration story (Step 8)
- `./artifacts/principles.md` — keep aligned with stated principles

If `./artifacts/data-model.md` exists, infer from the user's request whether to continue iterating or start fresh. Ask only if intent is genuinely unclear. Never silently overwrite.

## Step 3 — Entity view (ER)

Draw the abstract relationships. For each entity from `/product-7`:

- **Name** — `User`, `Lead`, `Workflow`, etc.
- **Definition** — one sentence
- **Owns** — what entities does it own (1:N or 1:1 relationships)
- **References** — what entities does it reference (N:1 or N:M)
- **Cardinality notes** — typical scale per parent (a user has ~100 leads, an org has ~10 users, etc.)

Express relationships in plain English or arrow notation:

```
Org (1) ─owns─→ (N) User
User (1) ─owns─→ (N) Lead
Lead (N) ─references─→ (1) Source
```

This view exists for the shared mental model. It is NOT the schema — the physical view is.

## Step 4 — Physical view (single-table layout)

For the primary store (single-table NoSQL default), walk each entity and capture:

### Per entity:
- **Item type** — the `type` attribute value (e.g., `"LEAD"`)
- **Key schema** — `pk` pattern, `sk` pattern. Use `${...}` placeholders for variable parts.
- **GSI participation** — for each GSI this entity populates: `gsXpk`, `gsXsk` patterns. Sparse if conditional.
- **Attributes** — full list with type and required/optional.
- **Example object instance** — a realistic, fully-populated row with actual-looking values. This serves as the **mock data spec**: every field here maps 1:1 to the field that will exist at MVP.

### Example block (illustrative — real artifact uses your actual entities):

```
### Entity: Lead
- type: "LEAD"
- pk: "ORG#${org_id}"
- sk: "LEAD#${created_at_iso}#${lead_id}"
- gs1pk: "ORG#${org_id}#STATUS#${status}"
- gs1sk: "LEAD#${created_at_iso}#${lead_id}"
- Attributes:
  - lead_id (string, required, ULID)
  - org_id (string, required)
  - created_at (string, ISO-8601, required)
  - status (string, enum: new|qualified|won|lost, required)
  - source (string, required, references Source)
  - contact_email (string, optional)
  - contact_phone (string, optional)
  - score (number, optional, 0–100)
  - assigned_user_id (string, optional, references User)
  - notes (string, optional, max 10000 chars)

- Example:
  {
    "pk": "ORG#org_abc123",
    "sk": "LEAD#2026-05-26T14:32:00Z#lead_ZX9...",
    "gs1pk": "ORG#org_abc123#STATUS#qualified",
    "gs1sk": "LEAD#2026-05-26T14:32:00Z#lead_ZX9...",
    "type": "LEAD",
    "lead_id": "lead_ZX9YA1BCDE",
    "org_id": "org_abc123",
    "created_at": "2026-05-26T14:32:00Z",
    "status": "qualified",
    "source": "src_naics_realtors_v1",
    "contact_email": "maria@chenrealty.example",
    "contact_phone": "+1-916-555-0142",
    "score": 78,
    "assigned_user_id": "user_jvb...",
    "notes": "Spoke 5/24 — interested in Q3 pilot."
  }
```

Repeat for every entity. The examples must be realistic enough that someone could hand them to a frontend developer and they'd build a working UI.

## Step 5 — GSI design

For each GSI in the single table:

- **GSI name** (e.g., `gs1`)
- **Purpose** — which access patterns from `/product-7` it serves
- **Key structure** — `gsXpk` and `gsXsk` patterns
- **Sparse or full** — full means every item in the table populates this GSI; sparse means only items with the GSI keys are indexed (saves cost, common pattern)
- **Projection** — keys-only / include specific attributes / all attributes. Keys-only is cheaper but requires a second read to hydrate; all is more expensive but single-read.

If the design has more than 3–4 GSIs at MVP, push back: each GSI is a separate copy of the data. Can two GSIs be combined via overloading?

## Step 6 — Query implementations

For every access pattern enumerated in `/product-7`, show how it executes. This is the validation that the model satisfies the patterns.

### Per pattern:

- **Pattern name** (from `/product-7`)
- **Target** — main table or `gsX`
- **Key conditions** — what's passed to the query
- **Filter (optional)** — server-side filter expression if any
- **Projection** — what attributes are returned
- **Pagination** — strategy (LastEvaluatedKey / cursor / offset)
- **Pseudocode** — the actual call signature (illustrative)

### Example block:

```
### Pattern: List qualified leads for an org, newest first (paginated, 20/page)

- Target: gs1 (status-indexed GSI)
- Key conditions:
  - gs1pk = "ORG#${org_id}#STATUS#qualified"
- Filter: none
- Projection: all
- Pagination: LastEvaluatedKey cursor
- Sort: descending on gs1sk (newest first because timestamp embedded)

Pseudocode:
  query(table, IndexName="gs1",
        KeyConditionExpression="gs1pk = :pk",
        ExpressionAttributeValues={":pk": f"ORG#{org_id}#STATUS#qualified"},
        ScanIndexForward=False,
        Limit=20,
        ExclusiveStartKey=cursor)
```

**Critical:** every access pattern in `/product-7` must have an implementation here. Run the validation pass in Step 9 to confirm zero gaps.

## Step 7 — Specialized stores

For patterns not served by the primary single-table store (text search, vector search, blob storage, time-series, cache), describe each store's schema:

- **Store** — OpenSearch / S3 Vectors / S3 / Redis / Postgres / etc.
- **Patterns served** — list pattern names from `/product-7`
- **Schema** — for search: index name, fields, analyzers; for blob: bucket, key pattern; for cache: key format, TTL
- **Sync from primary** — how this store stays consistent with the single-table (DynamoDB Streams + Lambda? Outbox? Periodic batch? Application-level dual-write?)
- **Eventual consistency window** — typical lag (200ms? 30s? 5min?)

## Step 8 — Lifecycle decisions (discuss inline with user)

Walk each entity and decide:

- **TTL** — does this item auto-expire? After how long? (For DynamoDB, this is a per-item `ttl` attribute with epoch seconds.)
- **Archival** — does it move to cold storage (S3) after N months? Why?
- **Delete semantics** — soft delete (flag) vs. hard delete?
- **Tenant offboarding** — when a tenant leaves, cascade delete? Retain for N days? Move to legal hold? What's the customer's data export path?

Don't manufacture lifecycle policies for entities that don't need them. For each entity, the right answer might be "no TTL, hard delete via admin only" — capture it explicitly so it's a decision, not a default.

Common cases to consider:
- Session tokens → short TTL (hours)
- Audit logs → retain for compliance window, then archive
- Soft-deleted items → hard delete after N days
- Workflow run history → archive after N months
- Tenant-offboarding → 30-day retention then hard delete (or per customer agreement)

## Step 9 — Validation pass

Mechanical sanity check before writing the artifact:

- **Every access pattern from `/product-7` has an implementation in Step 6.** List any gaps. Either add the implementation, or revise the access patterns (something is wrong if the model can't serve a stated pattern).
- **Every example object instance in Step 4 populates the keys it claims to.** No example missing a required GSI key. No example with attributes that aren't in the attribute list.
- **Every GSI in Step 5 is referenced by at least one query implementation in Step 6.** An unused GSI is dead cost.
- **Every specialized store in Step 7 has a sync path defined.** A store that's not synced from anywhere will drift.

Fix all gaps before writing. The validation pass is the cheapest place to find errors.

## Step 10 — Migration plan for deferred features

For each item in the MVP scope's OUT list, walk through the schema change required to add it later. Classify each:

- **Additive** — adding the feature requires only new item types in existing partitions, or new optional attributes on existing items. No backfill. No key restructure. Zero downtime.
- **Migration-required** — adding the feature requires key restructure, data backfill, or index changes. Plan needed.

For each migration-required deferred feature, sketch the plan:

- What changes (new entity, new keys, new GSI, attribute reshape)
- Backfill strategy (one-time job? dual-write window? lazy migration on read?)
- Cost / time estimate (rough)
- Risk (data loss, hot partitions during backfill, downtime, customer-visible behavior change)
- Mitigation (chunked backfill, feature flag the new path, snapshot before backfill, etc.)

If any deferred feature would force a fundamental re-architecture (e.g., changing from user-tenancy to org-tenancy), flag it loudly. That's the kind of thing to either commit to NOW (and pay the cost up front) or to make a deliberate decision to defer with eyes open.

## Step 11 — Write `./artifacts/data-model.md`

Use this structure:

```markdown
# Data Model — <Product name>

*Draft date: YYYY-MM-DD*
*Defended by: ./artifacts/access-patterns.md*

## Entity view (ER)

| Entity | Definition | Owns | References | Cardinality |
|---|---|---|---|---|
| <...> | <...> | <...> | <...> | <...> |

Relationships:
```
<Org (1) → (N) User>
<User (1) → (N) Lead>
...
```

---

## Physical view (single-table)

### Entity: <Name>
- **type:** "<TYPE>"
- **pk:** `<pattern>`
- **sk:** `<pattern>`
- **gs1pk / gs1sk:** `<pattern>` (if any)
- **gs2pk / gs2sk:** `<pattern>` (if any)
- **Attributes:**
  - <attr> (type, required/optional, notes)
  - ...
- **Example:**
```json
{
  "pk": "...",
  "sk": "...",
  ...
}
```

(Repeat per entity)

---

## GSI design

### gs1 — <purpose>
- **Patterns served:** <list>
- **Key structure:** `gs1pk = <pattern>`, `gs1sk = <pattern>`
- **Sparse / full:** <...>
- **Projection:** <keys-only / include / all>

(Repeat per GSI)

---

## Query implementations

### Pattern: <name from /product-7>
- **Target:** <main table / gsX>
- **Key conditions:** <...>
- **Filter:** <...>
- **Projection:** <...>
- **Pagination:** <...>

Pseudocode:
```
<query call>
```

(Repeat per access pattern — every pattern from /product-7 must appear)

---

## Specialized stores

### <Store name — e.g., OpenSearch>
- **Patterns served:** <list>
- **Schema:** <...>
- **Sync from primary:** <...>
- **Consistency window:** <typical lag>

(Repeat per specialized store)

---

## Lifecycle decisions

| Entity | TTL | Archival | Delete semantics | Tenant offboard |
|---|---|---|---|---|
| <...> | <...> | <...> | <...> | <...> |

---

## Migration plan for deferred features

### <Deferred feature name (from MVP OUT list)>
- **Classification:** <additive / migration-required>
- **Schema changes:** <...>
- **Backfill strategy:** <...> (if migration-required)
- **Cost / time estimate:** <...>
- **Risk:** <...>
- **Mitigation:** <...>

(Repeat per deferred feature)

---

## Validation
- All access patterns from /product-7 have query implementations: <yes / list gaps>
- All example instances populate claimed keys: <yes / list issues>
- All GSIs referenced by at least one query: <yes / list unused>
- All specialized stores have sync paths: <yes / list issues>
```

Read the structure back to the user in summary form before writing — especially the GSI count, the validation pass results, and any migration-required deferred features. Confirm. Then write.

## Step 12 — Append to the log

If `./artifacts/product-log.md` does not exist, create it with this header:

```
# Product log

Append-only record of phase completions. Timestamps in local PST.
```

Then append:

```
YYYY-MM-DD HH:MM PST — /product-11-data-model — ./artifacts/data-model.md
```

Use the actual current local PST time. If unsure of the timezone, ask once and remember.

## Step 13 — Tell them what's next

Close with:

> Next:
> - `/product-12-prebuild-qa` — answer the 12 pre-build questions before engineering starts.
> - Or `/product-7-stress-test ./artifacts/data-model.md` if the GSI count felt high, the migration plan had any re-architecture flags, or lifecycle decisions were deferred. Technical + Operator POVs are sharpest here.
