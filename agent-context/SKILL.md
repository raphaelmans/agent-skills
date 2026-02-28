---
name: agent-context
description: Log progress and update agent context files in agent-contexts directory using versioned naming convention (00-00-short-desc.md)
---

# Agent Context Logging

Use this skill when the user asks to:
- "Log progress" or "capture progress"
- "Update agent contexts"
- "Save our work" or "document what we did"
- Any request mentioning `agent-contexts`

## Convention

### File Naming

Files follow the pattern: `MM-NN-short-desc.md`

| Part | Description |
|------|-------------|
| `MM` | Major version (feature/milestone) |
| `NN` | Minor version (increment within major) |
| `short-desc` | Kebab-case description (2-4 words) |

**Examples:**
- `00-00-initial-setup.md`
- `00-01-auth-implementation.md`
- `01-00-api-refactor.md`
- `01-01-fix-middleware.md`

### Versioning Rules

1. **Always append** - Never overwrite existing files
2. **Increment minor** (`NN`) for updates within same feature
3. **Increment major** (`MM`) for new features/milestones
4. **User can specify** - If user says "02-*", start from `02-00`

### Directory Location

Agent contexts live in the project's `agent-contexts/` directory:

```
<project-root>/
└── agent-contexts/
    ├── 00-00-initial-setup.md
    ├── 00-01-auth-implementation.md
    └── 01-00-new-feature.md
```

## File Format

```markdown
# [MM-NN] Short Title

> Date: YYYY-MM-DD
> Previous: MM-NN-prev-file.md (if applicable)

## Summary

1-3 sentence overview of what was accomplished.

## Changes Made

### Category 1 (e.g., Implementation)

| File | Change |
|------|--------|
| `path/to/file.ts` | Description of change |

### Category 2 (e.g., Documentation)

| File | Change |
|------|--------|
| `path/to/doc.md` | Description of change |

## Key Decisions

- Decision 1 and why
- Decision 2 and why

## Next Steps (if applicable)

- [ ] Pending task 1
- [ ] Pending task 2

## Commands to Continue

```bash
# Any useful commands for next session
```
```

## Step-by-Step

### 1. Determine Version Number

Check existing files:

```bash
ls <project>/agent-contexts/
```

- If empty or user starts fresh: use `00-00`
- If user specifies `MM-*`: use `MM-00`
- Otherwise: increment the latest minor version

### 2. Create the File

```bash
# Create directory if needed
mkdir -p <project>/agent-contexts

# Create the file
touch <project>/agent-contexts/MM-NN-short-desc.md
```

### 3. Write Content

Include:
- Summary of work done
- Files changed (with paths)
- Key decisions made
- Next steps if applicable

### 4. Verify

```bash
ls -la <project>/agent-contexts/
```

## Example

If the latest file is `00-01-auth-implementation.md` and user says "log progress":

Create: `00-02-convention-fixes.md`

If user says "start 01-* for the new feature":

Create: `01-00-new-feature-name.md`

## Checklist

- [ ] Check existing files in `agent-contexts/`
- [ ] Determine correct version number (append, don't overwrite)
- [ ] Use kebab-case for short description
- [ ] Include date and previous file reference
- [ ] List all changed files with descriptions
- [ ] Document key decisions
- [ ] Add next steps if work is ongoing
