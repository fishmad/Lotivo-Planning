# Lotivo Component and Milestone Delivery Map

## What Lotivo is

Lotivo is a unified Australian property-classifieds platform operating multiple branded Portals from one canonical data platform.

It allows authorised owners, landlords, agents and organisations to create Listing advertising campaigns for durable Properties. Administrators verify and operate those campaigns. Public visitors search, view, enquire and optionally follow subjects or request inspections. Commercial capability sells Lotivo advertising Products and can distribute Listings externally.

Lotivo is not a property transaction, bidding, escrow, settlement or conveyancing platform.

## Main components

| Component | Responsibility |
|---|---|
| Platform and Portals | One application and canonical database serving branded publication channels |
| Identity | Users, authentication methods, Parties, people and organisations |
| Authority | Ownership, Representation, agency authority, licences and permissions |
| Property | Durable real-world Property identity, address, type and historical facts |
| Listing | One historical sale or rental advertising campaign for a Property |
| Media | Listing images/media, validation, ordering and access |
| Publication | Eligibility, approval, Portal publication, withdrawal and privacy |
| Discovery | SearchCriteria, locations, filters, results, canonical URLs and SEO |
| Engagement | Enquiries, Conversations, Messages, follows and inspection requests |
| Commerce | Products, Orders, Payments, Entitlements, refunds and complimentary grants |
| Distribution | External advertising-destination submission and reconciliation |
| Professional inventory | Organisations, agents, CRM/import connections and reconciliation |
| Administration | Moderation, verification, support, corrections, configuration and audit |
| Migration and operations | Predecessor import, security, queues, monitoring, backups and support |

## Delivery structure

```text
Release
└── Milestone: one demonstrable outcome
    ├── Work Item: bounded data/domain change
    ├── Work Item: application transition
    ├── Work Item: customer interface
    ├── Work Item: administrator interface
    └── Work Item: journey verification
```

Each Work Item should normally represent one state transition, coherent screen, governed list, bounded schema capability, adapter operation or browser-journey checkpoint.

# Release A — First operable marketplace loop

## M00 — Safe delivery foundation

**Outcome:** The application can be developed, tested and reviewed safely from a fresh installation.

Work Items:

- environment and health check;
- isolated PostgreSQL test database;
- focused, smoke and full verification commands;
- CI and branch protection;
- deterministic synthetic-data loader;
- one browser application-start test;
- development command documentation.

## M01 — Authentication and minimal Admin shell

**Outcome:** A User and authorised administrator can sign in and reach the correct area.

Work Items:

- Laravel authentication;
- minimal User and Person Party identity;
- administrator permission and Policy;
- customer shell;
- Admin shell and navigation;
- access tests.

Do not yet implement organisations, social login, complex roles or impersonation.

## M02 — Property identity

**Outcome:** An administrator can create and inspect one durable Property with a current address.

Work Items:

- immutable Property identity;
- effective Property Address;
- minimal Property type;
- create/correct Actions;
- Admin list and detail;
- deterministic Property scenario;
- focused tests.

## M03 — Listing campaign

**Outcome:** An administrator can create and edit a draft Listing for a Property.

Work Items:

- Listing identity and Property relationship;
- minimal sale/rent purpose;
- draft lifecycle;
- required descriptive and price fields;
- create/update Actions;
- Admin list, detail and blockers;
- draft tests.

## M04 — Publication and public Listing page

**Outcome:** An administrator publishes an eligible Listing and a public visitor views it through a stable URL.

Work Items:

- minimum publication eligibility;
- one authoritative public-visibility decision;
- publish/withdraw Actions with reason and audit;
- immutable-ID public route and optional slug;
- public Listing page;
- Admin publication diagnostics;
- publish/withdraw browser journey.

This is the first meaningful product release. The human gate approves public URL identity, public/private fields and minimum publication policy.

# Release B — Public discovery

## M05 — Location and SearchCriteria v1

**Outcome:** A visitor can express a small canonical search.

Work Items:

- minimal State, suburb/locality and postcode;
- SearchCriteria version 1;
- sale/rent, property-type and price filters;
- parser, normaliser and URL serializer;
- invalid/conflicting-state handling;
- round-trip contract tests.

Defer schools, arbitrary Places, maps, saved searches and advanced SEO landings.

## M06 — Search results

**Outcome:** A visitor can find eligible Listings and navigate to details.

Work Items:

- governed discovery query;
- deterministic sorting/pagination;
- Search results and cards;
- empty/invalid states;
- privacy and withdrawal enforcement;
- Admin visibility diagnostics;
- Search-to-detail browser journey.

## M07 — Canonical discovery and basic SEO

**Outcome:** Search and detail URLs remain consistent when labels or slugs become stale.

Work Items:

- canonical redirects;
- aliases only where required;
- canonical tags and indexability;
- titles and breadcrumbs from SearchCriteria;
- minimal sitemap;
- stale-slug tests.

Review real predecessor URLs before implementation.

# Release C — Owner self-service

## M08 — Owner workspace and draft Listing

**Outcome:** An authenticated owner creates and edits a draft Listing for an authorised Property.

Work Items:

- minimal ownership assertion;
- owner workspace and Property selection;
- draft Listing form and validation;
- owner Policy;
- truthful save feedback;
- Admin support view;
- owner-draft browser journey.

## M09 — Media management

**Outcome:** An owner uploads, orders and removes valid Listing images safely.

Work Items:

- Media Object and storage adapter;
- MIME/size/extension validation;
- processing job;
- quota;
- ordering and primary image;
- owner media interface;
- Admin moderation;
- failed-processing/retry behaviour;
- media security tests.

## M10 — Submission and Admin review

**Outcome:** An owner submits a Listing and an administrator approves or rejects it.

Work Items:

- submission transition;
- governed submitted fields;
- Admin review queue and detail;
- structured blockers;
- rejection with reasons;
- approval/publication;
- owner outcome and notification;
- complete owner-to-public browser journey.

This is the first complete customer/administrator operating loop.

# Release D — Authority, safety and engagement

## M11 — Governed authority and verification

**Outcome:** Publication uses explicit Ownership or Representation evidence.

Work Items:

- separate Ownership and Representation;
- verification case and evidence references;
- manual outcome, expiry/revocation where required;
- Admin verification queue;
- publication integration;
- default-deny authority tests.

Human review is required for legal, privacy and evidence policy.

## M12 — Enquiries and Conversations

**Outcome:** A visitor contacts the appropriate recipient without exposing private communication publicly.

Work Items:

- Enquiry;
- authenticated/verified-guest boundary;
- Conversation, Participant and Message;
- recipient routing;
- rate limiting and abuse protection;
- customer inbox;
- restricted Admin investigation;
- enquiry/reply browser journey.

## M13 — Notifications, follows and inspection requests

**Outcome:** People receive governed notifications, follow relevant subjects and request private inspections.

Split this milestone if needed. Keep communication preferences, marketing consent and follow interest separate.

# Release E — Commercial capability

## M14 — Product and Entitlement

**Outcome:** Lotivo grants one advertising capability without requiring Payment initially.

Work Items:

- one Product and price;
- Order and Entitlement;
- complimentary Admin grant with reason/approval;
- integration with one publication option;
- Admin commercial history.

## M15 — Payment and reconciliation

**Outcome:** A customer pays for one Product and an administrator can reconcile or refund it.

Work Items:

- one provider adapter;
- checkout and Payment attempt;
- idempotent webhook;
- Payment-to-Order allocation;
- Entitlement grant;
- failure/retry handling;
- Admin reconciliation and governed refund;
- provider contract tests.

Payment never bypasses authority, compliance or publication approval.

# Release F — Portals and distribution

## M16 — Multiple Lotivo Portals

**Outcome:** One canonical Listing publishes through multiple owned branded Portals without cloning it.

Work Items:

- stable Portal identity;
- Portal Domain and effective Brand;
- Portal publication;
- Portal-aware presentation;
- Admin Portal configuration;
- cross-Portal privacy and canonical-route tests.

## M17 — First external destination

**Outcome:** One eligible Listing is submitted to one external destination and reconciled.

Work Items:

- provider-neutral destination contract;
- one adapter and credential reference;
- canonical payload mapping;
- asynchronous submission;
- receipt and reconciliation;
- retry/permanent failure;
- Admin distribution queue;
- provider contract tests.

# Release G — Migration and production

## M18 — Predecessor migration rehearsal

**Outcome:** A bounded predecessor sample imports and reconciles without duplicate canonical identities.

Work Items:

- source mapping;
- dry-run importer;
- identity match report;
- exception queue and Admin reconciliation;
- provenance preservation;
- idempotent rerun;
- migration report.

Humans decide ambiguous identity matches.

## M19 — Production readiness and launch

**Outcome:** The validated product can be operated and recovered in production.

Work Items:

- production configuration and trust audit;
- backup/restore rehearsal;
- queue and failed-job operations;
- monitoring and alerts;
- privacy-request operation;
- security corrections and rate limits;
- incident runbook;
- deployment/rollback rehearsal;
- final migration rehearsal and launch checklist.

Legal, privacy, provider, security and go-live acceptance remain human gates.

## M20 — Professional organisation and inventory expansion

**Outcome:** A professional organisation manages authorised people and synchronises one inventory source.

Work Items:

- Organisation Party and membership;
- Agent Profile, licence and agency authority;
- organisation relationships;
- inventory connection and stable mappings;
- import reconciliation;
- Admin organisation/inventory interfaces.

This remains late unless professional inventory is required for the first launch.

## AI-ready Work Item contract

An AI-ready item has no unresolved product decision hidden within it:

```yaml
id: LOTI-0017
title: Administrator rejects a submitted Listing
milestone: M10
outcome: >
  An authorised administrator rejects a submitted Listing using
  governed reasons, and the owner sees the truthful result.
why_now: >
  The owner-submission journey cannot operate without a rejection path.
depends_on:
  - LOTI-0016
excluded:
  - appeal workflow
  - automated moderation
  - bulk rejection
acceptance:
  - unauthorised User receives 403
  - invalid transition is blocked
  - a reason is required
  - the owner sees the reason
  - public visibility remains false
  - the decision is audited
```

With this contract, AI can normally implement the migration, model changes, Form Request, Action, Policy, controller, Blade UI, tests, deterministic fixture and current-behaviour documentation.

Human intervention is concentrated at product scope, irreversible decisions, milestone demonstrations and production acceptance.

## Overall flow

```text
M00–M04  Safe foundation and first public Listing
    ↓
M05–M07  Public Search and durable discovery
    ↓
M08–M10  Owner self-service and Admin moderation
    ↓
M11–M13  Authority, enquiries and engagement
    ↓
M14–M15  Products, Entitlements and Payment
    ↓
M16–M17  Multiple Portals and external distribution
    ↓
M18–M19  Migration, production readiness and launch
    ↓
M20+     Evidence-driven professional expansion
```

The critical checkpoint is M04. Until an administrator can publish one Listing and a visitor can view it, broad future foundations should be resisted.

The second critical checkpoint is M10. Until an owner can submit and an administrator can make an operational decision through the real interface, the marketplace workflow is incomplete.
