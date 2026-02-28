---
name: user-stories
description: Create user stories from PRD or raw requirements. Organizes stories by domain/module with acceptance criteria, edge cases, and personas. Supports checkpoints to summarize progress. Use when asked to "create user stories", "write stories", "break down PRD", or "create checkpoint".
---

# User Stories Creation

Use this skill when the user asks to:
- "Create user stories for..." or "Write user stories"
- "Break down this PRD/plan into stories"
- "Add stories for {feature domain}"
- **"Create checkpoint"** - Summarize stories into checkpoint file

## Workflow Position

```
PRD → **User Stories** → Agent Plans → Implementation → Agent Contexts
```

User stories are the product-level requirements. They define **what users can do**, not how to build it.

## When to Use User Stories

| Scenario | Use User Stories? |
|----------|-------------------|
| PRD or feature requirements exist | Yes |
| Need acceptance criteria for testing | Yes |
| Planning user-facing features | Yes |
| Technical infrastructure work | No (use agent-plan) |
| Bug fixes | No |
| Pure backend/API work without UI | Maybe |

## First Step: Context Gathering

Before creating user stories, check if `agent-plans/context.md` exists.

**If it doesn't exist**, ask the user:

> Before creating user stories, I need some references:
> 1. Do you have a PRD or requirements document? (provide relative path)
> 2. Any other context documents (design briefs, notes)?
>
> If no documents exist, please describe the requirements and I'll capture them.

Then create `agent-plans/context.md`:

```markdown
# Agent Plans Context

References and context for planning artifacts.

---

## Changelog

| Date | Change |
|------|--------|
| YYYY-MM-DD | Initial creation. Added PRD reference. |

---

## Product Requirements

| Document | Path | Description |
|----------|------|-------------|
| PRD | `{relative-path}` | Main product requirements |

---

## Design References

| Document | Path | Description |
|----------|------|-------------|
| Design System | - | Not yet provided |

---

## Captured Requirements

{If user described requirements verbally, capture them here}
```

**If it exists**, read the PRD path and use it for context. If PRD path is missing, ask user to provide it or describe requirements verbally, then **prepend** a changelog entry:

```markdown
| YYYY-MM-DD | Added PRD reference: `path/to/prd.md` |
```

## Directory Structure

```
agent-plans/
├── context.md                        # Project references (PRD, design system)
└── user-stories/
    ├── checkpoint-01.md              # Checkpoints at root level
    ├── checkpoint-02.md
    ├── {NN}-{domain}/                # Domain folders (required, zero-padded)
    │   ├── {NN}-00-overview.md       # Domain overview
    │   ├── {NN}-01-{story-name}.md   # Individual stories
    │   └── ...
    └── ...
```

### Naming Rules

| Part | Format | Example |
|------|--------|---------|
| Domain folder | `{NN}-{domain}` | `00-onboarding`, `01-organization` |
| Overview | `{NN}-00-overview.md` | `00-00-overview.md` |
| Story | `{NN}-{NN}-{story-name}.md` | `00-01-user-auth.md` |
| Checkpoint | `checkpoint-{NN}.md` | `checkpoint-01.md` |

## File Templates

### Overview File (`{NN}-00-overview.md`)

```markdown
# {Domain Name} - User Stories

## Overview

Brief description of this feature domain and its purpose.

---

## References

| Document | Path |
|----------|------|
| PRD | `context.md` → Section X |
| Related Domain | `{NN}-{domain}/` |

---

## Story Index

| ID | Story | Status | Supersedes |
|----|-------|--------|------------|
| US-{NN}-01 | Story Title | Active | - |
| US-{NN}-02 | Story Title | Active | - |

---

## Summary

- Total: X
- Active: X
- Superseded: X
```

### Individual Story (`{NN}-{NN}-{story-name}.md`)

```markdown
# US-{NN}-{NN}: {Story Title}

**Status:** Active  
**Supersedes:** -  
**Superseded By:** -

---

## Story

As a **{persona}**, I want to **{action}** so that **{benefit}**.

---

## Acceptance Criteria

### {Scenario Name}

- Given {precondition}
- When {action}
- Then {expected result}

### {Another Scenario}

- Given ...
- When ...
- Then ...

---

## Edge Cases

| Scenario | Behavior |
|----------|----------|
| {Error condition} | {Expected handling} |
| {Validation failure} | {Error message} |

---

## Form Fields (if applicable)

| Field | Type | Required |
|-------|------|----------|
| Field Name | text | Yes |
| Email | email | Yes |

*Detailed validation rules in agent-plans implementation docs*

---

## References

- PRD: Section X.X ({brief description})
```

### What NOT to Include in User Stories

These belong in `agent-plans/` implementation documents:

- API Endpoints (method, input/output schemas)
- Detailed form validation rules
- UI mockups / ASCII layouts
- Directory structures
- Code examples
- Implementation steps

## Checkpoint Feature

**Trigger:** User says "create checkpoint"

### Behavior

1. List existing `checkpoint-{NN}.md` files in `agent-plans/user-stories/`
2. If checkpoints exist, read the **latest** one to find which stories it covered
3. Scan all domain folders for stories **NOT** in any previous checkpoint
4. Determine next checkpoint number (zero-padded: `01`, `02`, `03`)
5. Create `agent-plans/user-stories/checkpoint-{NN}.md`

### Tracking Logic

- Read last checkpoint's "Stories in This Checkpoint" table
- Compare against all `US-{NN}-{NN}` stories in domain folders
- New checkpoint includes only stories not previously checkpointed

### Checkpoint Template

```markdown
# Checkpoint {NN}

**Date:** YYYY-MM-DD  
**Previous Checkpoint:** checkpoint-{NN-1}.md (or "None")  
**Stories Covered:** US-{NN}-{NN} through US-{NN}-{NN}

---

## Summary

Brief summary of what was covered in this batch of stories.

---

## Stories in This Checkpoint

| ID | Domain | Story | Status |
|----|--------|-------|--------|
| US-00-01 | 00-onboarding | User Authentication Flow | Active |
| US-00-02 | 00-onboarding | User Completes Profile | Active |
| US-01-01 | 01-organization | Owner Registers Org | Active |

---

## Domains Touched

| Domain | Stories Added |
|--------|---------------|
| 00-onboarding | 2 |
| 01-organization | 1 |

---

## Key Decisions

- Decision 1
- Decision 2

---

## Open Questions

- [ ] Question needing clarification
- [ ] Item to revisit
```

## Step-by-Step

### Creating User Stories

1. **Check context** - Read `agent-plans/context.md` for PRD path
2. **If no context** - Ask user for PRD path or verbal requirements, create/update context.md
3. **Read PRD** - Understand the feature domain
4. **Identify domain** - Determine which `{NN}-{domain}/` folder
5. **Create/update overview** - Add story to index
6. **Write story** - Follow template with acceptance criteria
7. **Repeat** - For each story in the domain

### Creating Checkpoint

1. **List checkpoints** - Find latest `checkpoint-{NN}.md`
2. **Read latest** - Get list of already-checkpointed stories
3. **Scan domains** - Find all `US-{NN}-{NN}` stories
4. **Diff** - Identify new stories since last checkpoint
5. **Create checkpoint** - With next number, list new stories
6. **Summarize** - Key decisions, domains touched

## Checklist

- [ ] Check/create `agent-plans/context.md`
- [ ] Ask for PRD or capture verbal requirements
- [ ] Read PRD thoroughly before writing stories
- [ ] Identify personas involved (Player, Owner, Admin, etc.)
- [ ] Create domain folder with `{NN}-` prefix
- [ ] Create/update overview with story index
- [ ] Write stories with Gherkin acceptance criteria
- [ ] Document edge cases as table
- [ ] Include simplified form fields (types only, no validation details)
- [ ] Reference PRD sections
- [ ] Create checkpoint when requested

## Relationship to Agent Plans

| Artifact | Purpose | Contains |
|----------|---------|----------|
| **User Stories** | What users can do | Acceptance criteria, edge cases, personas |
| **Agent Plans** | How to build it | Phases, API specs, form validation, UI mockups, code |

After user stories are complete, use the `agent-plan` skill to create implementation plans.
