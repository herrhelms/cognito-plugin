---
name: status
description: >
  Show vault status, statistics, and health overview. Use when the user
  asks about memory state, what Claude knows, or vault health.
  Triggers on: "vault status", "what do you know", "memory status",
  "show me your memory", "how's the vault".
disable-model-invocation: true
allowed-tools: Bash(cat:*) Bash(find:*) Bash(wc:*) Bash(grep:*) Bash(du:*) Bash(ls:*)
---

# Vault Status Report

Run `cognito-ops status` and present the results to the user.

Additionally, report:

1. **Vault location** and storage mode (local vs global)
2. **Boot layer sizes** — are `_facts.md` and `_wisdom.md` within budget?
3. **Wing summary** — list each wing with room count and last update
4. **Diary status** — how many entries, last consolidation date
5. **Health issues** — run `cognito-ops lint` and report problems
6. **Recent activity** — last 5 `_log.md` entries

Format as a concise report. Don't dump raw file contents — synthesize.
