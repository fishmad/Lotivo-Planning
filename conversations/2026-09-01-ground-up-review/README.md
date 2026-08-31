# Conversation Map — Ground-Up Lotivo Review

This directory maps the substantive user-visible conversation to the offline reports produced from it.

## Immutable source snapshot

The original Codex conversation snapshot is:

<https://chatgpt.com/s/cx_6a96073fa2e88191a8cc4273ba60f1f9>

The snapshot is immutable and does not update with later messages. It is an **anyone-with-the-link** URL. It is stored here only because this GitHub repository is private. Revoke the shared link through ChatGPT Data Controls if it should no longer be accessible.

The offline reports remain usable without the snapshot. The snapshot is the closest preserved source for the exact user-visible ordering and wording; it does not include raw tool calls, shell commands or hidden tool output.

## Conversation sequence

### 1. Independent do-over assessment

The user requested a ground-up review of the project documentation, GitHub history, governance, Laravel verification failures, repeated runs, effort, difficult implementation areas, sequencing, folder structure, Work Item/ADR numbering and lessons for a fresh Laravel rebuild.

The user specified that the review was read-only.

Result: [Independent ground-up review](../../reports/01-independent-ground-up-review.md)

### 2. The unfinished Admin product

The user identified that the graphical administrator interface had barely begun and could be a substantial project in its own right.

Result: [Admin as a parallel delivery lane](../../reports/02-admin-delivery-lane.md)

### 3. A new day-zero planning repository

The user asked what a new repository containing only planning, documentation and AI instructions should look like before starting from a fresh Laravel installation.

Result: [Day-zero repository model](../../reports/03-day-zero-repository-model.md)

### 4. What Lotivo is and how AI should deliver it

The user asked for a description of the project, its main elements and a dependency-ordered series of small milestones suitable for bounded AI implementation.

Result: [Component and milestone delivery map](../../reports/04-component-milestone-map.md)

### 5. Completeness and potentially missing functionality

The user asked what might still be missing from the proposed property-advertising operation.

Result: [Operational completeness checklist](../../reports/05-operational-completeness.md)

### 6. Offline preservation

The user asked how to save the conversation for offline reading and whether it could be placed in Git. The recommended approach was a private Markdown repository plus an immutable Codex snapshot and optional PDF.

The user then authorised creation and publication of this separate private planning repository.

## Preservation note

The reports preserve the substantive answers as cleaned, reusable documents rather than duplicating progress messages and tool logs. Consult the immutable snapshot when exact conversational wording or ordering is required.
