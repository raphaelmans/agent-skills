---
name: dc-skills
description: Execute machine-bound skills against the local machine by combining Desktop Commander with a read→execute→verify pipeline. Trigger whenever a request needs both a skill's domain knowledge AND machine action — running a local CLI from a skill's syntax, refactoring files on disk, driving a project's test or lint toolchain, analyzing local data in a REPL, or any task where a SKILL.md prescribes a command Desktop Commander must execute. Also trigger on "/dc-skills", "use dc pipeline", or "run this skill on my machine". Do NOT trigger for knowledge-only skills — interview/conversation patterns, thinking frameworks, writing-style guides, evaluation criteria (e.g. grill-me, clarify, distill, critique, copywriting) — or any skill whose SKILL.md contains no runnable commands or file paths. The tell: if the target SKILL.md contains runnable commands or disk paths, use dc-skills; if it's just how-to-think/how-to-respond, skip it and run the target skill directly.
---

# DC Skills — The Desktop Commander + Skills Pipeline

A meta-skill. It does not do anything on its own. It enforces the correct order of operations whenever another skill must be executed against a real machine via Desktop Commander.

## Core pipeline

```
User Request
     ↓
1. Read the target SKILL.md  (view / read_file)
     ↓
2. Classify the skill        (machine-bound? or knowledge-only?)
     ↓
3. Derive the action         (command for machine-bound; behavior for knowledge-only)
     ↓
4. Execute                   (DC for machine-bound; just respond for knowledge-only)
     ↓
5. Verify                    (only for machine-bound: read-back, file check, exit code)
```

## Classifying the skill (step 2)

Not every skill needs Desktop Commander. After reading the SKILL.md, decide which kind it is:

**Machine-bound** — needs DC. The skill's body contains:
- Shell commands or CLI invocations (a package manager, a test runner, a domain CLI)
- File paths to read/write on the user's disk
- Process requirements ("requires X running", "start a REPL", "in your project directory")
- Typical shapes: anything driving `git`, a project's build/test/lint toolchain, a database CLI, a note or asset CLI, a browser-automation CLI, a code-mod script

For these, run the full pipeline: read → derive command → execute via DC → verify.

**Knowledge-only** — does NOT need DC. The skill's body contains only:
- Interview / conversation patterns
- Thinking frameworks
- Writing / response style guidance
- Evaluation or review criteria

For these, read the SKILL.md to load the behavior into context, then just respond. Skipping DC is correct — wrapping conversation in a "read-then-execute" ceremony wastes turns and adds no value.

**Hybrid** — starts knowledge-only, ends machine-bound. Example shape: a skill that plans through a design as knowledge, then actually creates files, issues, or commits. Treat each phase on its own terms — the thinking phase needs no DC, the execution phase does.

**The tell:** if the SKILL.md contains runnable commands or specific file paths, it's machine-bound. If it's entirely instructions for how to think or respond, it's knowledge-only.

## Rules

1. **Read the skill first. Actually read it.** Do not answer from the skill's description, from the system prompt's `<available_skills>` summary, or from prior knowledge of what the skill "probably" does. Use `view` (or `Desktop Commander:read_multiple_files`) on the actual `SKILL.md` before deriving any command. Skills change — your memory of them is stale.

2. **Derive the command from the skill, not from intuition.** The skill's syntax is authoritative. If the SKILL.md shows an exact command shape, use that shape. Do not invent flags. Do not substitute what "should work."

3. **Execute on the actual machine via Desktop Commander.** `start_process` for shell commands. `edit_block` / `write_file` / `create_file` for direct filesystem ops. Never use sandbox-only tools (a chat harness's `create_file`, analysis REPL) when the target is the user's host machine — they land in the wrong place.

4. **Verify.** Every time. Read the file back. Re-query the state. Do not trust the first line of stdout — many CLIs emit banners, deprecation notices, or update warnings before the real output, and a failed command can still exit 0.

5. **Try before theorizing.** If Desktop Commander is loaded and the skill is readable, the cheapest path to truth is to run the command. Hypothesizing about why it might fail burns turns and erodes trust when the hypothesis is wrong.

## When to use this skill

Trigger `dc-skills` whenever the request involves **a machine-bound skill + machine execution**. Pattern examples (not exhaustive):

- `/<skill-name> <task>` for any machine-bound skill → read its SKILL.md → derive the command → run via DC → verify
- Applying a project's conventions (test layout, lint rules, architectural boundaries) and then actually running them
- Refactoring files on disk and then running the test suite
- Loading local data into a REPL and analyzing it
- Any task where a skill exists *and* the deliverable touches the filesystem or a local process

Do NOT trigger `dc-skills` for:
- **Knowledge-only skills** — read the SKILL.md to load the behavior, then just respond. No DC ceremony.
- Pure advice or planning responses with no machine action
- Questions about a skill's contents ("what does X skill do?") — just `view` it and answer
- Work inside the chat sandbox that never needs to reach the user's host

**Rule of thumb:** `dc-skills` earns its keep when steps 4 (execute) and 5 (verify) have actual work to do. If the skill is pure behavior, skip this meta-skill and just run the target skill directly.

## Execution template

For a request like **"/<skill-name> <task>"**:

```
1. view <skills-root>/<skill-name>/SKILL.md
   (see "Finding skills" below — path varies by harness)

2. Extract the relevant command pattern from the SKILL.md.

3. Fill in parameters from the user's task. If the skill references a vault,
   project, or path, resolve it from an authoritative source (app config,
   env var, repo root detection) — don't guess.

4. Execute via Desktop Commander:
   - Shell:       Desktop Commander:start_process with the derived command
   - File write:  heredoc via start_process, OR edit_block for surgical changes
   - REPL:        start_process(<interpreter>) + interact_with_process

5. Verify:
   - For new files: get_file_info + read back, or the skill's own read command
   - For edits: re-read the edited region, or run tests
   - For processes: check exit code and parse stdout, not just the first line
```

## Finding skills

Skill discovery depends on the harness. Common layouts:

- **Claude Code** (user scope): `~/.claude/skills/<skill-name>/SKILL.md`
- **Claude Code** (project scope): `.claude/skills/<skill-name>/SKILL.md` relative to the repo root
- **Hosted chat harnesses**: skills may be mounted read-only at a path supplied by the system prompt (often under `/mnt/skills/...`)
- **Skill managers** (e.g. `skillshare`): entries under `~/.claude/skills/` may be symlinks into the manager's canonical storage — read the manifest if present, and use `cp -RL` to dereference when you need a real copy (for packaging, archiving, etc.)

When in doubt, list the skills directory before guessing a path.

## Known gotchas

- **Two "write a file" tools may coexist.** A sandbox-scoped `create_file` writes to the chat VM, not the user's host. Always prefer `Desktop Commander:` tools or heredoc via `start_process` when the target is the host. If a write reports success but `Desktop Commander:get_file_info` reports ENOENT, you wrote to the wrong filesystem.

- **Paths must be verified, not guessed.** Usernames, home directories, project roots, vault or data-dir paths — confirm from an authoritative source (env var, app config file, repo detection) rather than pattern-matching a similar-looking path from memory. A one-letter difference in a username is enough to silently write into the wrong place.

- **GUI-dependent CLIs require their app to be running.** Some CLIs (note apps, editors, browser automation) fail or hang when their host app is closed. If a command hangs with no output, check whether the supporting app is open before debugging anything else.

- **Writing directly to a managed data dir can bypass its indexer.** If you bypass an app's own "create" command by writing a file straight to disk, the app may not see it until it reindexes. When both options exist, prefer the app's own CLI unless content escaping is impractical (e.g. multi-line content with quotes or frontmatter — a heredoc is cleaner there).

- **First line of stdout is rarely the result.** Many CLIs print update warnings, banners, or deprecation notices before the real output. Skim past them; check the exit code separately from parsing stdout.

- **Skills can be authored for a different machine.** A skill may hardcode paths from the author's OS (e.g. a WSL path like `/mnt/d/...`) or assume a particular shell or package manager. Scan the paths before trusting them. When a skill's assumptions don't match the current environment, adapt the command from first principles rather than copying a broken literal.

- **Skill manifests can drift from the filesystem.** If a skill manager tracks skills in a manifest file, the manifest is a cache — it can lag behind the actual contents of the skills dir. When diagnosing a "skill not found" error, trust the filesystem over the manifest.
