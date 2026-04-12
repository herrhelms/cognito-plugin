---
name: wake
description: >
  Load persistent memory context at session start. This skill auto-triggers
  when a session begins or when Claude needs project context. It reads the
  vault's L0-L2 layers (identity, facts, wisdom, index) in ~700 tokens,
  giving Claude full project awareness without re-explanation.
  Triggers on: session start, "load context", "wake up", "what do you know
  about this project", any sign Claude is missing context it should have.
allowed-tools: Read Bash(cat:*) Bash(grep:*) Bash(find:*) Bash(ls:*) Bash(mkdir:*)
---

# Vault Wake-Up Protocol

## Step 1: Locate the vault

```bash
# Check local vault first
if [ -d ".claude/knowledge/cognito" ]; then
  VAULT=".claude/knowledge/cognito"
fi

# Then check global
if [ -z "$VAULT" ]; then
  FOLDER_HASH=$(echo -n "$PWD" | sha256sum | head -c 8)
  GLOBAL="$HOME/.claude/knowledge/cognito/$(basename $PWD)-${FOLDER_HASH}"
  [ -d "$GLOBAL" ] && VAULT="$GLOBAL"
fi
```

## Step 2: If no vault exists → bootstrap

Run `cognito-bootstrap` (from plugin bin/) or `cognito-bootstrap --local`
if `useLocalMemory` is desired. Then ask the user:
- "What's this project about?" → fill `_identity.md` + first wing
- "Who's on the team?" → create entity pages
- "Key decisions already made?" → populate `decisions.md`

## Step 3: Read boot layers (~700 tokens)

Read these files in order:
1. `$VAULT/_identity.md` — L0: who you are here (<50 tokens)
2. `$VAULT/_facts.md` — L1: critical project facts (<200 tokens)
3. `$VAULT/_wisdom.md` — L1.5: working patterns & process insights (<150 tokens)
4. `$VAULT/_index.md` — L2: vault map, wing list, room list (<300 tokens)

## Step 4: Route to relevant wing

Based on the user's first message, identify which wing(s) are relevant.
Read only the `_wing.md` summary of those wings — NOT the full room pages yet.
Read specific room pages only when the conversation touches that topic.

## Step 5: Check diary

Glance at today's diary entry if it exists (`$VAULT/dailies/diary-YYYY-MM-DD.md`)
to pick up any earlier observations from today.

## Key rules

- NEVER skip wake-up — the ~700 token cost pays for itself instantly
- Read rooms ON DEMAND, not all at once
- If `_facts.md` has `<TO_BE_FILLED>` placeholders, interview the user to fill them
- If any boot file is missing, recreate it from what you know
