---
name: journey-sync
description: Keep UI (Claude Design), functional spec, back spec and test plans aligned by using user journeys as the single source of truth. Starts every session with an intake form, extracts/updates journeys that respect the business rules across interconnected modules, runs a question loop to close gaps and contradictions, regenerates a journey presentation for review, then propagates the user's feedback to specs, test plans and a front change-request list. Use when the user starts a new feature, wants specs/screens/tests made consistent, says "parcours", "journeys", "align front/back/specs", "régénère les parcours", "plans de test depuis les parcours", or reviews a journey presentation.
---

# journey-sync

User journeys are the arbiter. Front and back are both **derived from journeys**, never from each other. Every UI element, rule, back item and test must trace to a journey step, or be explicitly flagged as a system actor / orphan.

## Hard rules

- **Front is read-only here.** It changes only in Claude Design. Never edit design files. Produce `front-change-requests.md` instead (one list, grouped by screen).
- **Specs are edited only by Claude Code** (this skill), from the user's decisions.
- **The design system is vocabulary, not a subject.** Do not judge colors, fonts, or components. Check behavior only.
- **Do not invent.** Where sources are silent, mark `ASSUMPTION:` and ask. On a real conflict, stop and ask; never pick silently.
- **Read whole, not sliced (MVP mode).** Load the full context of the feature each run. Do not split into per-screen tickets.
- Follow the host repo's own conventions for spec locations and naming (read its CLAUDE.md/rules); do not hardcode paths.
- Never ask the user for a fact you can look up (files, specs, code, DB). Ask only for decisions.

## Step 0 - Intake form (every new session)

Always start here, before reading anything. Use AskUserQuestion (max 4 questions per call, so use 2 calls). If `<output>/context.md` exists from a previous session, first offer: "Reuse last session's answers? (yes / edit)". Prefill from it.

Call 1:
1. **Feature + mode**: name of the feature; mode = `MVP` (full context, default) or `Evolution` (targeted change, impact analysis limited to touched modules).
2. **Front source**: Claude Design export folder / DesignSync project (needs `/design-login`) / files or screenshots pasted / none yet.
3. **Spec locations**: functional spec path(s), back spec path(s) (or "none yet").
4. **Linked modules**: modules/screens that interact with this feature (or "discover from specs/code").

Call 2:
5. **Output folder** (default `parcours/<feature-slug>/` next to the specs).
6. **Presentation format**: HTML artifact (default) / pptx / markdown.
7. **Output language** of documents and presentation.
8. **Run mode**: manual now / also propose a schedule (`/schedule`) for recurring re-runs.

Save the answers to `<output>/context.md`. Confirm in 3 lines, then proceed.

## Step 1 - Read

1. Read `context.md`, `glossary.md`, `map.md`, `decisions.md` if present (never re-ask decided items).
2. Read the full front source, functional spec(s), back spec(s) for the feature and every linked module. In Evolution mode, read linked modules at summary level only, and fully only those touched.
3. If the repo has a spec-lookup rule or specs index, use it to find related specs. For symbol/code facts use the repo's code-research tooling.

## Step 2 - Model (build or update; keep IDs stable)

Maintain these files in `<output>/` (templates: `references/templates.md`):

| File | Content |
|---|---|
| `map.md` | modules, screens, links between them, shared business objects with **status lifecycle**, cross-module rules, data dependencies (who creates / changes / reads what) |
| `glossary.md` | canonical terms; same name in two modules = flag; design-system components listed as vocabulary |
| `journeys.md` | journeys `J-xx`: actor, steps, `UI-xx` touched, `RG-xx` applied, `BK-xx` involved, system actors, happy path + error + edge + per-role variants |
| `decisions.md` | every user decision: id, question, answer, date, files impacted |
| `test-plan.md` | `TC-xx` derived from journeys and rule variants, each linked to `J-`, `RG-`, `UI-`, `BK-` |
| `front-change-requests.md` | changes to ask Claude Design, grouped by screen, each tied to a journey step |

IDs: `J-` journey, `UI-` component behavior, `RG-` business rule, `BK-` back item, `TC-` test case. Never renumber existing IDs.

Include **system actors** (batch, other module, external system, notification) so legitimate back-only items are not flagged as waste.

## Step 3 - Consistency checks

Run all; record each finding in `journeys.md` under Open points with a severity:

- UI behavior with no journey step (candidate: remove from front) / back item with no journey or system actor (candidate: remove or justify).
- Journey step with no UI support or no back support.
- Rule with no test; test with no rule.
- Contradictions (mandatory vs optional, lengths, statuses, naming across modules).
- Missing states: empty, error, loading, read-only, role/permission, boundary values.
- Object status transitions not used by any journey; journeys using transitions that do not exist.
- Cross-module: object produced by module A and consumed by B with mismatched fields or statuses.

## Step 4 - Question loop

Ask in **rounds**: every question whose prerequisites are settled, numbered, each with a recommended answer worded so "yes" accepts it. Questions that depend on an open answer wait for the next round. Use AskUserQuestion for closed choices, plain numbered text for the rest. Record answers in `decisions.md` immediately and apply them to every affected file. Repeat until no open point of severity high/medium remains, or the user says to proceed.

## Step 5 - Presentation (the user's only interface)

Generate the presentation (format from the form; for HTML use the Artifact tool with `artifact-design`). Content: scope and map overview, one section per journey (steps, rules, screens, system actors), open points with recommended answers, summary of changes since the last version. Give every journey and open point its ID so feedback can reference it. State the audience line at the top of your message.

## Step 6 - Feedback and realignment

The user reviews and replies (comments, edits, answers by ID). Then:

1. Record decisions.
2. **Impact analysis** with `map.md`: list every screen, module, rule, back item, test touched.
3. Update functional spec, back spec, test plan, map, glossary, journeys. Update front only through `front-change-requests.md`.
4. Regenerate the presentation with a "changed since last version" section. Loop to Step 3 until stable.

Report at the end: what changed, what is still open, what the user must do in Claude Design.

## Modes

- **MVP**: full context, whole feature in one pass, no fine slicing.
- **Evolution**: after MVP, small targeted changes; reuse `map.md`, impact analysis scoped to touched modules.
