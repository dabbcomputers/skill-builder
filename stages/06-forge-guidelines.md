---
name: forge-guidelines
description: Stage 6. Drafts the repo's coding guidelines from evidence in the code and existing docs, then asks the user about conventions the code does not make obvious. Writes docs/coding-guidelines.md, which engineers and the reviewer treat as the rulebook.
argument-hint: Optional. A section name to redo, for example "testing" or "where code lives".
needs: read, search, edit
---

## When to Use
Run after `forge-kb`. Re-run for one section when a convention changes. If the repo already has a guidelines or style document, extend it instead of replacing it. Out of scope: linter or formatter configuration, and skills (stage 7).

## What to read first
- `docs/agents/BUILD-STATE.md`: Discovery (stacks, tests, CI, existing docs).
- The knowledge files in `docs/agents/KB/`, especially the testing notes and each area's "Design patterns".
- Any existing contribution, style or guidelines document found by Discovery, and the repo's formatter and linter configuration (read for the rules they enforce, so the guidelines do not contradict them).

## Process
1. **Say what this stage does** in two lines: it turns how the code is actually written into rules, marks each rule as evidenced or asked, and asks about the rest.
2. **Gather evidence, not opinions.** For each topic below, look at the repo first: existing docs, configuration, and a sample of representative files in each area. Note the pattern and where you saw it. A convention counts as evidenced only when it appears consistently; otherwise treat it as a question.
3. **Draft the sections** below, one at a time. Each rule is one line, written as an instruction, with its source: `(seen in <relative path>)`, `(from <existing doc>)` or `(user decision)`.
4. **Ask only what the code does not answer**, one question at a time, with a recommended default. Typical gaps: which of two competing patterns is the intended one, what must never be done, and what the reviewer should block on.
5. **Handle conflicts.** If the code shows two patterns, say so, name where each is used, and ask which is current. Record the answer, and add the old pattern to tech debt in the matching knowledge file.
6. **Show the full draft** and wait for approval or edits.
7. **Write `docs/coding-guidelines.md`.** Link it from `uber-level.md` under cross-cutting conventions and from each engineer role's "What to read first".
8. Record the answers and mark stage 6 done in `docs/agents/BUILD-STATE.md`.

## Sections to draft

Include a section only if it applies to the repo; drop the rest.

1. **Where code lives:** folder layout and the responsibility of each layer; where shared constants, shared messages and shared contracts go; the rule that a new feature with a large footprint gets its own module with thin wiring.
2. **Naming and file conventions:** file and symbol naming, and the rule that a file's name must match what it does (rename or split when it stops matching).
3. **Reuse before writing:** the existing helpers, factories, base classes and caches agents must check before adding new ones.
4. **Contracts:** how external interfaces are shaped (request and response structure, error structure, stored fields); the rule that a design's field and interface tables are a closed list; one read path per field.
5. **Errors and logging:** how errors are raised and reported; the rule that every failure path logs enough to diagnose (identifiers, input sizes, position of failure); no silent default-returns; no raw internal errors shown to users.
6. **Security:** input validation at boundaries, ownership and permission checks on every data access, secrets handling, and which shared security code must not be changed casually.
7. **Testing:** where tests live, how CI selects them, what each level covers, fixtures for realistic data, and the rule that mocks do not stand in for real-world output shapes of non-deterministic components.
8. **Performance and cost:** expensive queries or calls that need explicit mention in designs; limits on result size.
9. **Review checklist:** the ordered list the reviewer applies, built from the sections above plus anything the user adds.
10. **Language-specific notes:** only where the repo itself shows them. Leave the section out if there are none.

## Output shape

```
# Coding guidelines

Scope: <which parts of the repo this covers>
How to read: each rule names its source. "user decision" rules outrank "seen in".

## <Section>
- <Rule as an instruction> (<source>)

## Review checklist
1. <Item the reviewer checks and can block on>
```

## Things to avoid
- Rules invented from general best-practice lists with no evidence in the repo and no user decision.
- Contradicting the repo's formatter or linter. Defer to them for style.
- Language, framework or product names taken from anywhere but this repo. Use the repo's own terms.
- Absolute paths or user names. Cite relative paths.
- Long essays or tutorials. One-line rules; link out for detail.
- Treating a one-off in the code as a convention.
- Writing aspirational rules the code violates everywhere without telling the user; ask first and log the gap as tech debt.
- Opening env, key or secret files to learn conventions.

## Related
- [`05-forge-kb.md`](05-forge-kb.md): the knowledge files this stage reads and links from.
- Next: `forge-skills` (stage 7).
