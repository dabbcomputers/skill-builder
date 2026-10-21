---
name: forge-deployment-engineer
description: Stage 4. Interviews the user about hosting, environments, access, logs and secrets handling, then writes the deployment engineer role and a deployment-infrastructure knowledge file. Access is read-only unless the user explicitly grants more, and every production command needs its own approval.
argument-hint: Optional. "redo" to revisit access rules after infrastructure changed.
needs: read, search, edit
---

## When to Use
Run after `forge-roles`. Always run, even for repos that deploy nowhere; the role then records "no deploy access" and stays diagnose-only. Out of scope: performing any deploy or connecting to any remote system. This stage only asks and writes documents.

## What to read first
- `docs/agents/BUILD-STATE.md`: Discovery section (CI, container, infrastructure files, environments seen) and earlier answers.
- `docs/agents/flows.md`: the release and incident flows this role serves.
- [`../templates/role.md`](../templates/role.md) and [`../capabilities.md`](../capabilities.md).

## Process
1. **Say what this stage does** in two lines: it asks about how code reaches users and what the agent may touch, and it never connects to anything while doing so.
2. **Start from what Discovery found.** Present the environments, hosting hints, CI and release files already seen, and ask the user to confirm or correct. Skip questions already answered.
3. **Interview, one question at a time**, with a recommended default where one exists:
   1. Which environments exist (for example local, test, production) and how does code reach each one?
   2. What hosts them, and what runs the pipeline?
   3. Should the agent have any server access at all? If no, the role is diagnose-from-provided-output only.
   4. If yes: by what means (remote shell, cloud command line, a log or monitoring API), to which environments, and read-only or not?
   5. Where do logs live, and how are they read without logging in (file, container logs, an observability service)?
   6. How are secrets managed? Ask for the command or tool that shows names or injects values; do not ask for, read or store any value.
   7. How is a deployed version confirmed (version endpoint, tag, build number)?
   8. What is the order of rollout and the approval before each higher environment?
   9. What counts as a production action? Default: every command against production, read or write.
   10. Is there a rollback procedure, and who may trigger it?
   11. Are there test accounts or fixtures that sanity checks may use? Ask where they are documented, not for credentials.
4. **Handle unclear answers.** Restate what you understood and ask again. Unresolved items go under "Open questions" in the knowledge file; never guess infrastructure facts.
5. **Draft the knowledge file** `docs/agents/KB/deployment-infrastructure.md` with: environments and how code reaches each, hosting and pipeline, log sources, version confirmation, rollout order, rollback, secrets handling (names and commands only), and open questions. Show it and wait for approval, then write it.
6. **Draft the role** `docs/agents/roles/deployment-engineer.md` from `templates/role.md` with these rules:
   - Role: the only agent allowed any remote or production access, and only as granted in the interview. Investigates and deploys; when a fix is a code change, hands it back to the orchestrator for the owning engineer. It never edits application source.
   - Capabilities: read, shell, shell-remote only if granted, skill. No edit access to application source.
   - What to read first: the deployment knowledge file, its own lessons file (created on first retro entry), and the open-decisions file.
   - Process: follows the release and incident flows in `flows.md`. Baseline before change, deploy to the lowest environment, validate, then each higher one with its own approval, then a verdict.
   - When to stop and ask: every production command, read or write, needs its own explicit approval in chat; approval for one command does not carry to the next. Also before anything destructive, anything that changes secrets or infrastructure, and any step the interview marked as approval-required.
   - Things to avoid: opening env or secret files, even for key names (ask the user to run a command and paste names); batching several production commands under one approval; fixing application code itself; assuming an environment is safe because it is "just test".
7. **Show the role and wait for approval**, then write it.
8. **Suggest deploy-related skills** only where the answers justify them (for example a log fetcher if an observability API exists, a version checker if there is a version endpoint, an environment helper if test access exists). List them for stage 7; do not write them here.
9. Record answers and mark stage 4 done in `docs/agents/BUILD-STATE.md`.

## Things to avoid
- Connecting to, deploying to or probing any remote system during this stage.
- Opening env, key or secret files. If a variable name is needed, ask for a command to run.
- Storing credentials, hostnames of private systems the user did not choose to record, absolute local paths or user names. Record the access method and where it is documented.
- Granting remote or write access by default. Default is none, then read-only if asked, then more only on explicit request.
- Assuming a cloud, container system, CI product or log service.
- Writing tool-specific names. Use abstract capabilities; stage 8 and 9 translate them.
- Asking several questions at once.

## Related
- [`03-forge-roles.md`](03-forge-roles.md): builds the other roles.
- Next: `forge-kb` (stage 5).
