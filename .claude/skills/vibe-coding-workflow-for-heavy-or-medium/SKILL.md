---
name: vibe-coding-workflow-for-heavy-or-medium
description: Run a document-driven Vibe Coding workflow for heavy or medium AI-assisted software projects. Use when the user wants to build or refactor a complex, multi-module project with Codex, Claude Code, Cursor, or another coding agent; asks for vibe coding; wants full requirements, layered design docs, task decomposition, multi-agent implementation with a coordinating main agent, progress tracking, or verification loops; or needs to turn a large product idea into an executable engineering plan. For small features, scripts, or prototypes, prefer vibe-coding-workflow-for-light.
---

# Vibe Coding Workflow

Use this skill to turn a large, vague, or complex coding goal into a controlled, multi-phase
engineering workflow: requirements, layered design, task split, multi-agent implementation,
and verification. Prefer this workflow for heavy or medium projects where a single prompt
would create too much ambiguity, context pressure, or unreviewable code. For small features
or scripts, use vibe-coding-workflow-for-light instead.

## Core Rules

- Do not start implementation until the goal, scope, and success criteria are clear enough to test.
- Keep project knowledge in files, not only in chat. Use `doc/` for planning artifacts and update existing project state files when present.
- Make every phase produce an artifact that the next phase consumes.
- Keep tasks small enough for one sub-agent pass. A task should have explicit files, steps, and checks.
- Delegate independent tasks to sub-agents; use the main agent to coordinate, review diffs, and track progress.
- Treat AI output as a draft until verified with diffs, tests, lint, type checks, and runtime checks.
- Pause and ask the user when a requirement, product decision, data contract, destructive action, or external dependency is unclear.

## Workflow

### 1. Stabilize The Project

Inspect the repository before planning. Identify the language, framework, package manager, test
commands, lint commands, existing conventions, and module boundaries. For existing projects, map
the current architecture so the design phase can account for integration points. If the project
is new, choose conservative defaults and prefer tooling the user already knows.

Create the full planning directory structure:

```text
doc/
  proposal.md
  high-level-design.md
  detailed-design.md
  prompt.md
  tasks/
    progress.md
```

### 2. Clarify Requirements

Interview the user or the existing codebase before writing code. Convert rough intent into
`doc/proposal.md`.

Include:

- Goal and non-goals
- Target users or runtime context
- Inputs, outputs, and external dependencies
- Functional requirements
- Constraints and assumptions
- Success criteria and acceptance checks
- Open questions

If important answers are missing, ask targeted questions before moving to design.

### 3. Design In Layers

Generate or update layered design docs, each building on the previous:

- `doc/high-level-design.md` for architecture, modules, data flow, storage, dependencies, and major tradeoffs.
- `doc/detailed-design.md` for module responsibilities, interfaces, file layout, algorithms, errors, tests, and edge cases.

Require the design to call out risk, ambiguity, and alternatives. Do not hide uncertainty in confident prose.

### 4. Split Into Independent Tasks

Break `doc/detailed-design.md` into independent, parallelizable task files:

```text
doc/tasks/<module-or-feature>.md
doc/tasks/progress.md
```

Each task file must include:

- Objective
- Input docs
- Files expected to change
- Implementation steps
- Tests and checks
- Definition of done
- Dependencies on other tasks

`progress.md` must be the shared source of truth. Use checklists for task state and keep notes short.

### 5. Generate The Control Prompt

When the project is ready for agentic implementation, generate `doc/prompt.md`. It should define:

- Main agent role: coordinate, track progress, review diffs, run checks, update `progress.md`.
- Sub-agent role: implement exactly one task or module, add tests, report blockers, update task status.
- Required context files: proposal, designs, task files, progress.
- Guardrails: ask on ambiguity, avoid unrelated refactors, do not skip tests, do not overwrite user work.
- Verification commands and final reporting format.

Load detailed templates from `references/prompt-templates.md` when drafting prompts or planning docs.

### 6. Implement With Coordinated Agents

Use a main agent to coordinate and sub-agents to implement. Work from `doc/tasks/progress.md`,
one task or tightly related group at a time. Before edits, inspect the relevant files and confirm
the task still matches the codebase.

Main agent responsibilities:
- Select the next unblocked task from `progress.md`.
- Dispatch independent tasks to sub-agents when boundaries are clean.
- Review every sub-agent's diff before accepting it.
- Update `progress.md` with status, commands run, failures, and next steps.
- Escalate ambiguities or cross-cutting concerns to the user.

Sub-agent responsibilities:
- Read only the context needed for the assigned task.
- Implement exactly the scoped change.
- Add or update focused tests.
- Run the task's checks.
- Report the diff, test results, and any blockers back to the main agent.

Prefer serial execution when tasks chain; prefer parallel sub-agents when tasks are truly
independent and have no shared state.

### 7. Verify And Close

Before calling the work done:

- Run the relevant test suite.
- Run lint and type checks when available.
- Run a smoke test or dev server for user-facing behavior when applicable.
- Inspect the final diff for unrelated edits, generated clutter, secrets, and accidental rewrites.
- Update project state or session logs when the session produced durable context.
- Report what changed, what was verified, and remaining risks.

## Templates

Use `references/prompt-templates.md` for reusable prompts and document skeletons:

- Requirements discussion prompt
- Proposal template
- High-level design template
- Detailed design template
- Task file template
- Progress file template
- Main-agent prompt
- Sub-agent prompt
- Verification checklist
