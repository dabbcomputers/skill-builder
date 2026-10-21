# Index: what agent-system-builder does and creates

This is the contract. If something is not listed here, the builder does not create it.

## What it is

A set of skills the user runs inside **their own repo**. It interviews the user, reads the repo, and writes a tool-neutral agent setup: roles, a knowledge base, skills, tool grants, and wrapper files for each AI tool in use. First system set supported: **Software Dev**.

It builds one piece at a time. After every piece it shows the result and waits for approval before the next.

## Scope: any language, one lifecycle

The target repo can be in any language or mix of languages (web, mobile, systems, data, infra). The builder does not assume one. What it is built around is the **software development lifecycle**: understand the ask, design, approve, implement, test, review, deploy, record lessons. That lifecycle is the same everywhere. Only the repo's own facts change, and those are discovered in stage 1 and confirmed by the user.

Rules that follow from this:

- Templates and skills contain no language, framework or test-runner names. Examples in generated files come from the user's repo.
- `forge-discover` identifies the stack by reading the repo's own manifests and layout, whatever they are, and asks when unsure.
- Engineer roles are named by area (for example frontend, backend, mobile, data), not by language.
- Test and build commands are recorded from the repo and confirmed by the user, never guessed from a default.
- CODE_DOCUMENTATION is produced by reading source with the agent, not by a language-specific parser. Per-function fields are the same in every language.

## How a run goes

Each stage is its own skill, so it can be re-run alone later. Stage names are the skill names.

| # | Stage skill | What it does | Asks the user | Writes |
|---|---|---|---|---|
| 0 | `build-agent-system` (entry) | Says what it is and what it will create (this index). Asks which system set to build. | System set: Software Dev (only one now). Target AI tools. | `docs/agents/BUILD-STATE.md` |
| 1 | `forge-discover` | Read-only scan of the repo, any language: stacks in use, layout, build and test commands, CI, deploy files, existing docs. Never opens env/secret files. | Confirms or corrects what it found. | nothing yet (kept in BUILD-STATE) |
| 2 | `forge-flows` | Proposes the delivery flows and shows them as a diagram. | One question per unclear point (see "Flow questions"). | `docs/agents/flows.md` |
| 3 | `forge-roles` | Builds the orchestrator first, then each engineer, one at a time. | Approval per role. Which engineers apply to this repo. | `docs/agents/roles/*.md` |
| 4 | `forge-deployment-engineer` | Interview about hosting and access, then the role. | See "Deployment questions". | `roles/deployment-engineer.md`, `KB/deployment-infrastructure.md` |
| 5 | `forge-kb` | One KB file per feature area, plus the router. Asks the CODE_DOCUMENTATION question. | CODE_DOCUMENTATION mode. Feature list confirmation. | `docs/agents/KB/**`, `docs/agents/uber-level.md` |
| 6 | `forge-guidelines` | Drafts coding guidelines from evidence in the repo, then asks about gaps. | Conventions the code does not make obvious. | `docs/coding-guidelines.md` |
| 7 | `forge-skills` | Writes the working skills the roles call, as neutral markdown. | Approval per skill. | `docs/agents/skills/*.md` |
| 8 | `forge-tools` | Grants tools per agent and skill. | Approval of the grant table. | `docs/agents/tools.md` |
| 9 | `forge-wrappers` | Writes the per-tool wrapper files for the user, from the neutral files. | Which tools; approval of the file list. | for Claude Code: `CLAUDE.md`, `.claude/agents/*.md`, `.claude/skills/*/SKILL.md`; other tools per `wrappers/` |
| 10 | `forge-verify` | Checks every link, every role section, every skill reference. | Nothing. | report only |

## What gets created in the user's repo

```
docs/agents/
  BUILD-STATE.md              stage progress and answers, so a run can resume
  flows.md                    the agreed flows
  uber-level.md               system overview and KB router
  tools.md                    tool grants per agent and skill
  roles/
    <orchestrator>.md         the lead role that talks to the user and delegates
    <engineer>.md             one per engineer the repo needs
    deployment-engineer.md
    reviewer.md
    <role>-lessons-learned.md created on first retro entry
  KB/
    <area>/<feature>.md       one per feature, same sections each
    deployment-infrastructure.md
    tests/<area>-testing.md
  project-manager/
    lessons-learned.md
    Plan.md                   open decisions
docs/coding-guidelines.md
**/CODE_DOCUMENTATION.md      per folder; see "CODE_DOCUMENTATION modes"
  (inside docs/agents/) skills/   neutral working skills, one markdown file each
wrapper files               generated per chosen tool, for example .claude/agents/ and .claude/skills/ for Claude Code
root entry file              generated per chosen tool, for example CLAUDE.md or AGENTS.md
```

## Skills it creates for the user's repo

| Skill | Purpose |
|---|---|
| `<orch>-high-level-design` | One-page design, then stops for approval |
| `<orch>-low-level-design` | Contracts, logging plan, test plan, call-site inventory |
| `kb-logger` | Writes a fact from conversation into the right KB file |
| `<orch>-retro` | Logs a failure to the right lessons-learned file |
| `<role>-retro` (on demand) | Same, scoped to one sub-agent's domain |
| `new-feature` | Lightweight loop for small asks |
| `bug-fixes` | Root cause, confirm approach, then fix |
| `code-reviewer` | Review checklist used by the Reviewer role |
| `docs-sync` (only if lazy mode) | Creates or refreshes one CODE_DOCUMENTATION.md on first touch |
| deploy skills | Only those the Deployment answers justify (log fetcher, version checker, env helper) |

## Roles it creates (Software Dev set)

| Role | Created when |
|---|---|
| Orchestrator (the lead role that talks to the user and delegates) | Always |
| Frontend Engineer | A frontend exists |
| Backend Engineer | A backend exists |
| Other engineers (mobile, data, ML, infra-as-code) | Discovery finds them and the user confirms |
| Deployment Engineer | Always, but access is read-only unless the user grants more |
| Reviewer | Always |
| Security Optimizer | User opts in (manual trigger only) |

There is no separate Tester role. Each engineer tests what it builds.

## Flow questions (stage 2)

Asked one at a time, only where the repo does not answer them:

1. Who approves a design, and at which point must the agent stop?
2. Which changes count as "small" and may skip the full design?
3. Where do tests live and which ones run in CI?
4. Is there a shared or cross-cutting layer (auth, billing, decorators) that needs extra care?
5. How are branches, PRs and releases handled?
6. Where are open decisions and past mistakes recorded today?

## CODE_DOCUMENTATION modes (stage 5)

The builder asks once:

- **Lazy (recommended for large repos):** nothing is generated up front. The first time an agent works in a folder with no `CODE_DOCUMENTATION.md`, the `docs-sync` skill creates it for that folder only.
- **All at once:** every source folder gets one now. Slower, and goes stale unless regenerated.

Format per file: path, description, then per function the line, description, `Calls`, `Called By`.

## Deployment questions (stage 4)

Asked only as needed, never assumed:

- Environments (local, test, prod) and how code reaches each.
- Hosting and CI/CD system.
- Whether the agent may have any server access. If yes: how (SSH, cloud CLI, log API), which environments, read-only or not.
- Log source (files, container logs, an observability API).
- Secrets manager. The builder asks for the command to run, never for the secret values.
- What counts as a prod action that needs explicit approval each time.

## Safety rules baked into every generated setup

- Env, key and secret files are never opened. If a key name is needed, the user is given a command to run.
- Every prod command, read or write, needs its own explicit approval.
- The design-approval gate is mandatory, including for small changes.
- Final summaries must name every env var or key that changed, split by frontend and backend.
- Nothing is created that is not in this index.

## Build order for this project

Stages are built and tested one at a time. Status:

| Stage | Status |
|---|---|
| 0 entry | drafted, see `stages/00-build-agent-system.md` |
| tool grants model | drafted, see `capabilities.md` |
| templates and wrappers | drafted, see `templates/` and `wrappers/` |
| 1 forge-discover | drafted, see `stages/01-forge-discover.md` |
| 2 forge-flows | drafted, see `stages/02-forge-flows.md` |
| 3 forge-roles | drafted, see `stages/03-forge-roles.md` |
| 4 forge-deployment-engineer | drafted, see `stages/04-forge-deployment-engineer.md` |
| 5 forge-kb | drafted, see `stages/05-forge-kb.md` |
| 6 forge-guidelines | drafted, see `stages/06-forge-guidelines.md` |
| 7 forge-skills | drafted, see `stages/07-forge-skills.md` |
| 8 forge-tools | drafted, see `stages/08-forge-tools.md` |
| 9 forge-wrappers | drafted, see `stages/09-forge-wrappers.md` |
| 10 forge-verify | drafted, see `stages/10-forge-verify.md` |

## Independence rules

- **No private content.** Nothing in this repo, its templates, wrappers or examples comes from any particular company's or project's code, docs, names, paths or incidents. Every example is generic or invented. Content in a user's setup comes only from their own repo and answers.
- **Path neutral.** All links are relative. No absolute paths, drive letters, user names or OS-specific separators are written into any file. Commands are written for "the host shell" and the builder checks the user's OS and shell when it needs to run something, instead of assuming one.
- **Agent free.** The builder's stages and everything it generates are plain markdown. Tool-specific files are produced only by `forge-wrappers`, for the tools the user chose.

## When tools are decided

Tool grants are written at two points, never silently widened in between:

1. **At orchestration time** (stage 8, `forge-tools`): each role and skill declares the capabilities it needs; the builder proposes the grants from the defaults in `capabilities.md`, the user approves, and `docs/agents/tools.md` records them.
2. **At first need**: if a role or skill later needs a capability it was not granted (for example the first time a deploy skill needs remote shell, or a browser check is requested), the agent stops, says which capability and why, and asks the user. On a yes it adds the grant to `docs/agents/tools.md` and re-runs `forge-wrappers` for that agent only. External integrations (`mcp:<name>`) are always added this way, because they can't be known up front.
