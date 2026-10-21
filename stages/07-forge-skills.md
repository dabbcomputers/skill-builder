---
name: forge-skills
description: Stage 7. Writes the working skills the roles call, as neutral markdown under docs/agents/skills/. Core set - high-level design, low-level design, knowledge logger, retro, small-change loop, bug fix, code review, and docs-sync when code documentation is lazy - plus deploy skills only where earlier answers justify them. One skill at a time, each approved.
argument-hint: Optional. A single skill name to build or redo.
needs: read, search, edit
---

## When to Use
Run after `forge-guidelines`. Re-run to add a skill later. Out of scope: tool grants (stage 8) and tool-specific wrapper files (stage 9). Skills written here contain no tool names.

## What to read first
- `docs/agents/flows.md`, `docs/agents/roles/*.md`, `docs/agents/KB/` and `docs/coding-guidelines.md`.
- [`../templates/skill.md`](../templates/skill.md): the exact section order.
- `docs/agents/BUILD-STATE.md`: code documentation mode, deploy skill suggestions from stage 4.

## Process
1. **Say what this stage does** in two lines: it writes the repeatable procedures the roles invoke, one at a time, each shown to you first.
2. **Propose the skill list** from the table below, keeping only skills that match the agreed flows. Name them with the orchestrator's name as prefix when only it uses them. Ask the user to confirm, drop or add.
3. **Build one skill at a time.** For each:
   1. Draft from `templates/skill.md` using the spec below, filled with this repo's real file paths and rules.
   2. Set `needs:` to the abstract capabilities the skill uses.
   3. Show it, take corrections, write it to `docs/agents/skills/<name>.md`.
4. **Assign each skill to the role that runs it.** A role lists a skill only if it holds the capabilities the skill needs (defaults in `capabilities.md`). If the orchestrator only decides and delegates, list shell-using skills under the engineer that runs them, not under the orchestrator.
5. **Link them.** Add each skill to the "Skills it may invoke" list of the roles that use it, and to the flow steps in `flows.md` that call it.
6. **Check ownership.** Every skill is listed by at least one role, and every role-listed skill exists.
7. Record the list, append every written path to `Generated files`, and mark stage 7 done in `docs/agents/BUILD-STATE.md`.

## Core skills and what each must contain

| Skill | Purpose | Must contain |
|---|---|---|
| `<orch>-high-level-design` | One-page design, then stop | Confirm problem and goal; identify affected knowledge areas and read them; verify every external fact against real sources and mark the rest uncertain; one page covering problem, approach, what changes where, alternatives rejected, risks, the stored fields to add or reuse, the interface contract, the security approach and the testing approach; present and stop until approved |
| `<orch>-low-level-design` | Contracts for an approved design | Field and interface tables as a closed list; files listed by layer; reused helpers named; logging plan listing every failure path; test plan with where tests live and how CI selects them; call-site inventory when a signature changes; blast-radius section (shared code, expensive queries, security boundaries); consistency check against the high-level design |
| `kb-logger` | Persist a fact from conversation | Decide the right knowledge file, check for a duplicate, make the smallest correct edit, update every affected file together, check new links resolve, tell the user in one or two lines |
| `<orch>-retro` | Log the orchestrator's own mistake | Attempted, went wrong, root cause, one concrete change for next time; pick the right log (orchestration lessons vs a role's own lessons file, created from the same header on first use); fix any knowledge gap that contributed |
| `<role>-retro` (on demand) | Same, scoped to one role | Created the first time that role logs a mistake |
| `small-change` | Lightweight loop | Read, short plan, implement with tests, review; the agreed definition of "small" from `flows.md`; escalate to the full design flow if the change touches shared code or contracts |
| `bug-fix` | Root cause first | Trace the full flow, including any background or asynchronous path, before explaining; state cause, options, critique, recommendation; stop for approval before dispatching a fix |
| `code-review` | Reviewer procedure | Apply the guidelines' review checklist; findings ranked with file, rule and failure scenario; report only |
| `docs-sync` (lazy mode only) | Create or refresh one folder's code documentation on first touch | Check for the file, generate it in the agreed format by reading source, never overwrite hand-written notes silently, report what was created |

Deploy-related skills (log fetcher, version checker, environment helper) are written only for answers recorded in stage 4. Each states which environments it may touch and that production use needs per-command approval.

## Rules for every skill
- Sections in the order of `templates/skill.md`; frontmatter has name, description, argument-hint, needs.
- The description says exactly what the skill does and when, since it is the only text loaded before use.
- Short enough to read in full. Link to knowledge files; do not copy them.
- Steps are numbered and plain. Stop points are explicit.
- Read-only skills say so.

## Things to avoid
- Tool names, product names or paths that are not from this repo. Examples are invented or taken from this repo.
- Absolute paths or user names; use relative links.
- Skills the flows never call.
- A skill that edits files outside its stated scope.
- Writing "Things to avoid" entries that have not been earned; start with the rules above and let retros add the rest.
- Opening env, key or secret files in any skill. Skills ask for a command's output instead.
- Building the next skill before the current one is approved.

## Related
- [`06-forge-guidelines.md`](06-forge-guidelines.md): provides the rules skills cite.
- Next: `forge-tools` (stage 8).
