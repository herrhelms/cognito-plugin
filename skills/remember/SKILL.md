---
name: remember
description: >
  Persist knowledge to the memory vault. Use when Claude learns something
  worth keeping: decisions, architecture changes, task updates, problems,
  discoveries, user preferences, or anything the user says to remember.
  Triggers on: "remember this", "save this", "note that", "don't forget",
  "log this decision", or when Claude recognizes important information
  that should survive across sessions.
argument-hint: <what to remember>
allowed-tools: Read Write Edit Bash(cat:*) Bash(grep:*) Bash(mkdir:*) Bash(date:*)
---

# Remember Protocol

When you learn something worth persisting, follow this flow:

## 1. Classify the memory

| Type | Target room | Example |
|---|---|---|
| Decision + rationale | `decisions.md` | "We chose Redis because..." |
| Architecture/design | `architecture.md` | "The API uses layered middleware..." |
| Task status change | `tasks.md` | "Auth migration is complete" |
| Problem or pitfall | `problems.md` | "Webhooks fire before DB write..." |
| Breakthrough/insight | `discoveries.md` | "Prisma doesn't support interactive tx..." |
| Background/goals | `context.md` | "Budget is $500/mo for infra" |
| Person/tool/concept | `entities/<name>.md` | "Alice is the tech lead" |
| Process observation | `dailies/diary-*.md` | "User prefers options over commitments" |

## 2. Route to the right wing

Determine which wing this belongs to. If unsure, check `_index.md`.
If no wing exists for this domain yet, create one (see Section 5).

## 3. Update the room page IN-PLACE

**This is critical: COMPILE, don't APPEND.**

Read the current room page. Find the relevant section. REWRITE that section
to incorporate the new information. The page should always reflect current
truth with brief historical notes on what changed.

**Good**: Change `**DB**: MySQL` to `**DB**: Postgres (migrated from MySQL, 2026-04-12, reason: JSONB)`
**Bad**: Append "Update: we switched to Postgres" at the bottom

Use this compressed format:
```markdown
- **Entity**: Choice | over Alternative | reason: why | YYYY-MM-DD | by: who
```

## 4. Update boot layers if needed

- **`_facts.md`**: Update if this is a critical fact (team change, stack change,
  blocker, deadline). Keep under 200 tokens — be ruthless.
- **`_index.md`**: Update if you created a new wing or room.
  Format: `**wing** (N rooms): description | room1 room2 room3`

## 5. Create new structure if needed

New wing:
```bash
mkdir -p "$VAULT/wings/<wing-name>"
# Create _wing.md with overview + room map
# Create the first room page
# Update _index.md
```

New room: just create the `.md` file in the wing directory and update
the wing's `_wing.md` room map table.

## 6. Log the action

Append one line to `_log.md`:
```
YYYY-MM-DD | ACTION | target | detail
```
Actions: CREATE, UPDATE, SUPERSEDE, DECIDE, ARCHIVE

## 7. Keep it tight

- Room pages: <2000 tokens. Split if larger.
- Use `|` separated metadata, not prose
- Bold the entity name in lists
- Always include dates
- Reference file paths, don't copy code
