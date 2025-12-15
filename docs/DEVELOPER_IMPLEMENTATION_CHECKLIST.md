# ✅ Developer Implementation Checklist  
**Architecture v1 — Centralized SQLite with Hub Sync**

---

## 1. Foundation & Project Setup

### Repository & Structure
- [ ] Create mono‑repo or clearly versioned multi‑repo structure
- [ ] Separate concerns:
  - API / backend
  - UI / frontend
  - Replication / sync
  - Ingestion (email, Excel)
  - Shared domain models
- [ ] Establish environment configuration strategy
- [ ] Define logging and error‑handling conventions

---

## 2. SQLite Configuration & Access Layer

### SQLite Setup
- [ ] Enable WAL mode
- [ ] Configure busy timeout
- [ ] Enforce foreign keys
- [ ] Ensure all DB access goes through application layer
- [ ] Implement transaction boundaries for all writes

### Data Access Layer
- [ ] Centralize all queries and mutations
- [ ] Prevent ad‑hoc SQL execution
- [ ] Add integrity checks for critical tables

---

## 3. Schema Enhancements (Required for Architecture v1)

### Ownership
- [ ] Add `home_office_id` to:
  - leagues
  - sessions
  - registrations
- [ ] Implement inheritance logic:
  - registration → session → league

### Revision Markers
- [ ] Add `row_version` and/or `updated_at` to mutable tables
- [ ] Enforce revision checks on updates

### Append‑Only Structures
- [ ] Payment ledger table
- [ ] Contact history table
- [ ] Notes table
- [ ] Communication log table
- [ ] Audit event tables (immutable)

---

## 4. Event Journal (Core Requirement)

### Local Event Journal
- [ ] Create append‑only event table
- [ ] Generate globally unique event IDs
- [ ] Track origin office and sequence number
- [ ] Store replayable payloads
- [ ] Ensure journal writes occur in same transaction as data changes

### Idempotency
- [ ] Detect and ignore duplicate event IDs
- [ ] Ensure replay safety

---

## 5. Replication Agent (Office Nodes)

### Push Logic
- [ ] Batch unsent events
- [ ] Push to hub on schedule
- [ ] Handle retries with backoff
- [ ] Mark events as delivered only after hub ack

### Pull Logic
- [ ] Track last received cursor
- [ ] Pull unseen events
- [ ] Replay events deterministically
- [ ] Handle partial batch failures

---

## 6. Sync Hub Implementation

### Event Store
- [ ] Durable append‑only event storage
- [ ] Per‑office delivery cursors
- [ ] Acknowledgement tracking

### APIs
- [ ] Push events endpoint
- [ ] Pull events endpoint
- [ ] Acknowledge delivery endpoint

### Monitoring
- [ ] Backlog per office
- [ ] Last sync timestamps
- [ ] Error and retry metrics

---

## 7. Ownership Enforcement

### Authorization Rules
- [ ] Enforce home office checks on mutable edits
- [ ] Allow append‑only actions from non‑home offices
- [ ] Block unauthorized financial edits

### Ownership Transfer Workflow
- [ ] Transfer request creation
- [ ] Approval flow
- [ ] Apply transfer as event
- [ ] Replicate transfer

---

## 8. Concurrency Control

### Optimistic Concurrency
- [ ] Require revision markers on updates
- [ ] Reject stale updates
- [ ] Return conflict metadata to UI

### Conflict Queue
- [ ] Store failed replays
- [ ] Provide admin resolution tools
- [ ] Apply resolutions as new events

---

## 9. Financial Logic

### Payment Ledger
- [ ] Append‑only payment entries
- [ ] Derived balance calculations
- [ ] Status derivation (paid, partial, unpaid)

### Corrections
- [ ] Void payment support
- [ ] Adjustment payment support
- [ ] Prohibit direct edits/deletes

---

## 10. Email Ingestion

### Ingestion Pipeline
- [ ] Mailbox connectivity
- [ ] Parsing logic
- [ ] Staging tables
- [ ] Validation rules

### Apply Workflow
- [ ] Human approval when needed
- [ ] Apply generates journal events
- [ ] Replication of applied changes

---

## 11. Excel Integration

### Import
- [ ] Parse Excel into staging
- [ ] Validate data
- [ ] Enforce ownership rules
- [ ] Apply via events

### Export
- [ ] Generate read‑only snapshots
- [ ] Scope exports by ownership
- [ ] Prevent accidental re‑import without validation

---

## 12. Hybrid WFH Support

- [ ] Secure access to both office nodes
- [ ] Attribute actions to correct office/user
- [ ] Enforce ownership regardless of connection location

---

## 13. Audit & Observability

### Audit Logging
- [ ] Capture before/after state
- [ ] Record actor, timestamp, origin office
- [ ] Ensure audit records are immutable

### Metrics & Logs
- [ ] Sync health
- [ ] Event backlog size
- [ ] Replay failures
- [ ] Ingestion backlogs

---

## 14. Backup & Recovery Hooks

### Office Nodes
- [ ] WAL‑compatible SQLite backups
- [ ] Restore + replay workflow tested

### Hub
- [ ] Event store backups
- [ ] Cursor restoration tested

---

## 15. Testing & Validation

### Functional Tests
- [ ] CRUD operations
- [ ] Ownership enforcement
- [ ] Payment workflows

### Replication Tests
- [ ] Offline office → reconverge
- [ ] Duplicate event replay
- [ ] Partial batch failure recovery

### Disaster Scenarios
- [ ] Node restore
- [ ] Hub restore
- [ ] Conflict resolution

---

## 16. Deployment Readiness

- [ ] Configuration documented
- [ ] Secrets management in place
- [ ] Monitoring dashboards live
- [ ] Runbooks validated
- [ ] Governance rules enforced in code

---

## 17. Definition of Done

The system is considered implementation‑complete when:

- [ ] No direct SQLite access exists
- [ ] All writes generate events
- [ ] Offices converge reliably
- [ ] Conflicts are visible and resolvable
- [ ] Recovery never requires manual data edits
- [ ] Audit trail is complete and immutable

---
