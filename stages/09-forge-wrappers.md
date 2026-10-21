---
name: forge-wrappers
description: Stage 9. Writes the thin per-tool wrapper files for the AI tools the user chose - root entry file, sub-agent files and skill files - from the neutral roles, skills and tool grants. Wrappers hold only frontmatter and a link; they are regenerated, never hand-maintained.
argument-hint: Optional. A tool name, or a single role or skill to regenerate.
needs: read, search, edit
---

## When to Use
Run after `forge-tools`. Re-run for one agent after a new grant, or for everything after roles or skills change. Out of scope: changing role, skill or grant content; this stage only translates and writes.

## What to read first
- [`../wrappers/README.md`](../wrappers/README.md) and the form file for each chosen tool, for example [`../wrappers/claude.md`](../wrappers/claude.md).
- `docs/agents/tools.md`, `docs/agents/roles/*.md`, `docs/agents/skills/*.md`.
- `docs/agents/BUILD-STATE.md`: the tools chosen in the entry stage.

## Process
1. **Say what this stage does** in two lines: it writes the small files each tool needs to load your neutral roles and skills, and it lists them before writing.
2. **For each chosen tool, find its form.** If `wrappers/<tool>.md` exists, use it. If not, ask the user for the tool's skill and agent file formats and its tool names, or read its documentation; do not guess. Add the new form file to the builder's `wrappers/` folder and fill the tool's column in the capabilities table, then continue.
3. **Plan the file list.** For each tool, list every wrapper to write: the root entry file (with the role-selection question), one file per sub-agent role, one file per skill. The lead role is the root session and gets a section in the root entry file, not its own agent file. Show the list with each file's relative path.
4. **Translate grants.** For each agent, convert its capabilities in `tools.md` to the tool's concrete names using the form's table. If the tool has no per-agent restriction, mark the grant soft in `tools.md`.
5. **Check for hand edits.** If a target file already exists and differs from what would be generated, show the diff and ask: overwrite, keep, or merge. Never overwrite silently. An existing root entry file is extended with the new role sections, not replaced.
6. **Wait for approval of the file list**, then write the files. Each wrapper contains: the tool's frontmatter, one sentence saying it is only wiring, and a relative link to the neutral file. Copy the neutral skill's description and argument hint into skill wrappers so the tool can match them.
7. **Expose the builder's own stages** if the user wants to re-run them from inside their repo: write a skill wrapper for each stage that points at the installed copy of `stages/`. Ask first.
8. **Record** the files written per tool and mark stage 9 done in `docs/agents/BUILD-STATE.md`.

## Rules for wrappers
- No role or skill content inside a wrapper.
- All links are relative and resolve from the wrapper's own location.
- Concrete tool names appear only here and in the wrapper forms.
- Wrappers are generated. To change behavior, edit the neutral file or `tools.md`, then regenerate.

## Things to avoid
- Guessing another tool's format or tool names.
- Writing content into a wrapper "for convenience".
- Overwriting hand-edited files without showing the diff.
- Absolute paths, drive letters or user names in any wrapper.
- Writing a wrapper for a role or skill that does not exist, or skipping one that does.
- Widening a grant by editing a wrapper.

## Related
- [`../wrappers/README.md`](../wrappers/README.md)
- [`08-forge-tools.md`](08-forge-tools.md): provides the grants.
- Next: `forge-verify` (stage 10).
