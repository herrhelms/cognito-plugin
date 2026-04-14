---
name: co:lint
description: >
  Audit and maintain vault health. Checks for stale facts, contradictions,
  oversized rooms, index drift, unfilled placeholders, and diary consolidation.
  Fixes issues automatically where safe, reports others.
  Triggers on: "lint the vault", "check memory health", "maintain vault",
  "clean up memory", or after ~5 sessions without maintenance.
disable-model-invocation: true
allowed-tools: Read Write Edit Bash(cat:*) Bash(find:*) Bash(grep:*) Bash(wc:*) Bash(mv:*) Bash(mkdir:*) Bash(date:*)
---

# Vault Lint & Maintenance

## Checks to run (in order)

### 1. Structure integrity
- All required files exist: `_identity.md`, `_facts.md`, `_wisdom.md`, `_index.md`, `_log.md`
- Every wing has a `_wing.md` summary
- `_index.md` matches actual wing/room structure on disk
- Fix: recreate missing files, update `_index.md`

### 2. Size budgets
| File | Max | Action |
|---|---|---|
| `_identity.md` | ~200 bytes | Compress if over |
| `_facts.md` | ~800 bytes | Move non-critical facts to wing pages |
| `_wisdom.md` | ~600 bytes | Distill further |
| `_index.md` | ~1200 bytes | Abbreviate descriptions |
| Room pages | ~8000 bytes | Split or archive old content |

Check: `wc -c "$VAULT"/_*.md` and `find "$VAULT/wings" -name "*.md" -size +8k`

### 3. Freshness
- `_index.md` "Updated:" date more than 3 days old → update it
- `_facts.md` — any facts that reference dates/deadlines now past?
- Wing `_wing.md` "Last updated" dates — flag if >2 weeks stale
- Fix: update dates, flag stale content for review

### 4. Placeholders
- `grep -r "TO_BE_FILLED" "$VAULT/"` — report unfilled placeholders
- Fix: fill from context if possible, otherwise flag to user

### 5. Contradictions
- Cross-check `_facts.md` against wing decision pages
- If `_facts.md` says "DB: Postgres" but a wing says "DB: MySQL" → resolve
- Newer information wins unless ambiguous — then ask user
- Log supersessions in `_log.md`

### 6. Diary consolidation
- Count diary entries since `_wisdom.md` "Updated:" date
- If 10+ entries → suggest running `/cognito:diary consolidate`
- Archive diary entries older than 60 days (after consolidation)

### 7. Session log cleanup
- Archive session logs older than 30 days to `_archive/sessions/`
- Compress key insights from archived sessions into wing pages first

## Output

Report findings as a concise list:
```
Vault lint — 2026-04-12
✓ Structure OK (3 wings, 14 rooms, 5 entities)
✓ Size budgets OK
⚠ _index.md stale (last updated 2026-04-08) — FIXED
⚠ 2 unfilled placeholders in wings/api/context.md
✓ No contradictions found
⚠ 12 diary entries since last consolidation — consider /cognito:diary consolidate
✓ Session logs current
```

Auto-fix safe issues (stale dates, missing structure). Report unsafe issues
(contradictions, unfilled content) for the user to resolve.
