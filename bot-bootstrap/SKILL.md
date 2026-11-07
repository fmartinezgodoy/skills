---
name: bot-bootstrap
description: "Use on a bot's first run to install and prove its routines."
version: 1.0.0
author: fmartinezgodoy
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [bots, bootstrap, routines, skills, smoke-test]
    category: autonomous-ai-agents
---

# Bot Bootstrap

Install a bot's skills, prove its routines exist, and smoke-test it headless. Do not assume anything exists
because a skill is installed: skill installs never create cron jobs.

## When

- prometheus' own first run.
- The end of every new-bot creation, after the baptism of fire (hermes-bot-design rule 10).

## Verified commands

Use only these (hermes-bot-design rule 9). Anything else goes through Bot Mode or the profile's `config.yaml`.

```bash
hermes profile create <name> --description "<one-sentence role>"
hermes -p <name> skills install fmartinezgodoy/skills/<skill> -y
hermes -p <name> chat -q "<one real task>" --oneshot
```

Routines are created with the agent's cron tool, named `[bot:<name>] <routine>`.

## Steps

1. **Install skills.** For each skill in the bot's list, run
   `hermes -p <name> skills install fmartinezgodoy/skills/<skill> -y`. Report each result.
2. **Prove routines.** List every `[bot:<name>]` routine. Compare against the bot's expected routines.
3. **Create missing routines.** For each missing one, show the exact schedule and prompt and wait for a yes.
   If no cron tool is available, print the schedule and prompt for the user to add in Bot Mode or
   `config.yaml`, then list again to prove it exists.
4. **Verification skill (coding bots only).** If the bot works on a real repo and no `verify-*` skill exists,
   offer `create-verification-skill` once. Skip on an empty machine. On no, move on.
5. **Fleet checkup (prometheus only).** Offer once to run `routine-healthcheck` now. On no, move on.
6. **Smoke test.** Run `hermes -p <name> chat -q "<one real task>" --oneshot`. Check the output against
   the bot's SOUL `# Avoid` anti-jobs. A violation fails the bootstrap.
7. **Record.** Write the result to the bot's inventory row: routines present, verify skill present, smoke test pass/fail.

## prometheus' expected routines

| Routine | Schedule | Skill |
|---|---|---|
| `[bot:prometheus] transcript-healthcheck` | `44 8 * * 1-5` | `transcript-healthcheck` |
| `[bot:prometheus] routine-healthcheck` | `49 8 * * 1` | `routine-healthcheck` |

Times are PT; convert to the owner's time zone and confirm which zone Hermes cron uses.
Each routine prompt is a one-line pointer to its same-name skill.

## Never

- Create a routine without a yes.
- Mark a routine present without listing it.
- Accept, print, or log a secret. Credentials go in the profile's credential file, edited by the user.
