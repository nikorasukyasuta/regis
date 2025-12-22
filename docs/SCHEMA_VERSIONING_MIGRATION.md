# Schema Versioning & Migration Strategy

## 1. Purpose

Schema versioning ensures:
- Controlled evolution of the database
- Reproducible environments
- Safe upgrades and recoveries
- Clear auditability of structural changes

---

## 2. Schema Versioning Model

- The schema has a single authoritative version number
- The version is stored in the database
- The version reflects the latest applied migration

Example:
- schema_version = 5

---

## 3. Migration Structure

Migrations are:
- Ordered
- Immutable
- Applied sequentially

Each migration:
- Has a unique, increasing identifier
- Describes exactly one logical change
- Is applied exactly once

Example naming:
- 001_initial_schema.sql
- 002_add_audit_fields.sql
- 003_add_soft_delete_fields.sql

---

## 4. Migration Rules

- Migrations are the only way to change schema
- No migration modifies or deletes previous migrations
- Migrations are committed to source control
- Migrations are reviewed before application

Manual database edits are prohibited.

---

## 5. Forward vs Backward Migrations

- Forward migrations are required
- Backward (rollback) migrations are:
  - Provided when safe and practical
  - Documented when not possible

Rollback considerations:
- Data‑destructive changes may not be reversible
- Rollback strategy must be documented per migration

---

## 6. Environment Consistency

- All environments apply migrations in the same order
- Schema version is checked at application startup
- Application refuses to start if schema version is incompatible

---

## 7. Migration Safety Rules

- Migrations are idempotent where possible
- Long‑running migrations are avoided
- Data migrations are explicit and logged
- Schema and data migrations are separated when appropriate

---

## 8. Audit & Visibility

- Migration application is logged
- Applied migrations are recorded in the database
- Migration history is queryable

---

## 9. Design Guarantees

This strategy guarantees:
- No silent schema drift
- Reproducible database state
- Safe evolution over time
- Clear accountability for schema changes
