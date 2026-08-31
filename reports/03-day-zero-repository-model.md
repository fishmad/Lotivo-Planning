# Lotivo Day-Zero Repository Model

The clean repository should begin small and outcome-driven. At day zero, create fewer than a dozen planning documents, one Work Ledger and no ADR until an actual irreversible decision requires one.

```text
Product purpose
    ↓
User and administrator journeys
    ↓
Outcome-based milestone
    ↓
Dependency-ordered Work Item
    ↓
Plan or ADR only when needed
    ↓
Implementation and focused verification
    ↓
Demonstrable customer/Admin outcome
    ↓
Accepted commit and milestone update
```

> Do not build a foundation unless the current or immediately following milestone consumes it through a real customer or administrator journey.

## Suggested repository tree

```text
Lotivo/
├── AGENTS.md
├── README.md
├── composer.json
├── app/
├── bootstrap/
├── config/
├── database/
├── public/
├── resources/
├── routes/
├── tests/
│
└── docs/
    ├── 00-start-here/
    │   ├── product-brief.md
    │   ├── scope-boundary.md
    │   ├── glossary.md
    │   └── status.md
    │
    ├── product/
    │   ├── journeys/
    │   │   ├── J-001-admin-publish-listing.md
    │   │   ├── J-002-public-find-and-view.md
    │   │   └── J-003-owner-submit-listing.md
    │   └── screen-inventory.md
    │
    ├── delivery/
    │   ├── roadmap.md
    │   ├── work-items.yaml
    │   ├── milestones/
    │   │   ├── M0-safe-foundation.md
    │   │   ├── M1-first-published-listing.md
    │   │   ├── M2-public-discovery.md
    │   │   └── M3-owner-publication.md
    │   └── work-items/
    │       └── LOTI-####/
    │           └── plan.md
    │
    ├── architecture/
    │   ├── system-context.md
    │   ├── data-principles.md
    │   └── decisions/
    │       ├── README.md
    │       └── ADR-template.md
    │
    ├── development/
    │   ├── commands.md
    │   ├── testing.md
    │   └── conventions.md
    │
    └── templates/
        ├── milestone-template.md
        ├── work-item-template.md
        └── adr-template.md
```

Directories establish where information belongs; they are not instructions to populate the future system in advance.

## One authority per question

| Question | Authority |
|---|---|
| Why does Lotivo exist? | `product-brief.md` |
| What is explicitly in or out? | `scope-boundary.md` |
| What do important terms mean? | `glossary.md` |
| Where are we now? | `status.md` |
| What outcomes come next? | `roadmap.md` |
| What proves a milestone complete? | Milestone document |
| What is being worked on now? | `work-items.yaml` |
| How will this Work Item be delivered? | Its `plan.md` |
| Why was an irreversible decision made? | ADR |
| What happened during implementation/review? | GitHub PR |
| Which exact version was accepted? | Work Item acceptance commit |

## Day-zero documents

### Product brief

One or two pages covering what Lotivo is, who it serves, the immediate problem, the smallest credible release and how success will be recognised.

### Scope boundary

The exclusion list is essential:

- no property transaction or settlement;
- no escrow or trust accounting;
- no buyer bidding or contract exchange;
- no advanced agency integration in the first release;
- no multiple paid destinations initially;
- no speculative operational automation.

### Glossary

Start with only the terms needed by initial milestones: User, Party, Property, Listing, Portal, Ownership, Publication and administrator.

### System context

```text
Browser
   ↓
Laravel application
   ↓
PostgreSQL/PostGIS

Optional later services:
- email
- object storage
- payment provider
- distribution provider
```

### Data principles

- ULID identity;
- PostgreSQL as authority;
- money in minor units;
- instants in UTC;
- important history is not silently destroyed;
- public and private facts remain separated;
- provider values do not automatically become canonical values.

### Roadmap

Use outcome milestones such as “An administrator can publish one Listing and a visitor can view it.” Avoid layer milestones such as “Complete models” or “Complete the database.”

### Status

Keep status to one page:

```markdown
# Current status

Current milestone: M1 — First published Listing
Health: Green

Now:
- LOTI-0004 — Publish and withdraw a Listing

Next:
- LOTI-0005 — Public Listing detail page

Blocked:
- None

Recently accepted:
- LOTI-0003 — Property and Listing records
- Commit: abc1234
- Verification: linked workflow
```

## Milestone template

Every milestone should define:

- one observable outcome;
- why it is next;
- included capability;
- explicit exclusions;
- required customer demonstration;
- required Admin operation;
- completion evidence.

A milestone is complete only when the demonstration can be reproduced from a fresh installation.

## Work Item model

```yaml
current_milestone: M1
next_recommended_work_item: LOTI-0004

work_items:
  - id: LOTI-0004
    title: Publish and withdraw a Listing
    milestone: M1
    delivery_rank: 4
    status: in_progress
    outcome: >
      An authorised administrator can publish and withdraw an
      eligible Listing with an audited reason and truthful feedback.
    why_now: >
      Public Listing presentation depends on one authoritative
      publication transition.
    depends_on:
      - LOTI-0003
    plan: docs/delivery/work-items/LOTI-0004/plan.md
    next_action: Implement the publish command and focused feature test.
    pull_request: null
    accepted_commit: null
    accepted_verification: null
```

IDs identify Work Items. `milestone`, `delivery_rank` and `depends_on` determine order.

Use the lifecycle:

```text
proposed → ready → in_progress → review → done
```

Alternative states are `blocked`, `deferred` and `superseded`. Normally only one implementation item is in progress.

## Choosing the next Work Item

1. It is required by the current milestone.
2. Its hard dependencies are complete.
3. It produces or unblocks an end-to-end outcome.
4. It resolves something expensive to reverse.
5. It provides earlier useful learning.
6. Its Admin operation and recovery are understood.
7. It is the smallest coherent mergeable step.

Every plan must answer why the work is being done now, what becomes possible, what is excluded and what customer/Admin behaviour proves it.

## ADR threshold

Write an ADR only when a decision materially affects identity, canonical ownership, money, public URLs, privacy, authority, history, persistence, provider trust or major dependency direction.

Do not create an ADR for ordinary Laravel choices. A normal ADR should be approximately one page: Context, Decision, Consequences, Rejected Alternatives and Supersession.

ADR numbers record chronology, not implementation order.

## Proportionate planning and verification

- Small reversible work: ledger entry and PR description.
- Normal feature: one-page plan.
- High-risk identity, money, privacy, concurrency, migration or URL work: detailed plan and possibly ADR.

Verification levels should exist from day zero:

- focused tests after coherent edits;
- smoke verification before checkpoint/handoff;
- full verification at ready-for-review or milestone acceptance;
- isolated suites for migrations, PostgreSQL constraints, concurrency, browser journeys and providers.

## AI operating contract

`AGENTS.md` should remain short enough for a developer to understand. It contains the source-of-truth order, startup procedure, lifecycle, core vocabulary, irreversible rules, Laravel defaults, testing expectations and completion/handoff requirements.

An ordinary session should read only the README, current status, active Work Item, its plan, directly applicable ADRs, and relevant implementation/tests—not the complete project history.

## Progress reporting

Report observable outcomes rather than invented percentages:

```text
M3 — Owner publication

✓ Owner can create a draft
✓ Owner can upload valid media
✓ Owner can submit for review
✓ Administrator can inspect submission
◐ Administrator can reject with governed reasons
○ Administrator can approve and publish
○ Owner receives truthful outcome
○ Permanent browser journey passes
```

GitHub contains detailed history. The repository contains current truth.

## Controlling growth

Before adding any model, table, Action, Policy, screen, Work Item, ADR or document, ask:

1. Which current milestone requires it?
2. Which user or administrator journey consumes it?
3. What breaks if it is deferred?
4. Is the decision difficult to reverse?
5. Can the outcome be delivered with less structure?

If there is no concrete answer, defer it.
