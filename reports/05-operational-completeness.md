# Lotivo Operational Completeness Checklist

Lotivo already has broad conceptual coverage. The largest remaining completeness risk is the operational last 20 percent: correction, recovery, support, abuse handling, failure states, lifecycle endings and production ownership.

A system is incomplete if it can create a record but cannot safely correct, suspend, reconcile, explain or recover it.

## 1. Complete Listing lifecycle

Define customer, public, Admin, audit and Search behaviour for:

- draft abandonment;
- submission withdrawal;
- rejection and resubmission;
- scheduled publication;
- expiry;
- withdrawal;
- sold/rented/completed;
- relisting as a new campaign;
- suspension for investigation;
- administrative takedown;
- restoration after an incorrect takedown;
- Property correction without rewriting Listing history.

## 2. Disputes, appeals and corrections

Provide an operational path for claims such as:

- “I own this Property; that advertiser does not.”
- “This Listing is fraudulent.”
- “The Property details are wrong.”
- “My Listing was rejected incorrectly.”
- “The agency relationship is incorrect.”
- “Private information has been exposed.”
- “This Property has been duplicated.”
- “This Payment or refund is wrong.”

A sophisticated case system is not initially necessary. A bounded case needs intake, ownership, evidence, restricted notes, status/priority, governed resolution, communication and audit.

## 3. Trust, safety and moderation

Define controls for:

- scam and impersonation Listings;
- stolen media;
- misleading price/availability claims;
- discriminatory or unlawful advertising content;
- spam Enquiries;
- abusive Messages;
- malicious links/uploads;
- repeat offenders;
- urgent takedown;
- legal or regulator requests.

Public Listings and private communications need simple reporting mechanisms.

## 4. Account lifecycle and recovery

- email verification;
- password reset;
- lost second factor;
- compromised-account response;
- safe email changes;
- external login linking/unlinking;
- administrator recovery;
- suspended/closed accounts;
- data export/deletion requests;
- preservation of required historical business records;
- restricted, conspicuous and audited impersonation if accepted.

## 5. Communication delivery

- Portal-branded templates;
- delivery status;
- bounce and complaint handling;
- unsubscribe handling;
- retry and deduplication;
- rate limiting;
- provider outage;
- preferences versus marketing consent;
- required operational messages;
- Admin delivery diagnosis.

A queued message is not the same as a delivered outcome.

## 6. Media lifecycle

- malware/content scanning;
- EXIF and location metadata removal;
- orientation and transformations;
- copyright complaints;
- abandoned temporary uploads;
- replacement/removal;
- private/public access;
- CDN/cache invalidation;
- retention after expiry;
- failed processing and repair;
- quota and cost monitoring.

## 7. Privacy operation

- privacy-request intake;
- requester identity verification;
- access/export;
- correction;
- deletion or lawful retention decision;
- consent history;
- retention schedules;
- restricted Admin access;
- sensitive-field redaction;
- Audit access control;
- data-breach response;
- exact-address exposure monitoring.

Final Australian legal/privacy policy requires specialist review rather than AI inference.

## 8. Financial exceptions

- abandoned checkout;
- successful Payment with failed Entitlement;
- duplicate webhook or charge;
- partial/full refund;
- chargeback;
- cancelled Product;
- GST/tax invoices;
- complimentary grants;
- manual reconciliation;
- provider outage;
- amount/currency disagreement;
- accounting export;
- financial retention.

Payment remains separate from publication approval.

## 9. Search quality

- no-results and sparse-supply behaviour;
- misspelled/ambiguous locations;
- stale or withdrawn Listings;
- external index removal;
- duplicate results;
- map/result disagreement;
- sorting fairness;
- sponsored placement disclosure;
- bot/scraper traffic;
- sitemap freshness;
- canonical redirect consistency;
- urgent privacy takedown.

## 10. Accessibility and mobile use

- keyboard-only operation;
- screen-reader semantics;
- focus management;
- clear errors;
- contrast;
- touch targets;
- narrow screens;
- large Admin tables;
- image alternative text;
- reduced motion;
- non-map alternatives.

Admin accessibility is not optional merely because the interface is internal.

## 11. Data quality and repair

Create governed diagnosis and repair for:

- duplicate Properties;
- wrong address association;
- stale effective relationships;
- incorrect metadata mappings;
- orphaned media;
- failed projection updates;
- incorrect route aliases;
- imported records missing provenance;
- disagreement between publication and Search;
- incomplete Payment/Entitlement outcomes.

Use the sequence: diagnose, preview, authorise, execute, verify and audit.

## 12. Production operations

Before launch, answer:

- Who receives each alert?
- How is a failed job retried?
- How are backups restored?
- What are the recovery objectives?
- How is a bad deployment rolled back?
- How are schema migrations rehearsed?
- What happens when email, storage, Payment, maps or distribution fails?
- How are DNS, certificates and Portal Domains monitored?
- How are secrets rotated?
- How is emergency publication takedown performed?
- How is an Incident communicated?

The evidence should be a successful restoration/rollback rehearsal, not merely a written procedure.

## Potentially underdeveloped business capability

These need explicit include/defer decisions:

- public content and help centre;
- contact, complaint and support intake;
- Listing performance reporting;
- business analytics and conversion measurement;
- SEO content governance;
- meaningful operational dashboards;
- safe bulk operations with previews;
- review/verification/support service levels;
- agency onboarding/offboarding;
- Portal-specific legal documents and communication;
- price changes and Entitlement grandfathering;
- feature rollout/configuration controls;
- accounting/support/privacy exports;
- provider or Portal decommissioning.

## Launch completeness scenarios

Demonstrate through the real interface:

1. A legitimate owner submits and publishes a Listing.
2. An administrator rejects it and the owner corrects/resubmits it.
3. A Listing is reported as fraudulent and urgently removed.
4. Two parties dispute authority over one Property.
5. A duplicate Property is resolved without losing Listing history.
6. A Payment succeeds while Entitlement initially fails.
7. A destination rejects a Listing and an administrator repairs/retries it.
8. A customer loses account access without losing durable Party/Listing history.
9. Default-deny controls prevent inappropriate exact-address exposure.
10. A Portal Domain changes while existing public URLs remain safe.
11. A provider outage recovers without duplicate publication, Messages or charges.
12. A production backup restores and important records reconcile.

If a scenario can only be repaired through direct database editing, the operational capability is incomplete.

## Universal capability checklist

For every material capability, ask:

```text
Can someone initiate it?
Can the correct person view it?
Is authority enforced?
Can an administrator diagnose it?
Can it be corrected safely?
Can it be cancelled, expired or revoked?
What happens when it partially fails?
Can it be retried without duplication?
Is the customer told the truth?
Is private information protected?
Is important history preserved?
Can support explain what happened?
Can it be monitored?
Can it be migrated and restored?
Is there a permanent journey test?
```

Attach this operational-completeness checklist to every milestone rather than creating another speculative final phase.

## Deliberate exclusions

Offers, bidding, negotiation, deposits, escrow, conveyancing, settlement and vendor–buyer accounting remain deliberately outside Lotivo. They are different businesses, not missing Lotivo features.
