# Source‑of‑Truth Rules

## 1. Authoritative Data Source

SQLite is the **single authoritative source of truth** for all persisted system data.

All final state must exist in SQLite.
No other system may override SQLite state directly.

---

## 2. Excel Role

Excel is a **transitional integration surface**, not a datastore.

Excel MAY:
- Be used for bulk data entry during transition
- Be used for reporting and review
- Export snapshots of SQLite data

Excel MAY NOT:
- Be treated as authoritative
- Write directly to core tables
- Silently overwrite existing records
- Resolve conflicts automatically

All Excel imports must:
- Pass through staging tables
- Be validated before application
- Generate auditable changes

---

## 3. Email Role

Email is an **ingestion mechanism**, not a data source.

Email MAY:
- Provide structured input for new or updated records
- Reduce manual data entry

Email MAY NOT:
- Directly modify core tables
- Bypass validation rules
- Override existing data without review

All email‑derived data must:
- Be parsed into staging
- Be validated
- Be auditable
- Allow manual review when confidence is low

---

## 4. Write Authority Rules

Only the backend service layer may write to SQLite.

Direct writes are prohibited from:
- Excel
- Email
- Scripts
- UI clients

All writes must:
- Occur within transactions
- Enforce validation rules
- Generate audit records

---

## 5. Conflict Resolution Rules

Conflicts must be:
- Detected explicitly
- Surfaced to the user
- Resolved intentionally

The system WILL NOT:
- Use last‑write‑wins
- Resolve conflicts silently
- Overwrite data without visibility

Conflict resolution requires:
- User review
- Explicit confirmation
- Audit logging

---

## 6. Temporal Authority

The most recent timestamp does NOT automatically win.

Authority is determined by:
- Ownership rules
- Validation success
- Explicit user action

---

## 7. Audit Guarantees

Every accepted change must:
- Be traceable to a source (UI, Excel, Email)
- Record who initiated it
- Record when it occurred
- Preserve previous state

No data change is valid without an audit trail.

---

## 8. Failure Behavior

When ambiguity exists, the system favors:
- Rejecting the change
- Requiring manual review
- Preserving existing data

Silent failure or silent overwrite is never acceptable.
