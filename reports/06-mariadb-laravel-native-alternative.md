# MariaDB and Laravel-Native Alternative

**Independent feasibility report — 1 September 2026**

## Executive decision

Lotivo can be built from a fresh Laravel installation using MariaDB as its authoritative relational database. Laravel 13 provides first-party MariaDB support, and the large majority of Lotivo's product capabilities—identity, organisations, properties, listings, portals, administration, messaging, orders, entitlements, audit history and integrations—fit conventional Laravel, Eloquent and InnoDB patterns.

That conclusion has two important qualifications:

1. The existing Lotivo application cannot be moved to MariaDB by changing `DB_CONNECTION`. Its migrations and application services are deeply coupled to PostgreSQL and PostGIS facilities.
2. A MariaDB version should preserve the **business capability**, not reproduce the present physical database design statement for statement. Trying to emulate every PostgreSQL range, exclusion constraint, deferred constraint, advisory lock, trigger and PostGIS query would produce a less native, more fragile Laravel application.

The sensible alternative is therefore a **MariaDB-first clean rebuild**, designed around short Laravel-native transactions, explicit current-state tables, append-only history, row locks, ordinary unique constraints, Policies and Actions. Advanced geospatial behaviour should be introduced behind a deliberate boundary and only after it is proven against the required Australian data and accuracy.

For the current Lotivo repository, the recommendation is to remain on PostgreSQL/PostGIS. For a genuinely new day-zero implementation, MariaDB is credible if the project deliberately simplifies its first delivery and accepts that PostGIS-grade spatial work and PostgreSQL-grade cross-row constraint enforcement will not be reproduced automatically.

## The question being answered

This report answers four separate questions that can otherwise be confused:

| Question | Answer |
|---|---|
| Can the intended Lotivo product be built with Laravel and MariaDB? | **Yes.** |
| Can the current repository simply change database drivers? | **No.** |
| Can every current PostgreSQL/PostGIS mechanism be recreated identically while staying close to native Laravel? | **No, and it should not be the goal.** |
| Is MariaDB a reasonable day-zero alternative? | **Yes, after a focused proof of its hardest invariants and spatial requirements.** |

The target is not theoretical database portability. The target is a maintainable Laravel application whose behaviour is correct on one consciously selected database.

## What Lotivo needs from its database

The platform is not merely a property CRUD application. Its database must support several different kinds of work:

- durable identity for Users, Parties, Organisations, Assets, Properties, Listings, Places, Portals and external records;
- effective-dated relationships such as addresses, property types, organisation relationships, representation and authority;
- historical commercial campaigns, publications, orders, payments and verification outcomes;
- rules preventing incompatible active claims or overlapping authority;
- typed metadata and reference values;
- public route identity and aliases;
- precise and privacy-reduced location points, nearby search and potentially boundary/catchment tests;
- scoped lists, filters and deterministic pagination;
- idempotent imports, distribution, webhook and queue processing;
- same-transaction audit/outbox records; and
- extensive authorised Admin commands and review queues.

MariaDB can store all of these. The real design question is **where each invariant is enforced**: in a normal constraint, in an application transaction with a row lock, in a carefully selected database-specific feature, or in a separate rebuildable projection/service.

## Current implementation: why this is not a driver switch

The reviewed Lotivo branch already has a first-party `mariadb` connection configuration, but its actual physical design is PostgreSQL-specific. The repository contains 32 migrations; 28 use raw SQL statements or selections, and PostgreSQL-specific SQL appears across migrations, application code and tests.

The recurring dependencies include:

- PostgreSQL advisory transaction locks using `pg_advisory_xact_lock` and `hashtextextended`;
- timestamp ranges (`tstzrange`), GiST indexes and exclusion constraints;
- deferred constraints and explicit `SET CONSTRAINTS` behaviour;
- `NULLS NOT DISTINCT`, PostgreSQL JSON operators, `ILIKE`, interval construction and PostgreSQL regular expressions;
- session-local guard state using `set_config`;
- trigger-heavy cross-row enforcement;
- serializable-transaction checks;
- PostGIS `geography` and `geometry` columns; and
- `ST_DWithin`, `ST_Distance` and `ST_Covers` queries.

These mechanisms currently protect publication concurrency, effective dating, route allocation, representation/authority rules, subject references, messaging, verification, integration idempotency and spatial discovery. A mechanical SQL translation would be a risky rewrite of both schema and behaviour, followed by a substantial rewrite of the integration tests.

This is not an argument that MariaDB is incapable. It is evidence that the current code has already made an accepted PostgreSQL/PostGIS architectural choice. Replacing that choice in the existing repository would require a superseding decision record, a physical redesign, data-conversion planning and feature-by-feature revalidation.

## What “close to Laravel native” should mean

Laravel-native should describe the dominant development path, not prohibit all database-specific SQL.

The preferred path is:

```text
Route → Controller → Form Request → Policy → Action (when needed)
      → Eloquent models / query objects → Blade or API Resource
```

The database path should normally be:

```text
Laravel migration → ordinary InnoDB table → FK / unique / check / index
                  → Eloquent transaction → row lock → state transition
                  → audit/outbox record in the same transaction
```

This means:

- use Laravel Schema Builder for most tables and indexes;
- use Eloquent and the query builder for ordinary reads and writes;
- use an Action for a meaningful multi-record or transactional transition;
- use Laravel Policies for platform authorisation;
- use `DB::transaction` with deadlock retries and `lockForUpdate` for concurrency;
- use Jobs, Events, Notifications, Cache and the queue system before adding custom frameworks;
- use raw MariaDB SQL only for a small, named capability that has a clear test suite; and
- do not claim database portability after choosing MariaDB-specific temporal or spatial behaviour.

Laravel 13 officially supports MariaDB 10.3 and later, provides transaction/deadlock-retry APIs, and its migration system includes ULID, JSON, geometry, geography, full-text and spatial-index declarations. The exact spatial meaning remains driver-dependent. See the official [Laravel database documentation](https://laravel.com/framework/docs/13.x/database) and [Laravel migration documentation](https://laravel.com/framework/docs/13.x/migrations).

## Recommended MariaDB baseline

For a fresh project:

- use a maintained MariaDB LTS release supported by the selected host; MariaDB currently lists 11.4 and 12.3 as LTS releases, with different maintenance horizons ([MariaDB release policy](https://mariadb.org/about/));
- use InnoDB, `utf8mb4`, a deliberate collation and strict SQL behaviour;
- use the Laravel `mariadb` connection, not a disguised generic connection;
- run the same MariaDB family and relevant version in development, CI and production;
- keep money as integer minor units plus ISO currency code;
- keep instants in UTC and store timezone identifiers for civil schedules;
- use ULIDs consistently, created through Laravel's native column and model support;
- make ordinary referential, uniqueness and check rules real database constraints; and
- keep production reference data separate from development demonstration data.

The exact major version should be selected during the proof milestone, based on managed-host availability and the temporal/spatial features actually used. It should not be chosen only because it is the newest release.

## Capability assessment

### Straightforward Laravel/MariaDB capabilities

These areas can use conventional Laravel architecture without a material loss of capability:

- Users, external login identities and recovery paths;
- Parties, People, Organisations, memberships and relationships;
- Roles, Permissions, Policies and scoped access;
- Portals, Domains, Brands and operating-party assignments;
- Assets, Properties, Listings and media metadata;
- governed metadata/reference catalogues;
- products, orders, payments, commercial grants and entitlements;
- enquiries, conversations, messages and inspection requests;
- follows, saved searches and notification preferences;
- content, moderation queues and operational incidents;
- external source mappings, distribution receipts and reconciliation records;
- jobs, notifications, webhook inboxes and an outbox table;
- audit events and command reasons; and
- the extensive Admin interface.

These are mainly questions of clear domain boundaries, scoped queries, transactional commands and good indexes rather than database brand.

### Capabilities requiring deliberate redesign and testing

- effective-dated relationships and prevention of overlapping active periods;
- public route allocation and collision handling;
- single-active publication or authority claims;
- case-insensitive uniqueness and collation behaviour;
- JSON querying and indexes;
- lock coordination where the protected domain row does not yet exist;
- idempotent concurrent imports and webhook handling;
- full-text search relevance and ordering;
- interval/retention queries; and
- spatial radius and polygon queries.

### Hardest areas

The most difficult equivalence work is:

1. PostGIS geography calculations in metres and their index behaviour.
2. Boundary and catchment containment using Australian source polygons.
3. General cross-row no-overlap rules currently expressed through range/exclusion constraints.
4. Deferred database validation across a multi-step transition.
5. Replacing the current trigger and advisory-lock design without introducing races.

These need proof before MariaDB becomes an irreversible project assumption.

## Recommended substitutions for PostgreSQL-specific mechanisms

### Concurrency and advisory locks

Use database row locks as the default correctness mechanism:

```php
DB::transaction(function () use ($propertyId) {
    $property = Property::query()
        ->whereKey($propertyId)
        ->lockForUpdate()
        ->firstOrFail();

    // Validate current state and make the bounded transition.
}, attempts: 5);
```

Laravel supports retrying transactions after deadlocks, while MariaDB/InnoDB supports transaction-scoped row locking through `FOR UPDATE` ([Laravel transactions](https://laravel.com/framework/docs/13.x/database), [MariaDB `FOR UPDATE`](https://mariadb.com/docs/server/reference/sql-statements/data-manipulation/selecting-data/for-update)). Locks should be taken in a deterministic order.

If the protected record does not yet exist, introduce a small, ordinary guard record such as `operation_locks` or a natural owning aggregate row. Insert-or-load that record by a stable key, then lock it in the transaction. This is easier to inspect and test than a hash-based database advisory lock.

For coordination that is not part of a relational transaction—such as ensuring only one reconciliation job runs—use Laravel's distributed `Cache::lock` with a shared Redis or database cache ([Laravel atomic locks](https://laravel.com/framework/docs/13.x/cache#atomic-locks)).

MariaDB has `GET_LOCK`, but it is session-scoped, does not interact with transactions and is not released by commit. It should not be the default replacement for PostgreSQL transaction advisory locks ([MariaDB `GET_LOCK`](https://mariadb.com/docs/server/reference/sql-functions/secondary-functions/miscellaneous-functions/get_lock)).

### Effective-dated relationships

The most Laravel-native design is to separate current truth from history:

```text
property_current_addresses
  property_id UNIQUE
  property_address_id
  starts_at

property_address_history
  id
  property_id
  property_address_id
  starts_at
  ends_at
  reason / actor / audit reference
```

An Action locks the Property (or current assignment), closes the previous history interval, writes the new history record and replaces the unique current assignment in one transaction. The same pattern can be applied selectively to current property type, active representation or other relationships.

This gives simple current reads, understandable uniqueness and durable history. It avoids asking every developer and AI agent to reason about range operators.

MariaDB also has application-time periods and supports `WITHOUT OVERLAPS` on temporal primary/unique keys in applicable versions ([MariaDB application-time periods](https://mariadb.com/docs/server/reference/sql-structure/temporal-tables/application-time-periods)). That can be valuable for a small number of genuinely general interval invariants. It is, however, MariaDB-specific DDL rather than a normal Laravel Schema Builder path. It should be introduced only if the simpler current/history design is insufficient.

System-versioned tables are available too, but they should not replace explicit business history, reasons, actors and commands. Their operational and schema limitations make them a specialised infrastructure tool, not the default domain model ([MariaDB system-versioned tables](https://mariadb.com/docs/server/reference/sql-structure/temporal-tables/system-versioned-tables)).

### Active publication and authority claims

Do not emulate a conditional PostgreSQL exclusion or partial unique index if the active concept can be made explicit.

For example:

```text
listing_publication_claims
  portal_id
  property_id
  purpose_scope
  listing_id
  claimed_at
  UNIQUE (portal_id, property_id, purpose_scope)

listing_publication_claim_history
  claim_id / listing_id / started_at / ended_at / reason
```

The current-claim table contains only active claims. A transition locks the owning Property and applicable claim row, performs policy checks, moves completed data to history and changes the current record atomically. This expresses the business question directly and is easy to expose in Admin tooling.

Generated columns can support conditional keys where justified; MariaDB permits indexed virtual or persistent generated columns ([MariaDB generated columns](https://mariadb.com/docs/server/reference/sql-statements/data-definition/create/generated-columns)). They should remain an exception because their expression, collation and version behaviour adds a hidden physical contract.

### Deferred validation

Laravel/MariaDB workflows should avoid designs that temporarily violate a database invariant and depend on a deferred check at commit.

Instead:

- lock the aggregate or guard row;
- compute and validate the intended final state first;
- order updates so ordinary FK/unique/check constraints remain true after every statement;
- use a staging record for imports where incomplete data must be assembled; and
- publish or activate only after the staged record passes validation.

This usually produces a clearer use case. Where a multi-aggregate invariant cannot be represented this way, that is a signal either to simplify the invariant or to reconsider whether PostgreSQL is the better database.

### JSON

Laravel's `$table->json()` works with MariaDB, but MariaDB implements `JSON` as a validated `LONGTEXT` alias rather than PostgreSQL-style binary JSON ([MariaDB JSON](https://mariadb.com/docs/server/reference/data-types/string-data-types/json)).

Use JSON for bounded, versioned data such as:

- provider payload snapshots;
- event context;
- saved SearchCriteria snapshots; and
- configuration payloads whose schema is validated by the owning application code.

Keep identity, authority, searchable business facts, money, lifecycle state and important relationships in relational columns. If a measured query needs a JSON property, extract it into an indexed generated/ordinary column deliberately. Also test duplicate-key, collation and comparison behaviour rather than assuming PostgreSQL equivalence.

### Public URL identity

Use immutable public identifiers as part of canonical routes from the start:

```text
/property/{location-slug}/{property-public-id}
/listing/{listing-slug}/{listing-public-id}
```

Store current canonical paths and aliases under ordinary unique constraints such as `(portal_id, normalized_path)`. Human-readable slugs may change; immutable IDs resolve identity and allow canonical redirects.

This removes much of the need for elaborate global clean-path arbitration. Route allocation remains a short Action that locks the owning record, reserves the unique path and records any alias. A duplicate-key exception becomes a bounded retry with a deterministic suffix, not a database-wide lock system.

### Geospatial data

MariaDB supports geometry types including Point and Polygon and can create InnoDB R-tree spatial indexes; indexed spatial columns must meet its nullability requirements ([MariaDB geometry types](https://mariadb.com/docs/server/reference/sql-structure/geometry/geometry-types), [MariaDB spatial indexes](https://mariadb.com/docs/server/reference/sql-structure/geometry/spatial-index)). Laravel can declare geometry columns, reference-system identifiers and spatial indexes through its MariaDB grammar.

The limitation is semantic, not merely syntactic. MariaDB documents SRID as an integer attached to a geometry and states that calculations assume Euclidean planar geometry ([MariaDB `ST_SRID`](https://mariadb.com/docs/server/reference/sql-statements/geometry-constructors/geometry-properties/st_srid)). It provides `ST_DISTANCE_SPHERE` for spherical point/multipoint distances in metres ([MariaDB `ST_DISTANCE_SPHERE`](https://mariadb.com/docs/server/reference/sql-statements/geometry-constructors/geometry-relations/st-distance-sphere)). This is useful, but it is not a general replacement for PostGIS geography and topology behaviour.

Recommended staged approach:

1. Store authoritative longitude/latitude and provenance. A `POINT` with SRID 4326 may also be stored for indexing; keep a separate privacy-reduced public point.
2. For nearby search, apply an indexed bounding-box prefilter and then calculate spherical point distance. Verify the query plan and accuracy with representative Australian data.
3. Store official polygons only with source, version and freshness. Prove the exact containment functions and edge behaviour required by the product.
4. Do not market a result as a verified school catchment or governing boundary unless the source and acceptance policy establish that meaning.
5. If accurate complex catchment, topology, large polygon or high-volume distance work becomes critical, put it behind a `GeoQuery` application boundary. MariaDB remains authoritative for Lotivo identity and provenance, while a dedicated geospatial service/projection may answer rebuildable spatial queries.

This boundary is an appropriate interface because it represents a real alternative implementation. It should not be generalized into interfaces around ordinary Eloquent repositories.

### Search

Start with typed relational filters, allow-listed list definitions, ordinary indexes and MariaDB full-text indexes where measured. Use Eloquent/query objects and deterministic tie-break ordering. Preserve a versioned normalized `SearchCriteria` contract so controllers, canonical URLs, breadcrumbs and SEO consume one interpretation.

Do not introduce Elasticsearch or another search engine on day zero. Add a rebuildable search projection only when representative data and query measurements prove it is needed. Spatial ranking should consume the `GeoQuery` boundary rather than leaking MariaDB functions throughout controllers.

### Audit, events and triggers

Every meaningful Action should write its business record plus the necessary Audit Event and outbox record in the same transaction. Jobs publish or process the outbox idempotently after commit.

Database triggers should be reserved for a rule that genuinely cannot be protected through a normal constraint and a single owning Action. Hundreds of triggers would work against the stated Laravel-native objective because behaviour becomes split between PHP and hidden database programs.

The rule of thumb is:

- FK, check and unique constraint for local structural truth;
- transaction and row lock for a transition;
- Policy/domain authoriser for permission and authority;
- Audit/outbox write in the same Action;
- database-specific SQL only for a measured, high-value invariant.

## Admin application implications

MariaDB does not materially reduce the size of the Admin product. The Admin side remains a parallel delivery lane spanning almost every module.

The Laravel-native approach helps because each Admin operation can reuse the same authorised Action as the public or professional workflow:

- Admin screens read through scoped query/list definitions;
- forms use Form Requests;
- access uses Policies plus the distinct domain authority checks;
- changes invoke named Actions such as `ApprovePlaceCandidate`, `ResolvePublicationException` or `MergePropertyIdentity`;
- Actions require reasons and produce audit records where appropriate; and
- Blade, pagination, validation and session feedback remain the initial presentation path.

Avoid generic raw table editors. MariaDB's simpler current-state tables can actually make Admin queues easier to understand: the current claim is explicit, its history is adjacent, and the operator acts through the same transition rules as every other caller.

## Proposed day-zero milestone flow

### M00 — MariaDB project foundation

Deliver:

- fresh Laravel installation and first-party MariaDB connection;
- exact MariaDB version in development and CI;
- InnoDB/charset/collation/strict-mode decision;
- authentication shell, ULID convention, money/time conventions;
- `composer smoke` and `composer verify`; and
- a minimal planning structure with accepted product boundaries.

Exit evidence: fresh install, migrate, rollback and test pass against MariaDB—not SQLite standing in for MariaDB.

### M01 — database risk proof

This milestone should happen before the full logical model or large migration set.

Prove in a disposable vertical slice:

- ULID PK/FK/check/unique behaviour;
- case-insensitive unique identity under the chosen collation;
- concurrent state transitions using `lockForUpdate` and deadlock retry;
- the guard-row pattern where no domain row exists;
- one current/history effective-dated relationship;
- one active publication claim with concurrent contenders;
- same-transaction audit and outbox writes;
- JSON validation/extraction behaviour;
- a Point/SRID/spatial index and radius query;
- polygon containment if it is launch-critical; and
- query plans with representative data volumes.

Exit evidence should include concurrency tests using separate connections, not only sequential feature tests. If the required geo accuracy or concurrency invariant cannot be demonstrated cleanly, select PostgreSQL/PostGIS before more implementation accumulates.

### M02 — identity and authority foundation

Users, external identities, Parties, Organisations, memberships, Policies and audit context. Include the first small Admin identity queue.

### M03 — catalogue and Portal foundation

Governed metadata/reference catalogue, Portal/Domain/Brand/operating-party assignments and Admin catalogue operations.

### M04 — Property identity vertical slice

Asset/Property identity, current address plus history, deduplication candidates, durable public URL and Admin resolution. Include deterministic local development data.

### M05 — Listing lifecycle and publication claim

Historical Listings, purpose/method, transition Action, active publication claim, canonical Listing URL and Admin exception workflow.

### M06 — media and verification

Media metadata/storage integration, quotas, verification evidence/results and Admin review queues.

### M07 — public discovery

SearchCriteria, indexed relational filters, radius search, canonical URLs, public Property/Listing views and SEO behaviour. Defer advanced polygon/catchment promises unless M01 proved them.

### M08 — commercial path

Products, Orders, Payments, Entitlements, complimentary grants, publication eligibility and Admin reconciliation.

### M09 — professional operations and integrations

Agency relationships, Representation, inventory ingestion, external distribution and reconciliation. Use the same current/history and idempotent-inbox patterns already proven.

### M10 — engagement and scheduling

Enquiries, messaging, follows, notifications and private inspection requests, with privacy-aware Admin access.

### M11 — advanced location and operational hardening

Place enrichment, official boundaries/catchments where justified, performance tests, backups/restores, retention, security, accessibility and incident tooling.

This order front-loads MariaDB's actual risk. It prevents months of ordinary CRUD work from obscuring a late discovery that a core spatial or concurrency promise is unsuitable.

## Verification strategy

The alternative should use three layers:

1. **Fast unit tests** for pure normalization, rules and value objects.
2. **MariaDB integration/feature tests** for queries, constraints, transactions, Policies and HTTP behaviour.
3. **Focused concurrency and spatial tests** using the production database family and representative data.

Critical cases include:

- two workers attempting the same active claim;
- two route allocations colliding;
- idempotent webhook/import delivery under concurrency;
- rollback leaving no partial audit/outbox state;
- effective relationship transition at exact boundary instants;
- selected collation handling case and accented Australian names as intended;
- JSON path and duplicate-key behaviour;
- radius results at boundary distances and across longitude/latitude cases;
- point-on-boundary polygon semantics; and
- query plans and response times at target volumes.

SQLite may still be used for isolated pure tests, but a suite that passes only on SQLite is not acceptance evidence for MariaDB database behaviour.

## Trade-off summary

| Dimension | MariaDB-first clean rebuild | Existing PostgreSQL/PostGIS direction |
|---|---|---|
| Laravel first-party support | Strong | Strong |
| Ordinary CRUD/domain work | Simple and native | Simple and native |
| Current Lotivo migration compatibility | Very poor | Already implemented |
| JSON | Validated text; index extracted values deliberately | Rich `jsonb` operators and indexing |
| Effective dating | Simple current/history pattern; optional vendor periods | Strong ranges/exclusions |
| Complex cross-row constraints | More application transaction design | Strong database facilities |
| Transaction advisory locking | Prefer guard rows; `GET_LOCK` is session-scoped | Strong transaction advisory locks |
| Point/radius search | Viable with proof and careful query design | Strong PostGIS geography support |
| Complex boundaries/catchments | Viable only after exact proof; service may be preferable | Natural PostGIS workload |
| Hidden database programming | Can be kept low by design | Current repository already has extensive raw SQL/triggers |
| Hosting familiarity | Often broad, provider-dependent | Also broadly managed, provider-dependent |
| Day-zero learning burden | Lower if advanced invariants are simplified | Higher database-specific design burden |
| Risk of changing current repository | High | Lowest |

## Where MariaDB would make the project better

If treated as a clean design constraint, MariaDB could correct several tendencies observed in the existing project:

- favour explicit current-state tables over generalized temporal machinery;
- favour one owning Laravel Action over duplicated PHP and trigger behaviour;
- make immutable public IDs part of URLs rather than solving every clean-slug collision globally;
- require spatial scope to be delivered incrementally and measured;
- keep ordinary features on the shortest native Laravel path;
- make concurrency tests a first milestone rather than late verification; and
- reduce the amount an AI developer must load before making a bounded safe change.

Those benefits come from the **redesign discipline**, not from MariaDB automatically being simpler in every respect.

## Where MariaDB could make the project worse

It would be a regression if the project:

- ports every current trigger into MariaDB triggers;
- replaces transaction advisory locks indiscriminately with session `GET_LOCK` calls;
- assumes geometry SRID provides PostGIS geography correctness;
- moves important relational facts into JSON to avoid schema design;
- weakens no-overlap or active-claim rules without explicit replacement tests;
- uses SQLite as CI evidence for MariaDB behaviour;
- exposes database-specific functions throughout controllers; or
- undertakes the conversion only to avoid making product-scope decisions.

In that scenario the project would carry both the current conceptual complexity and a less suitable physical implementation.

## Recommended decision

### For the existing Lotivo repository

Remain on PostgreSQL/PostGIS. The accepted architecture, migrations, services and tests already depend on it. A conversion would be a major programme with little direct user value and would delay the Admin and end-to-end product work that is still outstanding.

The better improvement is to keep the database but simplify future Laravel implementation: fewer new triggers, shorter Actions, explicit current-state models where appropriate, and database-specific mechanisms only when an immediate invariant requires them.

### For a separate fresh implementation

MariaDB is a sound candidate under these conditions:

- the project agrees that user-visible capability matters more than matching the existing schema;
- M01 proves its hardest concurrency, collation and spatial cases;
- effective dating generally uses explicit current/history records;
- immutable public IDs simplify URL identity;
- advanced boundary/catchment behaviour is staged or placed behind a real geospatial boundary;
- the application accepts MariaDB as its selected database rather than pretending to be portable; and
- database-specific features remain few, named and thoroughly tested.

If exact PostGIS-style geospatial behaviour, highly generalized effective-period exclusions and extensive database-enforced cross-row invariants are non-negotiable from the beginning, PostgreSQL/PostGIS remains the more efficient and lower-risk choice.

## Final answer

**Yes: the whole intended platform can be delivered with Laravel and MariaDB, while keeping most development close to native Laravel.** The clean route is not to port the existing physical database. It is to rebuild the product around Eloquent, InnoDB constraints, explicit current/history tables, row-locked Actions, Policies, Jobs and a small number of proven database-specific queries.

The database choice should be made immediately after a short risk-proof milestone. For the application that already exists, stay with PostgreSQL/PostGIS. For a genuine day-zero do-over with deliberately staged spatial scope, MariaDB is a defensible and potentially simpler foundation.

## Primary references

- [Laravel 13 database documentation](https://laravel.com/framework/docs/13.x/database)
- [Laravel 13 migrations](https://laravel.com/framework/docs/13.x/migrations)
- [Laravel atomic locks](https://laravel.com/framework/docs/13.x/cache#atomic-locks)
- [MariaDB release policy and maintained releases](https://mariadb.org/about/)
- [MariaDB JSON data type](https://mariadb.com/docs/server/reference/data-types/string-data-types/json)
- [MariaDB geometry types](https://mariadb.com/docs/server/reference/sql-structure/geometry/geometry-types)
- [MariaDB spatial indexes](https://mariadb.com/docs/server/reference/sql-structure/geometry/spatial-index)
- [MariaDB geometry relationships](https://mariadb.com/docs/server/reference/sql-statements/geometry-constructors/geometry-relations)
- [MariaDB `ST_SRID`](https://mariadb.com/docs/server/reference/sql-statements/geometry-constructors/geometry-properties/st_srid)
- [MariaDB `ST_DISTANCE_SPHERE`](https://mariadb.com/docs/server/reference/sql-statements/geometry-constructors/geometry-relations/st-distance-sphere)
- [MariaDB generated columns](https://mariadb.com/docs/server/reference/sql-statements/data-definition/create/generated-columns)
- [MariaDB constraints](https://mariadb.com/docs/server/reference/sql-statements/data-definition/constraint)
- [MariaDB application-time periods](https://mariadb.com/docs/server/reference/sql-structure/temporal-tables/application-time-periods)
- [MariaDB system-versioned tables](https://mariadb.com/docs/server/reference/sql-structure/temporal-tables/system-versioned-tables)
- [MariaDB `GET_LOCK`](https://mariadb.com/docs/server/reference/sql-functions/secondary-functions/miscellaneous-functions/get_lock)
- [MariaDB `FOR UPDATE`](https://mariadb.com/docs/server/reference/sql-statements/data-manipulation/selecting-data/for-update)
