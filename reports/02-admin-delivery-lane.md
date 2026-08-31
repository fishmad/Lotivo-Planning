# Administration as a Parallel Delivery Lane

The administrator experience is not a finishing layer. It is a second major product surface and the control plane through which Lotivo is operated.

The current project is less complete than its completed Work Item count suggests because many implemented foundations cannot yet be safely operated, corrected, reconciled or supported through a usable interface.

Admin should remain inside the same Laravel monolith and use the same domain Actions, Policies and authoritative data. It should not be built as an enormous late phase or a generic database editor.

## Operational completion loop

```text
Customer action
      ↓
Canonical domain state
      ↓
Administrator can find and understand it
      ↓
Administrator can perform authorised corrections
      ↓
Decision, reason, actor and outcome are audited
      ↓
Customer-facing state updates truthfully
```

## Admin delivery by phase

| Phase | Public/product delivery | Minimum Admin delivery |
|---|---|---|
| Decisions | Product, identity, privacy and URL boundaries | Administrator personas, authority levels, dual-approval rules and prohibited actions |
| Platform shell | Laravel, authentication, CI and basic UI | Admin authentication, MFA, permissions, navigation, list conventions and audit viewer |
| Core records | User, Party, Property and Listing | Search/view records, identity inspection, safe correction commands and history |
| Discovery | Search, detail pages and canonical routes | Visibility diagnosis, route/alias inspection, preview, blockers and safe repair |
| Owner journey | Draft, media, submission and publication | Review queue, verification, moderation, rejection reasons, approval and unpublish controls |
| Engagement | Enquiries, Conversations and notifications | Abuse handling, privacy controls, delivery failures and support investigation |
| Commerce | Products, Payments and Entitlements | Reconciliation, refunds, complimentary grants, corrections and approvals |
| Integrations | Inventory and distribution | Connection health, mapping failures, reconciliation queues, retries and credentials |
| Production | Migration and launch | Operational dashboards, incidents, privacy requests, audit exports and emergency controls |

## Minimal reusable Admin kernel

Build the following early, but keep it small until real screens prove what is reusable:

- separate Admin route area and navigation;
- MFA and explicit administrator permissions;
- Organisation and Portal scoping;
- governed tables, filters, sorting and pagination;
- standard record summary and history components;
- a common command pattern for consequential changes;
- mandatory reason capture where appropriate;
- consistent confirmation, success, blocked and partial-failure states;
- audit-event presentation;
- privacy-aware redaction;
- job status and retry presentation;
- permanent browser tests for critical Admin journeys.

## Command-oriented, not raw CRUD

Administrator actions should describe business transitions:

- approve or reject a Listing;
- suspend publication;
- replace the current Property address through an effective-dated command;
- merge duplicate Properties through the governed merge process;
- grant a complimentary Product with approval;
- retry a failed distribution;
- revoke Representation;
- resolve a verification case.

Administrators should not directly edit lifecycle codes, ownership foreign keys, Payment records, route claims, historical rows or privacy decisions.

Every consequential screen should answer:

1. What is the current canonical state?
2. Why is it in that state?
3. What prevents the desired outcome?
4. Which authorised commands are available?
5. What audit and downstream effects will the command create?

## Admin product areas

### Marketplace operations

- Property and Listing review;
- publication approval and withdrawal;
- media moderation;
- duplicate and merge management;
- address and route correction;
- Portal visibility and placement.

### Identity and authority

- User and Party investigation;
- Organisation membership;
- agent licence and agency authority;
- Ownership and Representation evidence;
- account recovery and external authentication issues.

### Customer support and safety

- Enquiry and Conversation investigation;
- abuse reports;
- privacy requests;
- consent and notification diagnosis;
- restricted and audited “view as” capability if accepted.

### Commercial operations

- Orders and Payments;
- refunds;
- Entitlements;
- complimentary grants;
- reconciliation and financial exceptions.

### Integration operations

- inventory connections;
- provider mappings;
- import failures;
- distribution receipts and retries;
- credential and webhook health.

### Platform governance

- Metadata and reference values;
- Portal, Domain and Brand configuration;
- feature/configuration controls;
- Audit and Incident records;
- migration and data-quality operations.

## Scope control

Classify each Admin screen as:

- `proposed`;
- `contract_ready`;
- `operational_mvp`;
- `production_hardened`.

Only the operational-MVP screens required by the current milestone should be implemented. A Visual Atlas image or screen-inventory entry must not imply that its underlying capability is active.

A capability should be counted as complete only when the intended person can use it, an authorised administrator can operate and recover it, important decisions are auditable, and both journeys pass through the real interface.

The unfinished Admin surface could plausibly account for another 30–50 percent of UI and application effort, depending on how many currently documented capabilities are activated. The efficient response is to activate fewer capabilities—not to build comprehensive Admin CRUD for every existing model.
