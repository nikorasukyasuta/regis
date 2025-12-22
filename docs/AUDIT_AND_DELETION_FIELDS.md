# Audit & Soft‑Delete Fields

## 1. Purpose

Audit and deletion fields ensure:
- Full traceability of changes
- Reversible operations
- Clear accountability
- Predictable system behavior

These guarantees are enforced at the schema level.

---

## 2. Standard Audit Fields (All Mutable Tables)

All mutable tables include the following fields:

- created_at (timestamp, not null)
- created_by (actor identifier, not null)
- updated_at (timestamp, not null)
- updated_by (actor identifier, not null)

Rules:
- created_* fields are set once
- updated_* fields change on every update
- Updates without updated_* changes are invalid

---

## 3. Soft‑Delete Fields (All Mutable Tables)

All mutable tables include:

- deleted_at (timestamp, nullable)
- deleted_by (actor identifier, nullable)

Rules:
- A record is considered deleted when deleted_at is not null
- Soft‑deleted records are excluded from normal queries by default
- Soft‑deleted records remain referencable
- Soft‑deleted records are never physically removed

Hard deletes are not permitted.

---

## 4. Append‑Only Tables

The following tables are append‑only:

- registration_notes
- communication_records
- audit_records

Append‑only rules:
- No updates
- No deletes
- Corrections require new records

---

## 5. Audit Record Storage

Audit records capture:

- Entity name
- Entity primary key
- Operation type (create, update, delete)
- Actor
- Source (UI, Excel, Email)
- Timestamp
- Before state
- After state

Audit records are immutable.

---

## 6. Deletion Semantics

Soft delete is treated as a state transition:

- Deletion generates an audit record
- Deletion records actor and timestamp
- Deletion does not cascade hard deletes
- Restoration clears deleted_* fields and generates an audit record

---

## 7. Design Guarantees

This design guarantees:

- No data loss through deletion
- Full historical traceability
- Clear accountability for all changes
- Safe recovery from user or system error
