---
name: bot-design
description: "Use when designing a new bot. One job, one voice, anti-jobs."
version: 1.0.0
author: fmartinezgodoy
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [bots, personas, soul, profiles]
    category: autonomous-ai-agents
---

# Bot Design

The rubric a bot-builder agent follows when creating and maintaining Hermes Agent bots.
Read the routing table, load only the reference you need, then apply the hard rules.

## Routing

| Subtask | Load |
|---|---|
| Creating a new bot end to end | `references/creation-checklist.md` |
| Writing or reviewing a SOUL.md | `references/soul-guide.md` |
| Drafting a SOUL.md from scratch | `templates/soul-template.md` |
| Healthchecks, fixes, archiving a bot | "Maintenance" below |

## What a bot is

- A bot is a Hermes **profile**: `~/.hermes/profiles/<name>/` with its own `config.yaml`,
  `SOUL.md`, `skills/`, memory, and credentials. Isolation comes from the profile, not from prompts.
- Create one with `hermes profile create <name> --description "<one-sentence role>"`.
- Bot Mode (desktop app) is a UI over the same profiles: Name, Title, Description, optional
  model pin, custom SOUL.md, per-skill and per-MCP enablement. CLI and Bot Mode manage the same thing.
- Routines are cron jobs named `[bot:<name>] <routine>`. The prefix is how routines are traced back to their bot.

## Hard rules

1. **One job.** Each bot does exactly one thing ("watches PRs", not "helps with dev").
   WHY: a narrow job makes the SOUL short, the toolset small, and failures obvious.
2. **One voice.** Give the bot a distinct, short, consistent personality.
   WHY: users recognize and trust a bot by how it sounds; a drifting voice reads as a different bot.
3. **Explicit anti-jobs.** State what the bot does NOT do, in SOUL.md `# Avoid`
   (a scout does not post; a drafter does not send; stay quiet when there is nothing to add).
   WHY: models drift toward being helpful beyond scope; anti-jobs are the brake.
4. **No leftover tools.** Enable only the skills, toolsets, and MCP servers the job needs.
   WHY: every extra tool is extra tokens, extra attack surface, and an invitation to scope creep.
5. **SOUL.md is identity only.** Tone, personality, style, avoids, and how to handle
   uncertainty, disagreement, and ambiguity. Never paths, commands, ports, architecture, or workflow.
   WHY: SOUL.md loads everywhere; project facts belong in per-project `AGENTS.md`.
6. **Keep SOUL.md to 4–8 lines** under `# Identity / # Style / # Avoid / # Defaults`.
   WHY: it replaces the default identity entirely and every character costs tokens on every turn.
7. **Intake: 3–5 questions max** (name, one job, voice, model, channel), then build.
   WHY: bias to act. Over-interviewing costs more than a quick revision after a real test.
8. **User approves SOUL.md** before it is written, and before any later edit.
   WHY: the SOUL is the bot's identity; agent writes to it always require approval.
9. **Do not invent CLI commands.** Enable skills/toolsets/MCP and create routines through
   Bot Mode or the profile's `config.yaml`. The only verbatim CLI is `hermes profile create`.
   WHY: a made-up command fails silently or does something unintended.
10. **Baptism of fire before handover.** Give the bot one real task and verify the output.
    WHY: a bot that has never done its job is a draft, not a bot.
11. **Register every bot** in the owner's bot inventory (name, job, model, channel).
    WHY: unlisted bots become orphaned bots nobody maintains.

## Maintenance

- **Weekly healthcheck.** Run a weekly routine that reviews each bot's latest transcript for
  friction and verifies its `[bot:<name>]` routines still exist. Propose fixes; never apply
  them until the user picks one. WHY: the owner stays in control of every bot's behavior.
- **Deprecation.** If a bot is unused for 30+ days, propose archiving it. Never delete
  unilaterally. WHY: profiles hold memory and credentials the owner may still want.
- **SOUL.md edits** follow rule 8: draft the diff, get approval, then write.
