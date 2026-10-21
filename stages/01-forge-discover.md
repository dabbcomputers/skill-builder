---
name: forge-discover
description: Stage 1. Read-only scan of the user's repo, in any language, to learn its stacks, layout, build and test commands, CI and deploy setup, existing docs and feature areas. Shows what it found, takes corrections, and saves the confirmed result to the build state. Writes no setup files.
argument-hint: Optional. A sub-folder to scan, or "redo" to re-run after the repo changed.
needs: read, search, shell
---

## When to Use
Run after the entry stage has recorded the system set and target tools, and before any role, KB or skill is generated. Re-run when the repo changed a lot (a new service, a new language). Out of scope: judging code quality, reading file contents in depth, and writing any role, KB or guideline file.

## What to read first
- `docs/agents/BUILD-STATE.md`: chosen system set, tools, earlier answers. Do not re-ask anything already recorded.
- [`../INDEX.md`](../INDEX.md): the "Scope" and "Independence rules" sections.

## Process
1. **Say what you are about to do** in two lines: a read-only scan, no secret files opened, nothing written until the user confirms.
2. **Map the layout.** List the top-level folders and one level below. Ignore generated and vendored folders (dependency installs, build output, caches, VCS internals). Note which folders look like separate deployable units.
3. **Identify the stacks.** Look for the repo's own manifests and config files, whatever the ecosystem (package, build, project, module or lock files; framework config; container and infra files). Name each stack by what the files say. If a file is unfamiliar, read its first lines or ask; never guess a stack from an extension alone.
4. **Find build and test commands.** Take them from manifests, task runners, make-style files, CI definitions and the README, in that order. Record each as "command, where found". If none is found, record "unknown" and ask.
5. **Find tests.** Where they live, naming pattern, which runner, and which ones CI runs. Note areas with no tests.
6. **Find CI and deploy setup.** Pipeline files, container files, infrastructure-as-code, release scripts. Record the names of environments you can see. Do not run anything that deploys or connects to a remote host.
7. **Find existing docs and agent setup.** README, a docs folder, contribution or style guides, any existing agent role, KB, skill, or tool entry files. If an agent setup already exists, list it and plan to extend, not replace.
8. **Propose feature areas.** From modules, routes, screens, packages or services, list candidate areas, each with its main folder. Keep to the units a newcomer would name; group small ones. This list feeds the KB stage.
9. **Propose engineer areas.** From the stacks and units, suggest which engineer roles fit (for example frontend, backend, mobile, data, infrastructure). Name by area, not language.
10. **Check the host.** Note the operating system and shell you are running in, since later stages run commands. Do not assume one.
11. **Present the findings** as a short summary in this order: units and stacks; build and test commands; tests and CI; deploy; existing docs and agent setup; candidate feature areas; suggested engineer areas; anything unknown.
12. **Ask for corrections**, one question at a time, starting with the most consequential unknown. Give a recommended default when you have one. When an answer is unclear, restate what you understood and ask again.
13. **Save.** After the user confirms, write the confirmed findings to the "Discovery" section of `docs/agents/BUILD-STATE.md` and mark stage 1 done. Show the user what was written.

## Things to avoid
- Opening env, key, credential or secret files, even to read key names. If a key or variable name is needed, tell the user which command to run and ask for the result.
- Running anything with side effects: deploys, installs, migrations, network calls to the user's systems, or the test suite.
- Assuming a language, framework or test runner from habit. Everything recorded comes from files in the repo or the user's answer.
- Writing absolute paths, drive letters or user names into saved results. Record paths relative to the repo root.
- Reading source files in depth. This stage maps the repo; depth is for later stages.
- Writing any file other than the Discovery section of the build state.
- Copying text from the repo's docs into the build state; summarize and link.

## Related
- [`00-build-agent-system.md`](00-build-agent-system.md): the entry stage that runs this one.
- Next: `forge-flows` (stage 2), which uses the test, CI and deploy findings here.
