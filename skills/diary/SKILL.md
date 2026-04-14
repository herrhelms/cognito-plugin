---
name: co:diary
description: >
  Claude's personal diary for process observations, working patterns, and
  meta-insights that don't fit in any wing room. Use proactively when you
  notice something about how the user works, a recurring pattern, a process
  insight, or a self-improvement note. Also use to consolidate diary entries
  into the _wisdom.md long-term memory layer.
  Triggers on: end of meaningful sessions, noticing user behavior patterns,
  "write a diary entry", recurring themes, or when explicitly asked to
  reflect. Also triggers for wisdom consolidation: "consolidate wisdom",
  "update patterns", or periodically (~weekly).
argument-hint: [entry text or "consolidate"]
allowed-tools: Read Write Edit Bash(cat:*) Bash(date:*) Bash(ls:*) Bash(grep:*) Bash(wc:*) Bash(mkdir:*)
---

# Diary System

The diary is YOUR space for meta-observations. Not project facts (those go in
wings), not session summaries (those go in sessions/) — this is where you
develop *wisdom* about how to work effectively.

## Writing a diary entry

File: `$VAULT/dailies/diary-YYYY-MM-DD.md`

If the file exists, append. If not, create with header.

### Format

```markdown
# Diary: 2026-04-12

## 14:30 — User interaction pattern
User changed auth approach for the third time. Not indecisiveness — they're
exploring the solution space. Next time: present 2-3 options with tradeoffs
upfront instead of committing to one approach.
→ pattern: exploratory-decision-making

## 16:15 — Codebase observation
Error handling is inconsistent across API routes. Some throw, some return
error objects, some swallow silently. This will bite us during the auth
migration. Flag proactively next session.
→ flag: error-handling-inconsistency

## 17:00 — Process note
The user works best in short focused bursts. Long sessions with many topics
lead to context-switching fatigue. Try to resolve one thing fully before
moving to the next.
→ pattern: focused-bursts
```

### What to write about

| Category | Example | Tag |
|---|---|---|
| User working patterns | "Prefers concise code, dislikes abstraction layers" | `→ pattern:` |
| Recurring issues | "Third race condition in webhook handlers this month" | `→ flag:` |
| Communication insights | "Gets frustrated with too many clarifying questions" | `→ pattern:` |
| Codebase smells | "Test coverage gaps in billing module" | `→ flag:` |
| Self-improvement | "My initial architecture suggestions were too complex" | `→ improve:` |
| Relationship notes | "User trusts my judgement on backend, wants more input on frontend" | `→ pattern:` |
| Predictive observations | "This sprint is behind — deadline pressure incoming" | `→ flag:` |

### Rules

- **Be genuine** — write what you actually observe, not what sounds impressive
- **Be specific** — "user prefers X" not "user has preferences"
- **Include actionable takeaways** — what should you DO differently?
- **Tag entries** with `→ pattern:`, `→ flag:`, or `→ improve:` for consolidation
- **Keep entries short** — 2-5 lines each, timestamp + observation + takeaway
- **Write proactively** — don't wait to be asked, notice and note

## Consolidating into _wisdom.md

When `$ARGUMENTS` is "consolidate" or when you have 10+ diary entries
since last consolidation, distill patterns into `_wisdom.md`.

### Process

1. Read all diary entries since last consolidation:
   ```bash
   ls -1 "$VAULT/dailies/" | sort -r | head -14
   ```

2. Find recurring patterns (tagged with `→ pattern:`, `→ flag:`, `→ improve:`)

3. Entries that appear 2+ times across different days → graduate to `_wisdom.md`

4. Write `_wisdom.md` in this compressed format:

```markdown
# Wisdom
Updated: 2026-04-12

## Working patterns
- **Decision style**: Exploratory — present options with tradeoffs, not single recommendations
- **Session rhythm**: Short focused bursts, one topic at a time, avoid multi-topic sessions
- **Communication**: Concise, minimal questions, make assumptions and state them

## Codebase patterns
- **Error handling**: Inconsistent across API routes — flag when touching related code
- **Webhooks**: Race condition prone — always add retry/idempotency checks
- **Tests**: Billing module has coverage gaps — prioritize when touching billing

## Self-corrections
- **Architecture**: Start simpler than instinct suggests — user scales up, not down
- **Explanations**: Lead with the "what", not the "why" — user asks why if curious
```

### Rules for _wisdom.md

- **Max 150 tokens** — this loads every session, keep it ruthlessly tight
- **Only graduated patterns** — if you only saw it once, it stays in the diary
- **Actionable** — every line should change how you behave
- **Supersede, don't append** — rewrite when patterns evolve
- **Date it** — so you know when wisdom was last refreshed

## Retention

- Diary entries older than 60 days: archive to `_archive/dailies/`
- But only AFTER consolidating their patterns into `_wisdom.md`
- Never delete `_wisdom.md` — it only grows (but stays compressed)
