---
name: forge-verify
description: Stage 10. Read-only check of the generated setup - links resolve, every role has every required section, every skill reference exists, grants match skill needs, wrappers point at real files, and no absolute paths, leftover placeholders or secret-file reads slipped in. Reports; fixes only on request.
argument-hint: Optional. A folder or stage to limit the check to.
needs: read, search, shell
---

## When to Use
Run as the last stage, and again after any later change to roles, skills, grants or wrappers. Out of scope: judging whether content is good, and changing files. This stage reports.

## What to read first
- `docs/agents/BUILD-STATE.md`: what was built and which tools were chosen.
- [`../templates/role.md`](../templates/role.md), [`../templates/skill.md`](../templates/skill.md), and the chosen tools' files in [`../wrappers/`](../wrappers/).

## Process
1. **Say what this stage does** in two lines: a read-only audit with a short report.
2. **Run the checks** below. Use search and read; use shell only for read-only listing. Do not open env, key or secret files. Every scan for placeholders, absolute paths and tool names covers only the files listed under `Generated files` in the build state, never the user's own files, which may legitimately contain template-like text or paths.
3. **Report** as a table of check, result (pass, fail, n/a) and, for each failure, the relative path and what is wrong. Group failures by severity: blocks use, should fix, minor.
4. **Offer fixes**, one group at a time. Apply a fix only after the user says yes, and re-run the failed check after.
5. **Record** the final result and mark stage 10 done in `docs/agents/BUILD-STATE.md`.
6. **Close with a short summary** for the user: the roles, skills and knowledge areas that exist, how to start (which wrapper or entry file to use), and the commands or files to try first.

## Checks
1. **Links:** every relative link in `docs/agents/**`, `docs/coding-guidelines.md` and every wrapper resolves to an existing file or heading.
2. **Roles:** each file in `docs/agents/roles/` has every section from `templates/role.md`, and no unfilled `{{...}}` placeholder.
3. **Skills:** each file in `docs/agents/skills/` has frontmatter (name, description, argument-hint, needs) and every section from `templates/skill.md`, and no placeholder.
4. **Cross-references:** every skill a role lists exists; every skill is listed by at least one role; every step owner in `flows.md` is a role; every "hands off to" names a real role.
5. **Knowledge:** every area in the routing table of `uber-level.md` has its files; every file in `KB/` is routed; every file follows the area shape.
6. **Grants:** `tools.md` has an entry for every role; each skill's `needs:` is held by every agent that may invoke it; reviewer and deployment roles lack `edit` on application source; `shell-remote` appears only where granted.
7. **Wrappers:** every wrapper points at an existing neutral file; every role and skill has one for each chosen tool; wrapper tool names match `tools.md` through the tool's form table.
8. **Independence:** no absolute path, drive letter or user name in any generated file; no tool names outside wrappers and `tools.md`'s wrapper-form mapping; no names copied from another project.
9. **Safety rules:** the design-approval gate is present in the orchestrator and flows; production commands require per-command approval in the deployment role; every role and skill that touches configuration says not to open env or secret files; final-summary rule about changed keys is present.
10. **Code documentation:** the chosen mode is recorded; in lazy mode the docs-sync skill exists and roles mention it; in all-at-once mode every source folder has a file.

## Things to avoid
- Editing during the audit.
- Reporting style opinions as failures; only the checks above count.
- Passing a check you did not actually run. Mark it n/a with a reason instead.
- Opening env, key or secret files.
- Absolute paths or user names in the report; use relative paths.

## Related
- [`09-forge-wrappers.md`](09-forge-wrappers.md): the stage whose output this verifies.
- [`../INDEX.md`](../INDEX.md): the list of what should exist.
