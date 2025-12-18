# UI Behavior Rules

## 1. Role of the UI

The UI is a **workflow and visibility layer**, not a source of truth.

The UI:
- Collects user intent
- Displays current system state
- Surfaces conflicts and validation errors
- Enables review and resolution

The UI does not:
- Decide authoritative state
- Resolve conflicts automatically
- Bypass backend validation

---

## 2. Read Behavior

- All displayed data reflects current SQLite state
- Soft‑deleted records are hidden by default
- Historical/audit views explicitly include deleted records
- Stale data is not silently refreshed during edits

---

## 3. Edit Behavior

- Edits are always explicit user actions
- Editable fields are clearly distinguished from read‑only fields
- The UI submits:
  - Proposed changes
  - Last‑seen version identifier
- The UI does not assume edits will succeed

---

## 4. Save & Validation Flow

When a user saves changes:

1. UI submits changes to backend
2. Backend validates and checks concurrency
3. Backend returns:
   - Success
   - Validation errors
   - Conflict errors
4. UI reflects the outcome explicitly

The UI never:
- Assumes last‑write‑wins
- Retries silently
- Applies partial updates

---

## 5. Conflict Handling in UI

When a conflict occurs:

- The UI informs the user clearly
- The UI displays:
  - Current persisted values
  - User’s attempted changes
- The user must explicitly choose how to proceed

Conflict resolution is:
- Intentional
- Visible
- Auditable

---

## 6. Audit Visibility

The UI provides access to:
- Change history per record
- Actor, timestamp, and source
- Soft‑delete events
- Conflict resolution outcomes

Audit data is:
- Read‑only
- Immutable
- Clearly distinguished from editable data

---

## 7. Excel & Email Review UI

The UI supports review workflows for:
- Excel staging records
- Email staging records

Review UI must:
- Show raw input
- Show parsed fields
- Show validation results
- Require explicit approval before application

---

## 8. Failure & Error Behavior

When errors occur, the UI:
- Displays clear error messages
- Preserves user input when possible
- Never hides failures
- Never silently discards changes

---

## 9. Design Guarantees

The UI guarantees:
- No silent data changes
- No hidden conflicts
- No bypassed validation
- No implicit state transitions
