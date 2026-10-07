---
name: skill-setup
description: Installs the bundled journey-sync skill on a colleague's machine and installs other skills from the open skills ecosystem (npx skills) on request, with checks before and after. Use when the user says "install journey-sync", "set up the skills", "I received this skill from a colleague", "install a skill", "find and install a skill for X", "update journey-sync", or wants to hand the skill kit to someone else.
---

# skill-setup

Two jobs: (A) install the bundled **journey-sync** skill, (B) install other skills on demand. Also (C) refresh the bundle for the person who maintains it.

Bundled content lives in `assets/journey-sync/` (next to this file). Target dir: `~/.claude/skills/` (Windows: `%USERPROFILE%\.claude\skills\`).

## Rules

- Never overwrite an existing skill folder without showing what exists and getting a yes. Back it up first (`<name>.bak-<yyyymmdd>`).
- Never install an external skill without the user's explicit yes on that exact skill. Skills run with full agent permissions: show source, install count and what it does before asking.
- Install at user level (`-g`), not inside a project working copy, unless the user asks otherwise.
- Do not edit the bundled files when installing. Copy only.

## A - Install journey-sync

1. **Preflight** (run, report each as OK / missing):
   - `~/.claude/skills/` exists (create it if not).
   - `node` and `npx` available (needed only for job B).
   - Existing `~/.claude/skills/journey-sync`? If yes, compare `SKILL.md` and `references/templates.md` with the bundled ones and tell the user whether it is identical, older or modified locally.
2. **Copy** `assets/journey-sync/` to `~/.claude/skills/journey-sync/` (PowerShell: `Copy-Item -Recurse -Force`; bash: `cp -r`). Back up first if it existed.
3. **Verify**: both files exist, `SKILL.md` starts with frontmatter containing `name: journey-sync`.
4. **Tell the user**: start a new session (or reload skills) so it is listed, then run `/journey-sync`. First run opens an intake form.
5. **Optional prerequisites to mention**, not to run for them:
   - `/design-login` once in an interactive session, only if they want Claude Design read access through DesignSync (otherwise they give an export folder or files).
   - A folder for specs and outputs (the form asks).

## B - Install another skill

1. Ask what they need (domain, task). If a package is already named, skip to step 3.
2. **Search**: `npx --yes skills find "<keywords>"` (non-interactive: `</dev/null`, strip ANSI codes, keep top results). Try 2-3 keyword variants. Check <https://skills.sh/> pages for install counts and source.
3. **Inspect without installing**: `npx --yes skills use <owner/repo@skill>` prints the SKILL.md. Read it. Summarize in 2-3 lines what it does, any scripts it runs, any external dependency.
4. **Quality check**: prefer 1K+ installs and known sources; flag anything under 100 installs or unknown author.
5. **Ask for a yes** on that exact skill, then install: `npx --yes skills add <owner/repo@skill> -g -a claude-code -y --copy`.
6. **Verify**: folder exists under `~/.claude/skills/<skill>`; report the path and that a new session is needed.
7. To remove: `npx --yes skills remove <skill>`; to update: `npx --yes skills update`.

If nothing suitable is found, say so and offer to do the task directly or to draft a custom skill.

## C - Refresh the bundle (maintainer only)

When the maintainer improved `~/.claude/skills/journey-sync`, copy it into `assets/journey-sync/` here (replace the folder), then tell them to re-share the `skill-setup` folder. Do this only on request, and show which files changed.

## Sharing this kit

The colleague needs only the `skill-setup` folder (with its `assets/`). They place it in `~/.claude/skills/`, open a new session and say "install journey-sync". Share by zip, network folder or SVN, as the maintainer prefers.
