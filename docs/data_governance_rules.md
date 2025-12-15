Below is a **complete, repository‑ready `DATA_GOVERNANCE_RULES.md`** that aligns precisely with the architecture, ownership model, and operational realities you’ve defined.

This document focuses on **who may change what, how changes are validated, how conflicts are prevented, and how trust is preserved over time**.

---

# 📜 DATA_GOVERNANCE_RULES.md  
**Centralized SQLite · Office Ownership · Event‑Based Sync**

---

## 1. Purpose

This document defines the **rules, responsibilities, and constraints** governing data creation, modification, synchronization, and auditability across the system.

Its goals are to:
- Prevent silent data corruption
- Minimize cross‑office conflicts
- Preserve auditability and trust
- Enable safe Excel and email integration
- Support eventual convergence across offices

---

## 2. Core Governance Principles

1. **SQLite is the system of record**
2. **Excel is an integration surface, not a datastore**
3. **All changes are auditable**
4. **Ownership determines edit authority**
5. **Append‑only data is preferred**
6. **Conflicts are surfaced, not hidden**
7. **Replication must converge deterministically**

---

## 3. Data Ownership Model

### Ownership Hierarchy

Ownership is defined at three levels, with inheritance:

1. **League**
2. **Session** (optional override)
3. **Registration** (optional override)

Inheritance rules:
- Registration inherits from Session if present
- Session inherits from League if present
- League always has a home office

### Ownership Field

Each owned entity includes:
- `home_office_id`

---

## 4. Edit Authority Rules

### Home Office Permissions

If the user is connected to the entity’s home office node:

- Full CRUD access (subject to role permissions)
- May transfer ownership
- May apply Excel and email‑derived changes

### Non‑Home Office Permissions

If the user is connected to a non‑home office node:

Allowed:
- Read access
- Append‑only actions:
  - Contact history entries
  - Registration notes
  - Communication logs
- Ownership transfer requests

Restricted:
- Direct edits to mutable core fields
- Financial corrections
- Status overrides

---

## 5. Ownership Transfer Rules

### Transfer Requirements

- Transfer must be explicit
- Transfer must be authorized
- Transfer must be auditable

### Transfer Workflow

1. Transfer request created
2. Authorized approver reviews request
3. Approval generates an ownership‑change event
4. Event replicates to all nodes
5. New ownership takes effect everywhere

### Transfer Constraints

- Transfers should be rare
- Transfers must not be automatic
- Transfers must not bypass audit logging

---

## 6. Data Classification

### Append‑Only Data (Preferred)

These records must never be edited after creation:

- Payment entries
- Contact history
- Registration notes
- Communication logs
- Audit records

Corrections are handled via:
- Void entries
- Adjustment entries
- Follow‑up notes

### Mutable Data (Controlled)

These records may be edited, subject to ownership and concurrency rules:

- Customer demographic fields
- Registration attributes
- Session metadata
- League metadata

---

## 7. Financial Data Governance

### Payment Model

- Payments are recorded as **append‑only ledger entries**
- Balances and paid status are derived
- Direct balance edits are prohibited

### Corrections

Allowed:
- Void payment entry
- Adjustment payment entry

Prohibited:
- Editing or deleting payment rows
- Overwriting derived totals without audit

---

## 8. Concurrency and Conflict Rules

### Optimistic Concurrency

- Mutable rows include revision markers
- Updates require last‑seen revision
- Stale updates are rejected

### Conflict Handling

- Conflicts are surfaced to users
- Conflicts are logged and auditable
- Conflicts must be resolved explicitly

### Conflict Queue

- Unresolvable events are placed in a conflict queue
- Conflicts require human resolution
- Resolutions generate new events

---

## 9. Replication Governance

### Event‑Based Replication

- Only **events** replicate between offices
- Events are immutable
- Events must be idempotent

### Event Requirements

Each event must include:
- Unique event ID
- Origin office
- Actor identity
- Timestamp
- Entity reference
- Operation type
- Payload sufficient for replay

### Replay Rules

- Events must apply deterministically
- Duplicate events must be ignored safely
- Failed replays must be logged and surfaced

---

## 10. Excel Governance Rules

### Excel Is Not Authoritative

- Excel files must never be treated as the source of truth
- Excel edits do not bypass validation or ownership rules

### Import Rules

- All imports go to staging
- Validation is mandatory
- Ownership rules are enforced
- Apply generates journal events

### Export Rules

- Exports are read‑only snapshots
- Re‑importing exports requires validation
- Prefer scoped exports (by league/session/office)

---

## 11. Email Ingestion Governance

### Ingestion Rules

- Emails are parsed into staging
- No direct writes to core tables
- Validation and matching required

### Apply Rules

- Applying staged email data generates events
- Ownership rules apply
- Low‑confidence matches require human approval

---

## 12. Audit Governance

### Audit Guarantees

- Every accepted change produces an audit record
- Audit records are immutable
- Audit records replicate with events

### Audit Visibility

Audit records must include:
- Who made the change
- When the change occurred
- What changed
- Before/after state (where applicable)
- Origin office

---

## 13. Data Retention Rules

### Core Data

- Retained indefinitely unless legally required otherwise

### Audit Data

- Retained indefinitely
- Never deleted or modified

### Staging Data

- Retained until applied or rejected
- Periodic cleanup allowed after resolution

---

## 14. Schema Evolution Governance

### Change Rules

- Schema changes must be backward compatible where possible
- Schema changes must be coordinated across nodes
- Replication must be paused during incompatible migrations

### Migration Rules

- Migrations must be versioned
- Migrations must be reversible where feasible
- Migration events must be auditable

---

## 15. Prohibited Practices

The following are explicitly forbidden:

- Editing SQLite files directly
- Sharing SQLite files over a network drive
- Bypassing the API for writes
- Treating Excel as authoritative
- Deleting audit records
- Silent conflict resolution

---

## 16. Governance Review Cadence

### Monthly

- Review ownership transfers
- Review conflict queue
- Review financial adjustments

### Quarterly

- Review governance rules
- Review audit completeness
- Review Excel usage patterns

---

## 17. Governance Enforcement

Governance rules are enforced by:
- Application logic
- API validation
- Ownership checks
- Audit logging
- Operational monitoring

Violations must:
- Be logged
- Be visible
- Be correctable

---

## 18. Final Statement

This governance model is designed to:
- Protect data integrity
- Preserve trust
- Enable growth
- Support real users under real conditions

If a rule is unclear, **favor safety, auditability, and explicit action over convenience**.

---

If you want next, I can produce:
- `DISASTER_RECOVERY_PLAYBOOK.md`
- A **one‑page governance summary for non‑technical stakeholders**
- Or a **developer‑focused enforcement checklist**

Just say which one you want.