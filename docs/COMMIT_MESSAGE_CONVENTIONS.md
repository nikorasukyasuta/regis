
---

# ✅ Commit Message Conventions  
**Solo Dev · Audit‑First · SQLite‑Centric System**

---

## 🎯 Core Principles

1. **Every commit answers “why,” not just “what”**
2. **Small commits > perfect commits**
3. **Schema and data changes are never ambiguous**
4. **Future‑you is the primary audience**

---

## 🧱 Commit Message Structure

```
<type>(<scope>): <short summary>

<optional body>

<optional footer>
```

---

## 🏷️ Commit Types (Required)

Use **one** of the following prefixes:

| Type | Purpose |
|----|--------|
| `arch` | Architecture or design decisions |
| `schema` | SQLite schema or migration changes |
| `data` | Data fixes, migrations, corrections |
| `feat` | New user‑visible functionality |
| `fix` | Bug fixes |
| `audit` | Audit logging or traceability |
| `excel` | Excel import/export or transition |
| `email` | Email ingestion or parsing |
| `ui` | Frontend UI/UX changes |
| `security` | Auth, permissions, secrets |
| `test` | Tests only |
| `docs` | Documentation only |
| `chore` | Tooling, cleanup, refactors |

---

## 📦 Scope (Strongly Recommended)

Scope should be **specific and concrete**:

Examples:
- `schema(registration)`
- `excel(import)`
- `email(parser)`
- `audit(logging)`
- `ui(conflict-resolution)`

---

## ✍️ Short Summary Rules

- Present tense
- Imperative mood
- ≤ 72 characters
- No punctuation at the end

✅ Good:
```
schema(registration): add audit fields and foreign keys
```

❌ Bad:
```
Updated registration table
```

---

## 📝 Commit Body (Optional but Encouraged)

Use the body when:
- Changing schema
- Touching data
- Introducing risk
- Making a non‑obvious decision

Format:
```
Why:
- Reason for change

Notes:
- Tradeoffs
- Follow‑ups
```

---

## 🧾 Footer (Optional)

Use footers for:
- Breaking changes
- Migration notes
- Risk flags

Examples:
```
BREAKING CHANGE: requires schema migration v3
```

```
RISK: affects Excel round‑trip behavior
```

---

## ✅ Examples (Use These)

### Architecture Decision
```
arch(core): define SQLite as system of record

Why:
- Prevent ambiguity during Excel transition
- Simplify audit guarantees
```

---

### Schema Change
```
schema(registration): add created_by and updated_by fields

Why:
- Required for audit completeness
- Enables user attribution

BREAKING CHANGE: migration required
```

---

### Excel Import
```
excel(import): validate required columns before staging

Why:
- Prevent silent partial imports
- Surface user errors early
```

---

### Email Parsing
```
email(parser): handle missing subject lines safely

Why:
- Some providers omit subject
- Prevent ingestion failure
```

---

### Audit Logging
```
audit(logging): capture before/after state on updates

Why:
- Required for traceability
- Enables rollback analysis
```

---

### UI Change
```
ui(conflict): display ownership warning on edit

Why:
- Prevent accidental overwrites
- Make conflicts explicit
```

---

### Test‑Only Commit
```
test(concurrency): add SQLite write contention tests
```

---

### Documentation
```
docs(architecture): add concurrency assumptions section
```

---

## 🚨 Special Rules (Non‑Negotiable)

### Schema Commits
- Must include:
  - Migration
  - Clear commit body
- Never mix schema + unrelated changes

### Data Commits
- Must explain:
  - Why data changed
  - How it was validated
- Never squash without review

### High‑Risk Areas
Add `RISK:` footer when touching:
- Excel imports
- Email parsing
- Concurrency logic
- Audit hooks

---

## 🧠 Solo Dev Best Practices

- Commit **before** switching context
- Commit **before** refactoring
- Commit **before** “just one more thing”
- If unsure, commit — you can squash later

---

## ✅ Definition of a Good Commit

A good commit:
- Can be reverted safely
- Explains intent
- Leaves breadcrumbs
- Does not surprise future‑you

---
