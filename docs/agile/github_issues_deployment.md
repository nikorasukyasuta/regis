# GitHub Issues & Epics Structure  
**Architecture v1 — Centralized SQLite with Hub Sync**

---

## Epic 1 — Architecture & Foundations
- Define environment configuration
- Establish logging and error conventions
- Enforce API-only database access
- Enable SQLite WAL mode and integrity checks

---

## Epic 2 — Database & Schema
- Add ownership fields (league/session/registration)
- Add revision markers to mutable tables
- Create append-only payment ledger
- Create audit tables
- Create staging tables (email + Excel)

---

## Epic 3 — Event Journal
- Implement local append-only event journal
- Generate globally unique event IDs
- Enforce idempotency
- Ensure journal writes are transactional

---

## Epic 4 — Replication Agent (Office Nodes)
- Push event batches to hub
- Pull unseen events from hub
- Replay events deterministically
- Handle retries and backoff
- Track delivery cursors

---

## Epic 5 — Sync Hub
- Implement durable event store
- Implement push/pull APIs
- Track per-node delivery state
- Expose monitoring