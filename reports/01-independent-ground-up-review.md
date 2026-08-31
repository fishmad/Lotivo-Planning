# Lotivo Independent Ground-Up “Do-Over” Review

**Review date:** 1 September 2026
**Scope:** Repository documentation, Laravel implementation, tests, migrations, Git history, Work Items, ADRs, GitHub pull requests, reviews and workflow history.

## Executive verdict

Lotivo has made many sound architectural decisions, but it has implemented them in an inefficient order and at a level of governance disproportionate for a one-person, pre-production project.

The central problem is not poor engineering. Architecture, governance, database safeguards and review machinery grew faster than the demonstrable product.

In roughly fifteen days, the project accumulated:

- 741 commits on `main`;
- approximately 100 pull requests;
- 77 Work Items;
- 55 accepted ADRs;
- 267 documentation files containing about 55,000 lines;
- 134 Eloquent models;
- 138 Action classes;
- 31 unusually large migrations;
- approximately 28,000 lines of tests;
- 1,750 GitHub Actions workflow runs.

Yet the repository still described the system primarily as local/testing foundations without a complete production-ready customer journey.

> Lotivo has mostly chosen the right concerns, but applied them at the wrong granularity, in the wrong order and too early.

A ground-up rebuild should preserve the hard-won identity, privacy, money, history and URL decisions. It should not replay the existing implementation sequence or rebuild every existing subsystem before proving the core marketplace journey.

## Decisions worth preserving

- `Property` and `Listing` are separate durable concepts.
- `User`, `Party`, Person, Organisation and professional identity are distinct.
- Historical Listings and important authority/payment records remain durable.
- Money uses integer minor units and an ISO currency code.
- PostgreSQL/PostGIS is the authoritative store.
- Lotivo is one platform serving multiple Portals, not separate application/database silos.
- Search state, public URL identity, privacy, ownership, Representation and publication authority remain separate concerns.
- Provider data does not become the canonical domain model.
- Exact-address disclosure and similar privacy-sensitive outcomes are default-deny.

The repeated reviews caught real defects, including cross-Portal identity leakage, payment-allocation weaknesses, publication-policy inconsistencies, route retargeting, stale visibility decisions, race conditions and privacy-boundary problems.

The lesson is not to discard governance. It is to concentrate governance on irreversible decisions and reduce it elsewhere.

## Where delivery went off course

### Horizontal foundations preceded the first thin vertical product

Broad foundations for metadata, commerce, distribution, incidents, messaging, verification, content, Portals, organisations and route identity were implemented before one small end-to-end user journey was repeatedly demonstrated.

The first substantial owner-facing vertical slice did not arrive until LOTI-0055. Deterministic representative development data arrived later in LOTI-0071. Final Search parity corrections and route identity work continued through LOTI-0072 and LOTI-0073.

That order forced late integration to discover that individually correct foundations did not compose into one usable journey.

The replacement rule should be:

> Every new foundation must immediately support a visible user outcome, a deterministic development scenario and one permanent journey test.

### The clean-address URL ambition created a disproportionate subsystem

The public route registry became one of the most complex areas because it attempted to provide clean address paths while handling collisions, multiple Portals and Domains, relisting, address changes, historical aliases, terminal Listings, legacy URLs, concurrent claims, privacy decisions, redirects, route ownership and host changes.

An always-present immutable suffix would retain readable URLs while eliminating much of the global claim ownership and recanonicalisation machinery:

```text
/buy/nsw/nowra-hill-2540/24-sample-road-l-ABC123
```

or:

```text
/listing/{immutable-public-id}/{optional-slug}
```

If completely clean address routes remain non-negotiable, the registry belongs before Search UI, SEO, legacy redirects, theming and public journey expansion—not as LOTI-0073.

### Database enforcement became enterprise-scale too early

The migrations contain approximately:

- 175 `CREATE FUNCTION` occurrences;
- 368 `CREATE TRIGGER` occurrences;
- 40 exclusion-constraint occurrences.

Several migrations are 40–62 KB and implement broad subsystems in one operation.

Some database enforcement is justified for money, identity, effective dates, uniqueness and destructive history. The balance nevertheless moved too far toward modelling every anticipated invariant before the associated user journey existed.

The preferred Laravel balance is:

- foreign keys, uniqueness, checks and essential exclusions in PostgreSQL;
- transactions and meaningful multi-record transitions in Laravel Actions;
- Policies and domain-aware authorisers for access;
- triggers only where concurrent correctness genuinely cannot be maintained safely in application code.

### Test isolation arrived too late

Much verification churn was caused by test architecture rather than feature logic:

- shared canonical fixtures;
- destructive teardown of common Location data;
- synthetic data using real codes such as `nsw`;
- concurrent processes competing for shared records;
- migration and rollback order contamination;
- tests capturing different wall-clock instants;
- incompatible generated-file ownership;
- assumptions about a Vite manifest;
- committed concurrency tests sharing database state.

The suite should be divided into:

1. Fast unit and HTTP feature tests using transaction rollback or `RefreshDatabase`.
2. PostgreSQL constraint tests with their own fresh schema or disposable database.
3. Migration and rollback tests in an isolated database.
4. Concurrency tests in separate processes using unique fixture namespaces.
5. A small permanent browser-journey suite.
6. Development data installed separately from automated-suite fixtures.

## Hardest implementation areas

Workflow times below are summed observed workflow elapsed time. They compare churn but do not represent developer labour or billable runner time.

| Work | Evidence | Assessment |
|---|---:|---|
| LOTI-0055 owner journey, PR #56 | 163 files, 9,726 additions, 60 commits; 61 Laravel runs and 21 failures; about 396 observed run-minutes | Strongest evidence that the first real vertical slice arrived too late. Existing foundations initially could not compose one allowed owner, compliance, entitlement, publication and UI path. |
| LOTI-0073 route registry E1, PR #97 | 68 files, 6,567 additions, 33 commits; 52 Laravel runs; 43 inline review findings; about 419 run-minutes | Hardest domain-design area. It exposed relisting recanonicalisation, host-history conflicts, deadlocks, alias integrity, concurrency and an invalid privacy trust-boundary assumption. |
| LOTI-0036 Search, PR #47 | 24 Laravel runs and 16 failures | Highest concentrated verification failure rate. Shared Location fixtures, destructive resets, projection order and presentation setup repeatedly broke runs. |
| LOTI-0060 Search correction, PR #65 | 71 files; 26 Laravel runs and 5 failures; three failures around 17 minutes each | Final URL/filter/product examples and predecessor behaviour were not settled early enough. |
| LOTI-0074 Portal theming, PR #78 | 28 Laravel runs, 3 failures and 8 cancellations | Failures largely came from suite isolation: a synthetic `nsw` claim, destructive Location cleanup and inconsistent time. |
| LOTI-0049 distribution, PR #55 | 94 files and 6,181 additions; 20 Laravel runs and 6 failures | Large provider-neutral distribution foundation implemented before the free journey and live-provider need were proven. |

Across GitHub Actions:

- 1,750 runs were recorded;
- 157 failed and 68 were cancelled;
- Architecture ran 946 times;
- Laravel verification ran 749 times;
- the two main workflows accumulated about 4,020 observed run-minutes, or 67 hours;
- failed runs accumulated about 650 observed minutes.

Proportionate verification later improved the failure rate, but only after most run volume had already occurred.

## Documentation and numbering

Work Item IDs should remain immutable identifiers, not execution order. The current system partly treats the number as identity and partly presents it as sequence. A better entry separates the concepts:

```yaml
id: LOTI-0073
stage: public-discovery
delivery_rank: 4
depends_on:
  - LOTI-0060
status: in_progress
```

ADRs should remain chronological. They record when a decision was made, not when it should be implemented. Improve navigation with indexes by topic and rebuild stage plus explicit supersession links; do not renumber accepted decisions.

The current document tree is historically understandable but operationally inefficient. The preferred home for Work Item plans is `docs/work-items`, while older accepted plans remain scattered across domain folders. The Work Ledger is about 324 KB and contains roughly 1,180 evidence bullets. Detailed review chronology and transient workflow runs would be better retained in GitHub PRs.

## Right-sized governance

Current governance is excessive for a one-person pre-production project. The ratio is wrong: too much governance around speculative or inactive capabilities and not enough early discipline around delivery order, test isolation, permanent journeys and production activation.

Strong governance should remain for:

- identity and merge rules;
- money and payment allocation;
- public URL identity;
- privacy and exact-address disclosure;
- authorisation and Representation;
- historical data preservation;
- cross-Portal scoping;
- provider trust boundaries;
- production migration and rollback.

Governance should be lighter for routine Laravel work, reversible internal class structure, speculative capabilities, evidence-only checkpoints, repeated exact-head reviews after minor corrections and status details already recorded in a PR.

Only about 20–30 percent of the current governance surface should sit on the daily development path. The remainder can remain reference material consulted when a change crosses its boundary.

## Ground-up construction order

1. Freeze only irreversible contracts: identity, money, authoritative storage, privacy and stable public URLs.
2. Establish fresh Laravel delivery safety: isolated tests, focused/full verification, branch protection, authentication and a minimal Admin shell.
3. Implement one minimal Property and Listing record with deterministic data.
4. Let an administrator publish it and a visitor view it through a stable immutable-ID URL.
5. Implement minimal public Search.
6. Complete the free owner draft, media, submission, Admin review and publication journey.
7. Add enquiries and replies.
8. Add one Product, one Payment provider and one Entitlement.
9. Add one external distribution destination.
10. Rehearse predecessor migration and production recovery.
11. Expand into agency inventory, additional Portals and advanced capabilities only when evidence supports them.

## Final assessment

Lotivo is not failing because it lacks thought or engineering discipline. It is slowed by treating too many matters as if they already carry production-scale organisational consequences.

The existing work produced valuable domain knowledge. A rebuild should reuse that knowledge without reproducing the process that discovered it.

> Keep the hard-won rules. Simplify their implementation. Build vertically. Make IDs identify records rather than pretend to be the plan. Put history in GitHub, current truth in a small ledger, and prove each foundation through a real user journey before starting the next one.
