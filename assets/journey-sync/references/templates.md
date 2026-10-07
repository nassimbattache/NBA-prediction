# journey-sync templates

Keep files few and rich. Update in place; never renumber IDs.

## context.md

```markdown
# Context - <feature>
- Mode: MVP | Evolution
- Front source: <path | DesignSync project | pasted | none>
- Functional spec: <paths>
- Back spec: <paths>
- Linked modules: <list>
- Output folder: <path>
- Presentation: html | pptx | md
- Language: <lang>
- Run mode: manual | scheduled (<cron>)
- Last run: <date>
```

## map.md

```markdown
# Map
## Modules and screens
| Module | Screen | Entry from | Exit to |
## Business objects and lifecycle
### <Object>
Statuses: draft -> submitted -> validated | rejected
| Transition | Trigger (UI / system) | Rule | Journey |
## Cross-module rules
| RG | Defined in | Applies in |
## Data dependencies
| Object / field | Created by | Changed by | Read by |
```

## glossary.md

```markdown
# Glossary
| Term | Definition | Module(s) | Do not confuse with |
## Design-system components (vocabulary only)
| Component | Used in |
```

## journeys.md

```markdown
# Journeys
## J-01 <name>
Actor: <role> | Priority: main | variant | Modules: <list>
| # | Step | UI | Rule | Back | System actor |
|---|---|---|---|---|---|
| 1 | <user does X> | UI-03 | RG-07 | BK-02 | - |
Variants: error / edge / per-role
## Open points
| ID | Severity | Finding | Recommended answer |
```

## decisions.md

```markdown
| ID | Date | Question | Answer | Files impacted |
```

## test-plan.md

```markdown
| TC | Journey | Rule(s) | UI | Back | Preconditions | Steps | Expected | Type (nominal/error/edge/role) |
```

## front-change-requests.md

```markdown
## <Screen>
| # | Change requested in Claude Design | Journey step | Reason |
```
