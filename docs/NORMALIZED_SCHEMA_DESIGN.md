# Normalized Schema Design

## 1. Core Tables

### 1.1 customers
Represents a single person or organization.

Fields (conceptual):
- id
- name fields
- primary contact info
- soft delete fields
- audit fields

Relationships:
- One customer → many registrations
- One customer → many communication records
- One customer → many addresses (via join table)

---

### 1.2 addresses
Represents a normalized physical address.

Fields:
- id
- street, city, state, postal code
- country
- soft delete fields
- audit fields

Relationships:
- Addresses are reusable
- Linked to customers via customer_addresses

---

### 1.3 customer_addresses
Join table linking customers to addresses.

Fields:
- customer_id
- address_id
- address_type (billing, mailing, etc.)
- audit fields

---

### 1.4 events
Represents a league, season, or session.

Fields:
- id
- name
- start/end dates
- status
- soft delete fields
- audit fields

Relationships:
- One event → many registrations

---

### 1.5 registrations
Represents a single participation instance.

Fields:
- id
- customer_id
- event_id
- registration_type (individual/team)
- status_id
- soft delete fields
- audit fields
- version field (for concurrency)

Relationships:
- Belongs to one customer
- Belongs to one event
- Has many registration_notes
- Has many communication records

---

### 1.6 registration_notes
Append‑only notes related to a registration.

Fields:
- id
- registration_id
- note_text
- created_at
- created_by

Rules:
- Notes are never updated or deleted

---

### 1.7 communication_records
Represents a single interaction.

Fields:
- id
- customer_id (nullable)
- registration_id (nullable)
- communication_type (email, call, note)
- source (UI, Excel, Email)
- content reference
- created_at
- created_by

Rules:
- Append‑only
- Never updated or deleted

---

## 2. Status & Lookup Tables

### 2.1 registration_statuses
Controlled lifecycle states.

Examples:
- pending
- confirmed
- cancelled

Rules:
- Read‑only
- Referenced by registrations

---

### 2.2 lookup tables
Examples:
- tshirt_sizes
- competition_levels
- sports

Rules:
- Read‑only
- No soft delete unless explicitly required

---

## 3. Explicit Non‑Tables

The schema does NOT include tables for:
- Excel files
- Raw emails as authoritative records
- Derived or computed views
- UI state

---

## 4. Ownership & Cardinality Summary

- customers → registrations (1‑to‑many)
- events → registrations (1‑to‑many)
- registrations → notes (1‑to‑many)
- customers ↔ addresses (many‑to‑many)
- registrations → communication_records (1‑to‑many)

---

## 5. Normalization Guarantees

The schema guarantees:
- No duplicated customer data across tables
- No embedded address fields in customers
- No status fields duplicated across entities
- No history overwritten

---

## 6. Deletion & History Rules

- All core tables use soft delete
- History tables are append‑only
- Soft‑deleted records remain referencable
