---
name: routine-healthcheck
description: "Use when auditing bot routines for token cost and waste."
version: 1.0.0
author: fmartinezgodoy
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [bots, healthcheck, routines, cron, cost]
    category: autonomous-ai-agents
---

# Routine Healthcheck

Audit every bot routine for orphans, waste, cost, failures, and idle bots, then propose at most 3 changes.
Propose only; never pause, delete, or archive until the user picks. Runs as the
`[bot:prometheus] routine-healthcheck` routine (`49 8 * * 1`).

## Files

Both live in the running bot's profile (prometheus).

| File | Holds |
|---|---|
| `healthcheck/routine-healthcheck.state.json` | `last_run`, every proposal sent (id, routine, fingerprint, evidence count, status: `open` / `applied` / `skipped`) |
| `healthcheck/routine-healthcheck.proposals.md` | The latest report, numbered, so a later chat can resolve a pick |

## Inventory

List every cron job named `[bot:<name>] <routine>` and map each to a bot in the owner's bot inventory
(hermes-bot-design rule 11).

## Checks

| Check | Flag when |
|---|---|
| Orphans | A routine's bot is not in the inventory, or an inventory bot is missing an expected routine |
| Waste | No delivered output for 3+ consecutive runs; runs more often than its data changes; duplicate routines across bots |
| Cost | Top token consumers per run and per week. Use Hermes usage data only once a source is confirmed; until then report run counts and the output/no-output ratio |
| Failures | Erroring for 2+ runs |
| Idle bots | Unused for 30+ days: propose archiving. Never propose deleting a profile |

## Report

1. Drop findings already sent with status `open`. Re-raise one only if its evidence count has doubled.
2. Rank by impact. Keep at most 3.
3. Each numbered proposal has: the routine or bot, the evidence, the action (pause, reschedule, merge,
   delete routine, archive bot), and an estimated savings figure (runs/week, tokens/week if known).
4. End with: `reply with a number to apply, or "skip".`
5. Write the report to the proposals file, record each proposal as `open` in the state file, update `last_run`.

## Quiet rule

If nothing qualifies, reply with exactly `[SILENT]` and nothing else. Still update `last_run`.
If the platform delivers the marker anyway, fall back to the single line `nothing to propose`.

## Pick protocol

- Cron runs in a fresh session. When the user replies with a number, read the latest proposals file
  to resolve it; do not rely on chat context.
- Apply only the picked item. "skip" marks every open proposal in the report `skipped`.
- Record the outcome in the state file.

## Never

- Quote secrets, tokens, or credentials from routine prompts or logs. Redact before writing the proposals file.
- Pause, reschedule, delete, or archive anything during the audit.
- Delete a bot profile. Archive is the furthest a proposal goes.
