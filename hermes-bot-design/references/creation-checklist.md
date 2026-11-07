# Creation Checklist

Follow these steps in order. Do not skip a step; do not reorder 2 and 4.

## Intake (keep it to 3–5 questions)

Ask only what you cannot reasonably infer. Typical questions:

- What should the bot be called?
- What is its one job, in one sentence?
- How should it sound?
- Which model: inherit the default, or pin one?
- Where does it deliver: Telegram or desktop chat?

If the user's request already answers some of these, do not ask again. Once the job is
clear, build — a quick revision after a real test beats a long interview.

## Checklist

1. **Confirm the spec.** Restate and get a yes on:
   - name
   - one-sentence job
   - voice / tone
   - anti-jobs (what it does NOT do)
   - model (inherit default or pin)
   - delivery channel (Telegram / desktop chat)

   If the job needs the word "and" to describe it, it is probably two bots.

2. **Draft SOUL.md** (4–8 lines, `# Identity / # Style / # Avoid / # Defaults`) using
   `templates/soul-template.md` and `references/soul-guide.md`. Show it to the user and
   get explicit approval before writing anything.

3. **Create the profile.**

   ```bash
   hermes profile create <name> --description "<one-sentence role>"
   ```

   This creates `~/.hermes/profiles/<name>/` with its own `config.yaml`, `SOUL.md`,
   `skills/`, memory, and credentials. Alternatively, create the bot in Bot Mode
   (desktop app): Name, Title, Description — it manages the same profile.

4. **Write the approved SOUL.md** into the profile (`~/.hermes/profiles/<name>/SOUL.md`),
   or paste it into the custom SOUL.md field in Bot Mode. Write exactly what was approved.

5. **Enable the minimum skill/toolset set.** Use Bot Mode's per-skill and per-MCP toggles,
   or edit the profile's `config.yaml`. Start from nothing and add only what the one job
   requires. Pin the model here if step 1 asked for it. Do not invent CLI commands for this.
   Also DISABLE the toolsets the job does not use — set `agent.disabled_toolsets` in the
   profile's `config.yaml` (reference trim: prometheus runs on web, terminal, file, skills,
   todo, memory, session_search, clarify, cronjob only). Every enabled toolset is noise,
   tokens, and attack surface for every future turn of that bot.

6. **Add routines only if a recurring job exists.** Name each one
   `[bot:<name>] <routine>` (e.g. `[bot:pr-scout] morning sweep`). Configure it through
   Bot Mode or the profile's `config.yaml`. No recurring job, no routine.

7. **Baptism of fire.** Give the bot one real task from its job. Read the output yourself:
   - Did it stay in its one job?
   - Did it respect every anti-job?
   - Does it sound like the approved voice?
   - Did it use only the tools it was given?

   Fix and re-run until all four are yes. Only then hand it over.

8. **Register the bot** in the owner's bot inventory:

   | Name | Job | Model | Channel |
   |---|---|---|---|
   | `<name>` | `<one-sentence job>` | `<default or pinned model>` | `<Telegram / desktop>` |

## Done means

- Profile exists, SOUL.md matches what the user approved.
- Only the needed skills/toolsets/MCP are enabled.
- Routines (if any) carry the `[bot:<name>]` prefix.
- One real task completed and verified.
- Bot appears in the inventory.
