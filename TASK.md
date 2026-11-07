# TASK: Create the `hermes-bot-design` skill (the bot-builder rubric)

You are building the first skill of a public agent-skills tap (`fmartinezgodoy/skills`).
This skill is the design rubric that a "bot builder" agent (an agent whose job is to
create and maintain other bots) must follow when designing Hermes Agent bots.
It will also be read by humans. Write everything in **English** (public artifact).

## Skill format (must follow exactly)

- Directory: `hermes-bot-design/SKILL.md` plus optional `references/` and `templates/` subdirs.
- `SKILL.md` starts with YAML frontmatter:

```yaml
---
name: hermes-bot-design
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
```

- description: max 60 chars after "Use when", action-oriented, not the category name.
- Body style: routing-first (a table mapping subtask → reference file), imperative
  rules with WHY, no logs/PR-narrative, one rule per lesson. Keep SKILL.md under
  ~120 lines; push long detail into `references/`.

## Content requirements (the rubric itself — sourced from official Hermes docs)

### 1. A bot is a profile
- Bots live at `~/.hermes/profiles/<name>/` (own config.yaml, SOUL.md, skills/, memory, credentials).
- Create via CLI: `hermes profile create <name> --description "<one-sentence role>"`.
- Bot Mode (desktop app) is a UI over profiles: Name/Title/Description + optional
  model pin, custom SOUL.md, per-skill/MCP enablement. Both paths manage the same thing.
- Routines (scheduled jobs) are cron jobs named `[bot:<name>] <routine>`.

### 2. SOUL.md rules (from the official "Use SOUL.md with Hermes" guide)
- SOUL.md = identity ONLY: tone, personality, communication style, stylistic avoids,
  how the bot handles uncertainty/disagreement/ambiguity.
- NEVER: repo conventions, file paths, commands, ports, architecture, project workflow (those go in AGENTS.md per-project).
- 4–8 lines is enough. Structure: `# Identity / # Style / # Avoid / # Defaults`.
- Strong: stable, broadly applicable, specific in voice, not overloaded.
- Weak: project details, contradictions, micro-managing response shapes, generic filler ("be helpful").
- SOUL.md replaces the default identity entirely; every character counts against the token budget.

### 3. Design bar (from the dr-eggbot pattern, adapted)
- ONE JOB: every bot does exactly one thing (e.g., "watches PRs", NOT "helps with dev").
- ONE VOICE: distinct, short, consistent personality.
- EXPLICIT ANTI-JOBS: state what the bot does NOT do (a scout does not post; a
  drafter does not send; stays quiet when nothing to add). Anti-jobs go in SOUL.md "Avoid".
- NO LEFTOVER TOOLS: enable only the skills/toolsets/MCP the job needs — nothing more.
- BIAS TO ACT: once the job is clear after a few intake questions, build; don't over-interview.
- INTAKE: ask max 3-5 preference questions before creating (name, one job, voice, model, channels).

### 4. Creation checklist (make this a numbered checklist in a reference file)
1. Confirm: name, one-sentence job, voice/tone, anti-jobs, model (inherit default or pin), delivery channel (Telegram/desktop chat).
2. Draft SOUL.md (4–8 lines, Identity/Style/Avoid/Defaults) — have the user approve it.
3. `hermes profile create <name> --description "<role>"`.
4. Write the approved SOUL.md into the profile.
5. Enable the minimum skill/toolset set for the job.
6. Only add routines if a recurring job exists (name them `[bot:<name>] ...`).
7. Baptism of fire: give the bot one real task; verify output before handing over.
8. Register the bot in the owner's bot inventory (name/job/model/channel).

### 5. Maintenance (the "maintain" half)
- Healthcheck pattern: a weekly cron that reviews each bot's latest transcript for
  friction + verifies routines still exist; propose fixes, never apply without user pick.
- Deprecation: if a bot is unused 30+ days, propose archive (don't delete unilaterally).
- SOUL.md edits need user approval (agent writes to SOUL.md always require approval).

## Deliverables

1. `hermes-bot-design/SKILL.md` — frontmatter + routing + the hard rules, tight.
2. `hermes-bot-design/references/creation-checklist.md` — the full checklist + CLI commands.
3. `hermes-bot-design/references/soul-guide.md` — distilled official SOUL.md guidance + 3 example SOULs (pragmatic engineer, mentions scout with anti-jobs, daily briefer).
4. `hermes-bot-design/templates/soul-template.md` — fill-in template with comments.
5. Update root `README.md`: one table row for the skill (name + description).
6. `git add` + commit (message: `feat: hermes-bot-design skill — rubric for building Hermes bots`). Do NOT push.

## Constraints
- Do not create any other skill. Do not touch files outside this repo.
- No secrets, tokens, or personal server paths anywhere (public repo).
- For enabling skills/toolsets and creating routines, do NOT invent CLI commands:
  reference Bot Mode (desktop) and the profile's `config.yaml` instead — this is
  the approved approach. The only CLI commands you may use verbatim are
  `hermes profile create <name> --description "<role>"` and git commands.
