# System Scope & Non‑Goals

## 1. System Purpose

This system exists to:

- Replace Excel‑based workflows for registrations, contacts, and status tracking
- Provide a normalized, auditable SQLite‑backed data store
- Support safe multi‑user access over time
- Reduce manual data entry through email ingestion
- Preserve a clear audit trail for all changes

The system prioritizes **data integrity, traceability, and correctness** over speed or convenience.

---

## 2. In‑Scope Capabilities

The system WILL:

- Use SQLite as the authoritative datastore
- Support CRUD operations through a backend service layer
- Provide a modern UI for daily operations
- Allow Excel import/export during a transition period
- Ingest structured data from supported email formats
- Enforce validation and normalization rules
- Record immutable audit history for all changes
- Support future centralization and multi‑user workflows

---

## 3. Explicit Non‑Goals

The system WILL NOT:

- Treat Excel as a source of truth
- Allow direct editing of SQLite files
- Support real‑time collaborative editing
- Automatically resolve conflicting edits silently
- Attempt to parse arbitrary or unstructured emails
- Optimize for massive scale or cloud deployment
- Replace accounting or payment processing systems
- Serve as a general CRM beyond defined use cases

---

## 4. User & Usage Assumptions

- Initial usage is local or small‑team
- Write contention is expected but manageable
- Users value correctness over speed
- Excel usage will decrease over time
- Email ingestion will require validation and review

---

## 5. Design Biases

When tradeoffs arise, the system favors:

- Explicit over implicit behavior
- Validation over permissiveness
- Auditability over convenience
- Manual review over silent automation
- Predictable failure over hidden errors

---

## 6. Deferred Decisions (Intentionally Out of Scope)

The following are intentionally deferred:

- Cloud hosting strategy
- Mobile‑first UI
- Third‑party integrations beyond email
- Advanced analytics or dashboards
- Multi‑tenant support

These may be revisited later but are not part of the current scope.
