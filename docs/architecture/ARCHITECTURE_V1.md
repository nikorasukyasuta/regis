# Architecture v1
**Full‑stack local application with SQLite, Excel transition, and email automation**

---

## 1. Purpose

This system exists to replace Excel-centric workflows for registration and contact tracking with a normalized, auditable SQLite-backed application. It prioritizes data integrity, traceability, and predictable failure behavior over convenience or automation.

---

## 2. Scope and non-goals

### 2.1 In scope
- **SQLite authoritative datastore:** All final state persists in SQLite.
- **Backend service layer:** All writes are executed via the backend (no direct DB writes).
- **Excel transition:** Excel import/export supported via staging and validation.
- **Email ingestion:** Supported email formats can be parsed into staging for review/application.
- **Auditability:** All accepted changes are traceable and reviewable.

### 2.2 Out of scope
- **Excel as truth:** Excel is never authoritative.
- **Direct SQLite editing:** No user or tool edits SQLite files directly.
- **Silent conflict resolution:** No last-write-wins, no auto-merge of conflicting edits.
- **Arbitrary email parsing:** Only explicitly supported formats are ingested.
- **Cloud scale guarantees:** This v1 targets local/small-team usage.

---

## 3. System topology

### 3.1 Components
- **Frontend UI:** Primary workflow surface for users (create/edit/search/review).
- **Backend API / service layer:** Validation, transactions, audit hooks, access control.
- **SQLite database:** Authoritative persistence.
- **Staging subsystems:**
  - **Excel staging:** Receives imports, validates, applies approved changes.
  - **Email staging:** Receives parsed email data, validates, applies approved changes.

### 3.2 Data flow summary
1. **User/UI → Backend → SQLite** for interactive changes
2. **Excel → Staging → Validation → Apply → SQLite** for bulk transition workflows
3. **Email → Parsing → Staging → Validation/Review → Apply → SQLite** for automation

---

## 4. Source-of-truth rules

- **SQLite is authoritative:** SQLite is the single source of truth for persisted state.
- **Excel is transitional:** Excel is an integration surface for import/export only.
- **Email is ingestion:** Email provides untrusted input that must be staged and validated.
- **Write authority:** Only the backend may write to SQLite.
- **Conflict philosophy:** Conflicts are surfaced, not hidden.

(See also: `docs/SOURCE_OF_TRUTH_RULES.md`.)

---

## 5. Concurrency assumptions

### 5.1 Model
- **Expected concurrency:** MODERATE write concurrency with occasional overlapping edits.
- **SQLite writer behavior:** Single-writer constraint acknowledged; writes are short and transactional.

### 5.2 Optimistic concurrency
- **Mutable records include:** [row_version and updated_at]
- **Update rule:** Client supplies last-seen version; mismatch rejects the update.
- **No auto-merge:** System does not merge conflicting edits automatically.

### 5.3 Conflict handling
- **Detection:** Version mismatch, ownership violations, and validation failures create conflicts.
- **Visibility:** Conflicts are visible in UI and logs.
- **Resolution:** Manual, explicit, auditable.

(See also: `docs/CONCURRENCY_ASSUMPTIONS.md`.)

---

## 6. Data model and integrity

### 6.1 Core entities
The system includes (at minimum):
- customers
- address (normalized)
- individual_registration
- team_registration
- registration_status
- contact_status (communication history)
- registration_notes (immutable notes)
- leagues / sessions / sports (lookups)
- tshirt_sizes / competition_levels (lookups)

### 6.2 Integrity rules
- **Foreign keys enforced**
- **Validation at service layer**
- **No partial writes:** multi-table operations are transactional
- **Preferred append-only patterns** for notes and communication history

### 6.3 Deletion policy
- **Policy:** [SOFT DELETE ONLY]
- **Audit preservation:** prior state remains reviewable regardless of deletion policy

---

## 7. Audit model

### 7.1 Requirements
Every accepted change must record:
- **Actor:** user/service identity
- **Source:** UI / Excel / Email
- **Timestamp**
- **Entity and keys**
- **Before/after state:** DIFF

Audit records are immutable.

### 7.2 Audit coverage guarantee
All writes flow through the backend; audit hooks are executed within the same transaction as the write.

---

## 8. Excel transition layer

### 8.1 Excel role
Excel is used for:
- Bulk entry during transition
- Reporting snapshots

Excel is not authoritative.

### 8.2 Import workflow
1. Excel import writes to **staging**
2. Staging data is validated (schema + business rules)
3. Conflicts are surfaced (no blind overwrite)
4. Approved changes are applied via backend services
5. Application produces audit records

### 8.3 Export workflow
- Exports are read-only snapshots of current SQLite state.
- Export format aligns with defined templates to support transition workflows.

---

## 9. Email ingestion and automation

### 9.1 Supported formats
- **Supported:** Auto Generated Email Response's
- **Unsupported:** routed to manual review; never partially applied

### 9.2 Ingestion workflow
1. Receive email metadata + body
2. Parse into structured fields
3. Write raw + parsed fields to **staging**
4. Validate against SQLite state
5. Detect duplicates and conflicts
6. Require review when ambiguous
7. Apply approved changes via backend services
8. Produce audit records for all outcomes

### 9.3 Failure behavior
- Preserve existing data
- Log failure with visibility
- Keep staged record for later review

---

## 10. Security and access control

### 10.1 Authentication
- **Auth mechanism:** [TBD: local accounts / Windows auth / etc.]

### 10.2 Authorization
- Role-based permissions for create/edit/import/apply/review.
- Sensitive operations require explicit privilege.

### 10.3 Secret handling
- Email credentials and any tokens are stored outside source control.
- `.env.example` documents required env vars.

---

## 11. Operations

### 11.1 Backups
- SQLite backups are automated and tested.
- Backup approach: [WAL-COMPATIBLE FILE COPY / ONLINE BACKUP API / EXCEL FILE]

### 11.2 Recovery
- Restore procedure is documented and tested.
- Recovery does not require manual DB edits.

### 11.3 Observability
Minimum required visibility:
- Import failures (Excel/email)
- Validation failures
- Conflict occurrences
- Audit write failures
- SQLite lock/write contention events

---

## 12. Decisions and constraints

- **SQLite remains authoritative for v1**
- **No direct writes outside backend**
- **No silent conflict resolution**
- **Staging is mandatory for Excel and Email**
- **Audit is mandatory and immutable**

Decision log entries are tracked in `docs/rules/DECISIONS.md`.

---
