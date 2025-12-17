# Architecture Document v1  
## Centralized SQLite with Hub Sync

---

## 1. Scope and Goals

### 1.1 Purpose

This document defines the target architecture for a multi‑office, locally hosted application built on SQLite. The system is designed to support concurrent users across multiple offices, hybrid work‑from‑home access, Excel‑based transition workflows, and automated email ingestion—while preserving data integrity, auditability, and operational simplicity.

---

### 1.2 In Scope

- Centralized SQLite databases per office
- Application‑mediated access (no direct DB access)
- Two primary office nodes
- Hybrid WFH users connecting securely to either office
- Event‑based, batch‑oriented replication
- Ownership‑based edit controls
- Append‑only financial and communication data
- Excel import/export as an integration surface
- Email ingestion with staging and validation
- Full audit logging and traceability

---

### 1.3 Out of Scope

- Shared SQLite files on network drives
- Real‑time collaborative editing across offices
- Peer‑to‑peer mesh replication
- Excel as a system of record
- Silent conflict resolution
- Deletion or mutation of audit history

---

### 1.4 Architectural Goals

- **Reliability:** Offices operate independently during outages
- **Data Integrity:** No silent overwrites or corruption
- **Auditability:** Every change is traceable
- **Operational Simplicity:** SQLite retained as primary datastore
- **Scalability:** Additional offices without redesign
- **User Experience:** Immediate local feedback, predictable sync

---

### 1.5 Consistency Model

- Strong consistency within an office
- Eventual consistency across offices
- Expected convergence window: 1–5 minutes

---

### 1.6 Design Constraints

- SQLite remains the primary datastore
- Writes occur only through the application API
- Replication is asynchronous and event‑based
- Ownership rules enforced at the application layer
- Audit logging is mandatory

---

### 1.7 Success Criteria

- Offices operate independently without data loss
- Hybrid users work safely outside office hours
- Excel usage declines naturally
- Conflicts are rare and visible
- All offices converge reliably
- Recovery never requires manual data repair

---

## 2. System Topology

### 2.1 Components

#### Office Nodes (A and B)

Each office runs an identical node consisting of:
- Web UI and API server
- Local SQLite database
- Background jobs (email ingestion, Excel import/export)
- Replication agent

#### Sync Hub (Rivers Edge)

- Central event store
- Delivery tracking per node
- Event distribution
- Operational monitoring

#### Hybrid WFH Users

- Connect securely to either office node
- Treated identically to office users for audit and permissions

---

### 2.2 Availability Targets

| Component | Work Hours | Non‑Work Hours |
|--------|------------|----------------|
| Sync Hub (Rivers Edge) | 99% | 95% |
| Office Nodes | Best effort | Best effort |

Office nodes must continue operating during hub outages.

---

## 3. Ownership Model

### 3.1 Ownership Hierarchy

Ownership is defined at three levels:
1. League (default)
2. Session (override)
3. Registration (override)

Each owned entity includes:
- `home_office_id`

---

### 3.2 Edit Permissions

#### Home Office
- Full CRUD access (subject to role permissions)
- Ownership transfer authority
- Financial and status updates

#### Non‑Home Office
- Read‑only access to mutable fields
- Append‑only actions allowed:
  - Contact history
  - Notes
  - Communication logs
- Ownership transfer requests only

---

### 3.3 Ownership Transfer

- Explicit request
- Authorized approval
- Applied as a journaled event
- Replicates to all nodes
- Fully auditable

---

## 4. Data Model Strategy

### 4.1 Append‑Only Data (Preferred)

- Payments
- Contact history
- Notes
- Communication logs
- Audit records

Corrections are handled via voids or adjustments.

---

### 4.2 Mutable Data (Controlled)

- Customer demographics
- Registration attributes
- Session metadata
- League metadata

Mutable edits require optimistic concurrency checks.

---

## 5. Financial Data Model

### 5.1 Payment Ledger

- Payments recorded as append‑only entries
- Balances and paid status derived
- Direct balance edits prohibited

### 5.2 Corrections

- Void entries
- Adjustment entries
- No deletion or mutation of payment rows

---

## 6. Replication Architecture

### 6.1 Replication Strategy

- Replicate immutable events, not row diffs
- Events replay deterministically on each node
- Idempotent application

---

### 6.2 Event Requirements

Each event includes:
- Globally unique event ID
- Origin office ID
- Origin sequence number
- Timestamp
- Actor identity
- Entity reference
- Operation type
- Replayable payload

---

### 6.3 Sync Cadence

- Push events every 1–2 minutes
- Pull events every 1–2 minutes
- Exponential backoff on failure

---

## 7. Concurrency and Conflict Handling

### 7.1 Optimistic Concurrency

- Mutable rows include revision markers
- Updates require last‑seen revision
- Stale updates rejected

---

### 7.2 Conflict Resolution

- Conflicts surfaced explicitly
- Stored in conflict queue
- Resolved manually when required
- Resolution generates new events

---

## 8. Email Ingestion

### 8.1 Ingestion Flow

- Emails parsed into staging tables
- Validation and matching applied
- Human approval when confidence is low
- Apply generates journal events

---

## 9. Excel Integration

### 9.1 Governance

- Excel is not authoritative
- Imports go to staging
- Validation required
- Ownership rules enforced

### 9.2 Export

- Read‑only snapshots
- Scoped by ownership where possible

---

## 10. Hybrid Work‑From‑Home Access

- Secure access to either office node
- Ownership rules enforced regardless of location
- Append‑only actions always permitted
- Full edits only when connected to home office

---

## 11. Operations and Monitoring

### 11.1 Required Metrics

- Sync health per node
- Event backlog size
- Replay failures
- Ingestion backlogs
- SQLite performance

---

### 11.2 Failure Behavior

- Hub outage: offices continue locally
- Node outage: other office unaffected
- Network partition: eventual convergence

---

## 12. Backup and Recovery

### 12.1 Office Nodes

- WAL‑compatible SQLite backups
- Daily incremental, weekly full
- Restore via backup + event replay

### 12.2 Hub

- Event store backed up frequently
- Delivery cursors preserved
- Hub is authoritative convergence record

---

## 13. Schema‑Level Requirements

- `home_office_id` on leagues, sessions, registrations
- Revision markers on mutable tables
- Append‑only event journal
- Staging tables for email and Excel
- Conflict queue storage

---

## 14. Rollout Plan

### Stage 1
- Single office deployment
- Audit and journal enabled
- Excel and email via staging

### Stage 2
- Second office added
- Hub deployed
- Replication validated

### Stage 3
- Hybrid WFH access enabled
- Monitoring and recovery drills

---

## 15. Final Statement

This architecture prioritizes safety, auditability, and operational clarity over premature optimization. It is designed to survive real‑world usage, human error, and infrastructure failures without silent data loss.

If a decision must be made under uncertainty, favor explicit action, auditability, and recoverability.

---
