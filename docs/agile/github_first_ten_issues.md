Issue 1 — Define System Scope & Non‑Goals
Epic: Architecture & Decisions Labels: design, blocking

Goal: Clearly define what this system will not do.

Acceptance Criteria

Scope document written

Explicit non‑goals listed

Reviewed once after writing

Issue 2 — Define Source‑of‑Truth Rules
Epic: Architecture & Decisions Labels: architecture, blocking

Goal: Eliminate ambiguity between SQLite, Excel, and Email.

Acceptance Criteria

SQLite = authoritative

Excel = transitional

Email = ingestion only

Rules documented

Issue 3 — Define Concurrency Assumptions
Epic: Architecture & Decisions Labels: architecture, high-risk

Goal: Document how concurrent edits are handled during Excel coexistence.

Acceptance Criteria

Ownership rules defined

Conflict detection strategy documented

No silent overwrite paths

Issue 4 — Write Architecture v1 Document
Epic: Architecture & Decisions Labels: documentation, blocking

Goal: Lock architectural decisions before coding.

Acceptance Criteria

Architecture v1 written

Reviewed after 24 hours

No open questions

Issue 5 — Finalize SQLite Schema
Epic: Database & Schema Labels: schema, blocking

Goal: Freeze the data model.

Acceptance Criteria

Tables finalized

Relationships defined

No TODOs in schema

Issue 6 — Add Audit Fields & Metadata
Epic: Database & Schema Labels: schema, audit

Goal: Ensure every record is traceable.

Acceptance Criteria

created_at / updated_at

created_by / updated_by

Immutable audit tables defined

Issue 7 — Define Schema Migration Strategy
Epic: Database & Schema Labels: schema, blocking

Goal: Prevent schema drift.

Acceptance Criteria

Migration approach documented

Versioning defined

Rollback strategy noted

Issue 8 — Implement Excel Staging Tables
Epic: Excel Integration Labels: excel, high-risk

Goal: Prevent Excel from writing directly to core tables.

Acceptance Criteria

Staging tables exist

No direct Excel → core writes

Validation hooks planned

Issue 9 — Excel Import → Staging Pipeline
Epic: Excel Integration Labels: excel, high-risk

Goal: Safely ingest Excel data.

Acceptance Criteria

Import works

Validation errors visible

No silent failures

Issue 10 — SQLite → Excel Export
Epic: Excel Integration Labels: excel

Goal: Allow users to keep Excel for reporting.

Acceptance Criteria

Export matches schema

Read‑only intent documented

Round‑trip tested