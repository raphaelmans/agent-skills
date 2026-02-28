---
name: agent-plan
description: Create structured implementation plans derived from user stories. Includes phased approach, API endpoints, form validation, UI mockups, and developer checklists. Use when asked to "create a plan", "plan implementation", or "break down into phases".
---

# Agent Plan Creation

Use this skill when the user asks to:
- "Create a plan for..." or "Plan how to implement..."
- "Break this down into phases" or "Create a roadmap"
- "Create agent-plans for..." or "Add to agent-plans"
- Any large feature or project that needs structured planning
- Multi-day implementation work requiring coordination

## Workflow Position

```
PRD → User Stories → **Agent Plans** → Implementation → Agent Contexts
```

Agent plans are the technical breakdown. They define **how to build** what user stories describe.

## When to Use Agent Plans

| Scenario | Use Agent Plan? |
|----------|-----------------|
| Large feature (multiple modules) | Yes |
| Multi-developer coordination | Yes |
| Phased rollout needed | Yes |
| Complex dependencies | Yes |
| Single-file change | No (just do it) |
| Quick bug fix | No |
| Simple CRUD endpoint | No |

## First Step: Context Gathering

Before creating agent plans, check if `agent-plans/context.md` exists.

**If it doesn't exist**, ask the user:

> Before creating implementation plans, I need some references:
> 1. Do you have a design system document? (provide relative path)
> 2. Do user stories exist in `agent-plans/user-stories/`?
> 3. Any other technical references (ERD, API docs)?
>
> If no documents exist, please describe the technical requirements.

Then create `agent-plans/context.md`:

```markdown
# Agent Plans Context

References and context for planning artifacts.

---

## Changelog

| Date | Change |
|------|--------|
| YYYY-MM-DD | Initial creation. Added design system reference. |

---

## Product Requirements

| Document | Path | Description |
|----------|------|-------------|
| PRD | - | Not yet provided |

---

## Design References

| Document | Path | Description |
|----------|------|-------------|
| Design System | `{relative-path}` | UI/UX guidelines |
| ERD | `{relative-path}` | Entity relationships |

---

## Captured Requirements

{If user described requirements verbally, capture them here}
```

**If it exists**, read the design system path and use it for UI mockups. If design system is missing, ask user to provide it or describe conventions, then **prepend** a changelog entry:

```markdown
| YYYY-MM-DD | Added design system reference: `path/to/design.md` |
```

## Relationship to User Stories

| Artifact | Purpose | Contains |
|----------|---------|----------|
| **User Stories** | What users can do | Acceptance criteria, edge cases, personas |
| **Agent Plans** | How to build it | Phases, API specs, form validation, UI mockups, code |

Before creating an agent plan, ensure user stories exist in `agent-plans/user-stories/`.
Reference them in the overview's Reference Documents table.

## Convention

### Directory Structure

```
<project-root>/
└── agent-plans/
    ├── {NN}-{domain}/                    # Versioned domain folder
    │   ├── {NN}-00-overview.md           # Master plan
    │   ├── {NN}-01-{phase-name}.md       # Phase 1 details
    │   ├── {NN}-02-{phase-name}.md       # Phase 2 details
    │   ├── ...
    │   ├── {domain}-dev{N}-checklist.md  # Developer checklists
    │   └── {NN}-NN-deferred.md           # Future work
    └── consolidated-dev{N}-checklist.md  # Cross-domain checklists (optional)
```

### Naming Rules

| Part | Format | Example |
|------|--------|---------|
| Domain folder | `{NN}-{domain}` | `00-server`, `01-ui`, `00-api` |
| Overview | `{NN}-00-overview.md` or `00-{domain}-overview.md` | `00-00-overview.md` |
| Phase docs | `{NN}-{phase}-{name}.md` | `00-01-infrastructure.md` |
| Dev checklist | `{domain}-dev{N}-checklist.md` | `server-dev1-checklist.md` |
| Deferred | `{NN}-NN-deferred.md` | `00-07-deferred.md` |

### Versioning Rules

1. **Start with `00-`** for initial planning
2. **Increment major (`01-`, `02-`)** for plan iterations or major pivots
3. **Keep old versions** for reference (don't delete `00-*` when creating `01-*`)
4. **Phase numbers** are sequential within a domain

## File Templates

### Overview File (`{NN}-00-overview.md`)

```markdown
# {Project/Feature} - Master Plan

## Overview

Brief description of what this plan covers.

### Completed Work (if any)

- Item 1
- Item 2

### Reference Documents

| Document | Location |
|----------|----------|
| Context | `agent-plans/context.md` |
| User Stories | `agent-plans/user-stories/{NN}-{domain}/` |
| Design System | See `context.md` |
| ERD | See `context.md` |

---

## Development Phases

| Phase | Description | Modules | Parallelizable |
|-------|-------------|---------|----------------|
| 1 | Foundation | 1A, 1B | Yes |
| 2 | Core Features | 2A, 2B | Partial |
| 3 | Polish | 3A | No |

---

## Module Index

### Phase 1: Foundation

| ID | Module | Agent | Plan File |
|----|--------|-------|-----------|
| 1A | Module Name | Agent 1 | `01-phase-name.md` |
| 1B | Module Name | Agent 2 | `01-phase-name.md` |

---

## Developer Assignments

| Developer | Modules | Focus Area |
|-----------|---------|------------|
| Dev 1 | 1A, 2A | Backend core |
| Dev 2 | 1B, 2B | Frontend |

---

## Dependencies Graph

```
Phase 1 ─────┬───── Phase 2 ─────── Phase 3
             │
        1A ──┼── 2A
             │
        1B ──┴── 2B
```

---

## Key Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Tech choice | Option A | Reason |

---

## Document Index

| Document | Description |
|----------|-------------|
| `00-00-overview.md` | This file |
| `00-01-phase-name.md` | Phase 1 details |

---

## Success Criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Build passes
```

### Phase Document (`{NN}-{phase}-{name}.md`)

```markdown
# Phase {N}: {Phase Name}

**Dependencies:** Phase {N-1} complete  
**Parallelizable:** Yes/No/Partial  
**User Stories:** US-{NN}-{NN}, US-{NN}-{NN}

---

## Objective

What this phase accomplishes.

---

## Modules

### Module {ID}: {Name}

**User Story:** `US-{NN}-{NN}`  
**Reference:** `{plan-file}.md`

#### Directory Structure

```
src/modules/{name}/
├── {name}.router.ts
├── dtos/
├── services/
└── ...
```

#### API Endpoints

| Endpoint | Method | Input | Output |
|----------|--------|-------|--------|
| `module.method` | Mutation | `{ field: type }` | `{ result }` |
| `module.query` | Query | `{ id: string }` | `{ entity }` |

#### Form Fields

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| Name | text | Yes | 1-150 chars |
| Email | email | Yes | Valid email format |
| Phone | tel | No | Max 20 chars |

#### UI Layout

```
┌─────────────────────────────────────────────┐
│  Page Title                                 │
│                                             │
│  ┌───────────────────────────────────────┐  │
│  │ Form Field                            │  │
│  └───────────────────────────────────────┘  │
│                                             │
│  [Cancel]                    [Submit]       │
└─────────────────────────────────────────────┘
```

#### Flow Diagram

```
/page-a
    │
    ▼
[Action] ─── Success toast
    │
    ▼
/page-b
```

#### Implementation Steps

1. Step one
2. Step two

#### Code Example

```typescript
// Example code
```

#### Testing Checklist

- [ ] Test case 1
- [ ] Test case 2

#### Handoff Notes

- Notify Dev X when complete
- Update overview to mark complete

---

## Phase Completion Checklist

- [ ] All modules implemented
- [ ] Tests passing
- [ ] No TypeScript errors
- [ ] Handoffs complete
```

### Developer Checklist (`{domain}-dev{N}-checklist.md`)

```markdown
# Developer {N} Checklist

**Focus Area:** {Description}  
**Modules:** {List}

---

## Module {ID}: {Name}

**Reference:** `{phase-file}.md`  
**User Story:** `US-{NN}-{NN}`  
**Dependencies:** {None / Module X}

### Setup

- [ ] Task 1
- [ ] Task 2

### Implementation

- [ ] Task 1
- [ ] Task 2

### Testing

- [ ] Test 1
- [ ] Test 2

### Handoff

- [ ] Notify Dev X
- [ ] Update overview

---

## Parallelization Summary

| Sequence | Task 1 | Task 2 |
|----------|--------|--------|
| First | Module A | Module B |
| Then | Module A (cont.) | Module C |

---

## Final Checklist

- [ ] All modules complete
- [ ] No TypeScript errors
- [ ] Integration tested
- [ ] Documentation updated
```

### Deferred Document (`{NN}-NN-deferred.md`)

```markdown
# Deferred Work

Items explicitly out of scope for current implementation.

---

## Deferred Features

| Feature | Priority | Reason Deferred |
|---------|----------|-----------------|
| Feature A | High | Time constraint |
| Feature B | Medium | Needs design |

---

## Future Considerations

- Item 1
- Item 2

---

## When to Revisit

- After MVP launch
- When X is complete
```

## Step-by-Step

### 1. Assess the Work

Before creating a plan, understand:
- Scope (how many modules/pages?)
- Dependencies (what needs to happen first?)
- Parallelization (can work be split?)
- User stories (which stories does this implement?)

### 2. Create Domain Folder

```bash
mkdir -p <project>/agent-plans/{NN}-{domain}
```

Choose version:
- `00-` for first plan in this domain
- Increment if a plan already exists and this is a major revision

### 3. Create Overview First

Write `{NN}-00-overview.md` with:
- Phase breakdown
- Module assignments
- Dependencies graph
- User story references

### 4. Create Phase Documents

For each phase, create `{NN}-{phase}-{name}.md`:
- Detailed implementation steps
- Code examples
- Testing requirements

### 5. Create Developer Checklists

Create `{domain}-dev{N}-checklist.md` for each developer:
- Task-by-task checkboxes
- Clear ownership
- Parallelization guidance

### 6. Add Deferred Document (Optional)

If deferring work, create `{NN}-NN-deferred.md`:
- What's out of scope
- Why it's deferred
- When to revisit

## Example Directory

```
agent-plans/
├── 00-server/
│   ├── 00-server-overview.md
│   ├── 01-server-infrastructure.md
│   ├── 02-server-foundation.md
│   ├── 03-server-core.md
│   ├── 07-server-deferred.md
│   ├── server-dev1-checklist.md
│   └── server-dev2-checklist.md
├── 00-ui/
│   ├── 00-ui-overview.md
│   ├── 01-ui-foundation.md
│   ├── 02-ui-discovery.md
│   ├── ui-dev1-checklist.md
│   └── ui-dev2-checklist.md
└── 01-ui/                          # Second iteration of UI plans
    ├── 01-00-overview.md
    ├── 01-01-phase-navigation.md
    └── 01-06-dev-checklist-1.md
```

## Checklist

- [ ] Check/update `agent-plans/context.md` for design system
- [ ] Verify user stories exist in `agent-plans/user-stories/`
- [ ] Reference user stories in overview
- [ ] Determine if work needs a plan (multi-module, multi-dev, complex)
- [ ] Check existing plans in `agent-plans/`
- [ ] Create domain folder with correct version prefix
- [ ] Write overview with phases, assignments, dependencies
- [ ] Create phase documents with:
  - [ ] API endpoints table
  - [ ] Form fields with validation
  - [ ] UI layouts (ASCII mockups)
  - [ ] Flow diagrams
  - [ ] Implementation steps
  - [ ] Code examples
- [ ] Create developer checklists with task breakdowns
- [ ] Add deferred document if excluding scope
- [ ] Include success criteria in overview

## Full Workflow

```
PRD
 ↓
User Stories (agent-plans/user-stories/)
 ↓
Agent Plans (agent-plans/{NN}-{domain}/)
 ↓
Implementation
 ↓
Agent Contexts (agent-contexts/)
```

| Artifact | Purpose | When Created |
|----------|---------|--------------|
| `context.md` | Project references | Before user stories |
| `user-stories/` | What users can do | Before agent plans |
| `{NN}-{domain}/` | How to build it | Before implementation |
| `agent-contexts/` | What was done | After implementation |
