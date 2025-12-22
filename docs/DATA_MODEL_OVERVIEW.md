# Data Model Overview

## 1. Core Domain Entities

The system includes the following core entities:

### 1.1 Customer
Represents an individual or organization participating in registrations.

Responsibilities:
- Identity and contact information
- Relationship to registrations

Does NOT:
- Store registration state
- Store communication history

---

### 1.2 Registration
Represents a single participation instance.

Types:
- Individual registration
- Team registration

Responsibilities:
- Links customer(s) to events/sessions
- Tracks status and lifecycle

---

### 1.3 Event / Session
Represents a scheduled activity (league, season, tournament, etc.).

Responsibilities:
- Defines time‑bounded participation
- Owns registration eligibility

---

### 1.4 Address
Represents a normalized physical address.

Responsibilities:
- Reusable across customers
- No business logic

---

### 1.5 Communication Record
Represents a single interaction (email, note, call).

Responsibilities:
- Append‑only history
- Audit‑friendly

---

## 2. Supporting Entities

### 2.1 Status
Represents controlled state transitions.

Examples:
- Registration status
- Contact status

---

### 2.2 Lookup Tables
Represents fixed reference data.

Examples:
- T‑shirt sizes
- Competition levels
- Sports types

---

## 3. Explicit Non‑Entities

The system does NOT model:

- Excel files as entities
- Emails as authoritative records
- Derived or computed views as tables
- UI‑specific state

---

## 4. Ownership & Boundaries

- Customers own registrations
- Registrations reference events
- Communication records reference customers or registrations
- Lookup tables are read‑only

---

## 5. Deletion Policy

- All entities use **soft delete only**
- No cascading hard deletes
- Historical references remain valid
