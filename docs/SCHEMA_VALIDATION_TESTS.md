# Schema Validation Tests

## 1. Purpose

Schema validation tests verify that:
- Invalid data cannot be persisted
- Referential integrity is enforced
- Audit and deletion rules cannot be bypassed
- Concurrency safeguards are structurally supported

These tests validate the schema, not application logic.

---

## 2. Referential Integrity Tests

### 2.1 Foreign Key Enforcement

Tests:
- Inserting a registration with a non‑existent customer_id fails
- Inserting a registration with a non‑existent event_id fails
- Inserting a communication_record with invalid lookup references fails

Expected result:
- Database rejects the operation

---

## 3. Lookup Table Enforcement

Tests:
- Inserting a registration with an invalid status_id fails
- Inserting a communication_record with an invalid communication_type_id fails

Expected result:
- Database rejects invalid enum values

---

## 4. Soft‑Delete Semantics

Tests:
- Soft‑deleted parent records remain referencable
- Hard delete attempts fail or are prohibited
- Soft‑deleted records are excluded from default queries

Expected result:
- No data loss