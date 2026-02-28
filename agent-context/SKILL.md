---
name: agent-context
description: Log progress and create or update agent context notes in `agent-contexts/` using versioned filenames (`00-00-short-desc.md`), Obsidian frontmatter, session-scoped tags (`frontend/<feature-module>`, `backend/<route-service>`), and up to 2 semantically related prior-context links. Use when asked to log progress, capture context, document work done, or update `agent-contexts` files.
---

# Agent Context Logging

Use this skill when the user asks to:
- Log or capture progress
- Save or document completed work
- Create/update `agent-contexts` notes

## File Convention

### Naming

Use: `MM-NN-short-desc.md`

- `MM`: major milestone
- `NN`: minor increment
- `short-desc`: kebab-case summary (2-4 words)

### Versioning Rules

1. Always append; never overwrite
2. Increment `NN` for same milestone updates
3. Increment `MM` for a new milestone
4. If user specifies `MM-*`, start at `MM-00`

### Location

Store notes in: `<project-root>/agent-contexts/`

## Obsidian Metadata

Every note must start with YAML frontmatter containing:
- `tags`
- `date`
- `previous`
- `related_contexts`

## Tag Taxonomy (Session-Scoped)

Always include:
- `agent-context`

Derive additional tags from files changed in the current chat session only.

Allowed tags:
- `frontend/<feature-module>`
- `backend/<route-service>`

Do not add global tags such as `area/*`, `topic/*`, or `stack/*`.

### Derivation Rules

Use only changed files listed in the current note's `Changes Made` section.

Frontend mapping:
1. `client/src/features/<feature>/...` -> `frontend/<feature>`
2. `client/src/modules/<module>/...` -> `frontend/<module>`
3. `client/src/app/...` or `client/app/...` -> `frontend/<first-route-segment>`

Backend mapping:
1. `server/src/app/api/<route...>/route.ts` -> `backend/<route...>`
2. `server/src/lib/modules/<module>/services/<service>.ts` -> `backend/<module>/<service>`
3. `server/src/lib/modules/<module>/...` -> `backend/<module>`

If both sides changed, include both tag types.
If only one side changed, include one tag type.
If neither changed, keep only `agent-context`.

## Related Context Rule

Reference up to 2 prior context files that are semantically relevant.

Selection order:
1. Highest overlap in changed paths and domain keywords
2. If no semantic overlap, use the 2 most recent notes
3. If fewer than 2 exist, include only available notes

Write references in:
- Frontmatter: `related_contexts`
- Body: `## Related Contexts`

## Template

Use: `references/file-template.md`

## Workflow

1. Determine next version:
```bash
ls <project>/agent-contexts/
```
2. Create note file:
```bash
mkdir -p <project>/agent-contexts
touch <project>/agent-contexts/MM-NN-short-desc.md
```
3. Fill template:
- Add summary, changes, decisions, next steps
- Derive tags from this session's changed files only
- Add up to 2 related context links
4. Verify output:
```bash
ls -la <project>/agent-contexts/
```

## Checklist

- [ ] Filename follows `MM-NN-short-desc.md`
- [ ] Frontmatter has `tags`, `date`, `previous`, `related_contexts`
- [ ] Tags follow `frontend/*` and/or `backend/*` from changed files only
- [ ] No unrelated global taxonomy tags
- [ ] Up to 2 semantically relevant related context links included
- [ ] Summary, changes, decisions, next steps completed
