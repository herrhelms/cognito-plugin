---
name: co:recall
description: >
  Search and retrieve knowledge from the memory vault. Use when Claude
  needs to find past decisions, context, architecture details, or any
  stored knowledge. Triggers on: "do you remember", "what did we decide",
  "what's the context", "what was the reason", any reference to past
  work or decisions, or when Claude senses it's missing information
  that should exist in the vault.
argument-hint: <what to find>
allowed-tools: Read Bash(cat:*) Bash(grep:*) Bash(find:*)
---

# Recall Protocol

## Search cascade — go in order, stop when found:

### Level 1: Boot layers (already loaded)
Check `_facts.md` and `_wisdom.md` — these are in your context from wake-up.
Many common questions are answered here instantly at zero additional cost.

### Level 2: Index-guided lookup
Read `_index.md` to identify the relevant wing and room.
Then read that specific room page. This is the most common path.

```
Query: "what did we decide about auth?"
→ _index.md shows wing "api" has room "decisions"
→ Read wings/api/decisions.md
→ Find the auth entry
```

### Level 3: Wing summary scan
If you're not sure which room, read the wing's `_wing.md` summary.
The room map table tells you which room covers what topic.

### Level 4: Cross-wing search
If it could be in multiple wings, use grep:
```bash
grep -ril "search term" "$VAULT/wings/" --include="*.md" | head -10
```

### Level 5: Entity check
If it's about a person, tool, or concept:
```bash
ls "$VAULT/entities/"
# Then read the relevant entity page
```

### Level 6: Full vault search
Last resort — broad search across everything:
```bash
grep -rin "search term" "$VAULT/" --include="*.md" | head -20
```

### Level 7: Diary search
Check diary entries for process observations:
```bash
grep -ril "search term" "$VAULT/dailies/" --include="*.md" | head -5
```

## If not found

Be honest: "I don't have that in my memory vault. Would you like to
tell me so I can remember it for next time?"

Then use `/cognito:remember` to store what they tell you.

## Response format

When recalling, cite your source naturally:
"According to my notes from April 10th, we decided on Redis for caching
because of persistence across deploys and shared state across instances."

Don't say "checking my memory vault" — just answer naturally as if you
remember, because you do.
