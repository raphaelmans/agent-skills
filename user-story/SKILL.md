---
name: user-stories
description: Generate structured user stories with acceptance criteria from any input (PRD, feature spec, verbal description, or codebase exploration). Use when asked to "write user stories", "create user stories", "break this into stories", "what are the user stories for...", or any request to define what users can do for a feature. Also trigger when the user mentions "acceptance criteria", "Given/When/Then", or wants to define feature behavior from the user's perspective. Do NOT use for single bug fixes, tiny config changes, refactors with no behavior change, or exploratory spikes.
---

# User Story Generation

## Output Structure

```
<project-root>/
└── user-stories/
    └── {domain}/
        ├── index.md                        # Domain index — lists all sub-features and story IDs
        ├── {sub-feature}/
        │   ├── US-{DOMAIN}-001.md          # One story per file
        │   ├── US-{DOMAIN}-002.md
        │   └── US-{DOMAIN}-003.md
        └── {sub-feature}/
            ├── US-{DOMAIN}-001.md          # Numbering restarts per sub-feature
            └── US-{DOMAIN}-002.md
```

### Naming Rules

| Part | Format | Example |
|------|--------|---------|
| Domain folder | lowercase, kebab-case | `orders`, `user-management` |
| Sub-feature folder | lowercase, kebab-case | `place-order`, `cancel-order` |
| Story file | `US-{DOMAIN}-{NNN}.md` (UPPERCASE domain) | `US-ORDERS-001.md` |
| Domain index | `index.md` | `index.md` |

### Numbering Rules

- Format: `US-{DOMAIN}-{NNN}` — domain UPPERCASE, numbers zero-padded to 3 digits
- **Sequential per sub-feature folder** — each sub-feature starts at 001
- IDs are stable — never renumber when adding new stories

## Story File Template

Each `.md` file contains exactly one story:

```markdown
# US-{DOMAIN}-{NNN}: {Short title}

**As a** {actor},
**I want** {capability},
**So that** {benefit}.

## Acceptance Criteria

- **Given** {precondition}, **When** {action}, **Then** {expected result}.
- **Given** {precondition}, **When** {action}, **Then** {expected result}.
```

Rules:

- Actor is inline (e.g. "buyer", "seller", "admin") — no upfront persona definitions
- Keep "I want" focused on ONE capability — split if it does two things
- Write 2–5 acceptance criteria per story
- Cover the happy path first, then key edge cases
- Each criterion must be testable — no vague language like "should work well"

## Domain Index Template

Each domain folder has an `index.md`:

```markdown
# {Domain} User Stories

> Source: {where these came from — PRD link, feature spec, interview, etc.}
> Generated: {YYYY-MM-DD}
> Last updated: {YYYY-MM-DD}

## Sub-features

### {sub-feature}

| ID | Title | Actor |
|----|-------|-------|
| US-{DOMAIN}-001 | {title} | {actor} |
| US-{DOMAIN}-002 | {title} | {actor} |

### {sub-feature}

| ID | Title | Actor |
|----|-------|-------|
| US-{DOMAIN}-001 | {title} | {actor} |
```

Update the index whenever stories are added, removed, or renamed.

## Workflow: Interview Mode

### 1. Understand the Input

Ask: **"What are we writing stories for?"**

- **PRD or document provided** → Read it fully, extract features, confirm understanding
- **Raw feature spec** → Parse it, ask clarifying questions
- **Verbal description** → Interview to build understanding
- **Existing codebase** → Explore the code, infer current behavior, identify gaps

### 2. Identify Domains and Sub-features

Propose domains (bounded areas of functionality), then sub-features within each:

> Based on what you've described, I see these domains:
>
> - **{domain-a}** — {brief description}
>   - {sub-feature-1}, {sub-feature-2}
> - **{domain-b}** — {brief description}
>   - {sub-feature-1}, {sub-feature-2}
>
> Does this grouping make sense? Anything to add, merge, or split?

Also identify actors that interact with each domain.

### 3. Interview Per Sub-feature

For each sub-feature, drill into specifics. Ask 2–4 questions per round:

- "What's the happy path for {action}?"
- "What happens when {edge case}?"
- "Can a {actor} do {action} without {precondition}?"
- "What should the system do if {failure scenario}?"
- "Are there time-based rules? (expiry, cooldowns, windows)"

Resolve answers before moving on.

### 4. Draft and Approve Stories

Once you have enough understanding, draft the stories and present them:

> Here are the draft stories for **{domain} / {sub-feature}**:
>
> **US-{DOMAIN}-001: {Title}**
> As a {actor}, I want {capability}, so that {benefit}.
>
> **US-{DOMAIN}-002: {Title}**
> As a {actor}, I want {capability}, so that {benefit}.
>
> Want me to show the full acceptance criteria, adjust these, or move on?

### 5. Write Files

After user approval, create the story files and update the domain `index.md`.

### 6. Summary

After all domains are done:

> **User stories created:**
>
> | Domain | Sub-feature | Stories | Actors |
> |--------|-------------|---------|--------|
> | {domain-a} | {sub-feature} | US-{DOMAIN}-001 – 003 | {actor} |
> | {domain-b} | {sub-feature} | US-{DOMAIN}-001 – 002 | {actor-a}, {actor-b} |

## Updating Existing Stories

- **Adding stories to existing sub-feature**: Continue from the next number, update `index.md`
- **Adding new sub-feature**: Create folder, start at 001, add section to `index.md`
- **Adding new domain**: Create domain folder with `index.md`, then sub-features as normal
