# Concurrency Assumptions

## 1. Concurrency Model Overview

The system assumes **low to moderate write concurrency** with occasional overlapping edits.

Concurrency is managed through:
- Explicit ownership rules
- Optimistic concurrency checks
- Conflict detection and surfacing
- Manual resolution when ambiguity exists

The system does not attempt to prevent all conflicts — it ensures they are visible and resolvable.

---

## 2. SQLite Write Assumptions

- SQLite allows only one writer at a time
- Writes are serialized through the backend service layer
- All writes occur within explicit transactions
- Long‑running write transactions are avoided

The system prioritizes **short, predictable writes** over throughput.

---

## 3. User Edit Assumptions

- Multiple users may view the same data concurrently
- Multiple users may attempt to edit overlapping records
- Users are not assumed to coordinate edits manually

The system must protect against:
- Lost updates
- Silent overwrites
- Partial writes

---

## 4. Optimistic Concurrency Rules

Mutable records include:
- A version field or timestamp
- A last‑modified indicator

When updating a record:
- The client must provide the last‑seen version
- If the version does not match, the update is rejected
- The user is informed of the conflict

No automatic retries or merges occur.

---

## 5. Excel Concurrency Assumptions

During the Excel transition period:

- Excel files may be edited concurrently by multiple users
- Excel data may become stale before import
- Excel imports may overlap with UI edits

Therefore:
- Excel imports never overwrite blindly
- Excel imports are validated against current SQLite state
- Conflicts are detected and surfaced
- Users must explicitly resolve conflicts

Excel is treated as **untrusted input**.

---

## 6. Email Concurrency Assumptions

- Emails may arrive out of order
- Emails may reference outdated information
- Emails may duplicate existing data

Therefore:
- Email ingestion never directly modifies core records
- Email‑derived changes require validation
- Conflicts are surfaced for review
- Duplicate detection is mandatory

---

## 7. Conflict Definition

A conflict exists when:
- A record has changed since it was last read
- An import attempts to modify protected fields
- Ownership rules are violated
- Validation rules fail due to concurrent changes

Conflicts are first‑class system events.

---

## 8. Conflict Resolution Rules

Conflicts must:
- Be visible to the user
- Require explicit resolution
- Be logged and auditable

The system WILL NOT:
- Auto‑merge conflicting edits
- Use last‑write‑wins
- Discard user input silently

Resolution outcomes are treated as new changes.

---

## 9. Failure Behavior

When concurrency ambiguity exists, the system favors:
- Rejecting the change
- Preserving existing data
- Requiring manual review

Silent failure or silent overwrite is never acceptable.

---

## 10. Design Guarantees

The system guarantees:
- No data is overwritten without visibility
- No conflict is hidden
- No write bypasses validation
- No concurrency behavior is implicit
