---
name: transcript-healthcheck
description: "Use when scanning bot chats for friction to propose fixes."
version: 1.0.0
author: fmartinezgodoy
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [bots, healthcheck, transcripts, friction, routines]
    category: autonomous-ai-agents
---

# Transcript Healthcheck

Scan the fleet's chats for repeated friction and propose at most 3 fixes. Propose only; never apply
until the user picks. Runs as the `[bot:prometheus] transcript-healthcheck` routine (`44 8 * * 1-5`).

## Files

Both live in the running bot's profile (prometheus), never in the scanned bots' profiles.

| File | Holds |
|---|---|
| `healthcheck/transcript-healthcheck.state.json` | `last_run`, every proposal sent (id, bot, fingerprint, evidence count, status: `open` / `applied` / `skipped`) |
| `healthcheck/transcript-healthcheck.proposals.md` | The latest report, numbered, so a later chat can resolve a pick |

## Scope

- Every bot in the owner's bot inventory (hermes-bot-design rule 11), sessions since `last_run`.
- Never read a profile that is not in the inventory. Never glob across profiles.
- Prefer asking each bot to self-report its friction (`message_agent`). Otherwise read its session
  files read-only. Confirm the session storage path before the first run; do not guess it.

## Signals

- User corrections: "no", "I said", "again", or the same ask rephrased.
- The same instruction restated across sessions.
- Anti-job violations: the bot did something its SOUL `# Avoid` forbids.
- Tool errors and retries.
- Abandoned threads: the user stopped replying mid-task.
- Replies that break the bot's voice.

## Evidence bar

A pattern counts only if it appears in **2+ sessions**. One-offs are not learnings.

## Classify

Tag each finding with one type: **SOUL edit**, **skill edit**, **enabled-tool change**, or **routine change**.
Prefer a structural fix (a rule, a disabled tool, a skill step) over adding more prose to the SOUL.

## Report

1. Drop findings already sent with status `open`. Re-raise one only if its evidence count has doubled.
2. Rank by impact. Keep at most 3.
3. Each numbered proposal has: the bot, evidence (session id plus a short quote), the proposed diff, its type.
4. End with: `reply with a number to apply, or "skip".`
5. Write the report to the proposals file, record each proposal as `open` in the state file, update `last_run`.

## Quiet rule

If nothing qualifies, reply with exactly `[SILENT]` and nothing else. Still update `last_run`.
If the platform delivers the marker anyway, fall back to the single line `nothing to propose`.

## Pick protocol

- Cron runs in a fresh session. When the user replies with a number, read the latest proposals file
  to resolve it; do not rely on chat context.
- Apply only the picked item. SOUL diffs also need explicit approval of the diff (hermes-bot-design rule 8).
- "skip" marks every open proposal in the report `skipped`.
- Record the outcome in the state file.

## Never

- Quote secrets, tokens, or credentials. Redact before writing the proposals file.
- Dump raw transcripts. A short quote plus the session id is the evidence.
- Edit any SOUL, skill, config, or routine during the scan.
