---
name: design-project-agent-team
description: Assess a code repository and design a minimal Codex agent team, present a role, configuration, permission, and validation proposal, wait for explicit approval, then create and verify project agents. Use when the user asks to inspect a project and recommend or create reusable custom Codex agent roles, an agent team, .codex/agents configuration, an orchestration workflow, or long-lived project tasks. Do not use for one-off subtask delegation alone.
---

# Design Project Agent Team

## Purpose

Turn an unfamiliar repository into a justified, minimal Codex agent-team design. Separate assessment from creation so the user can review the roles, permissions, files, risks, and validation plan before anything is changed.

Respond in the user's language. Keep repository-specific rules in the repository rather than baking them into this skill.

## Non-negotiable gates

1. Begin with read-only assessment.
2. Treat a broad initial request to "create an agent team" as authorization to assess and propose, not as approval of a plan that does not exist yet.
3. Do not create or edit project files until the user explicitly approves the identified proposal version and its file allowlist.
4. Do not create visible independent tasks unless the user separately asks for them after reviewing the distinction between project agents, temporary subagents, and visible tasks.
5. Do not stage, commit, push, merge, rebase, tag, or open a pull request without separate Git authorization.
6. Never expand authorization for network access, live services, providers, accounts, credentials, profiles, ACLs, production operations, or destructive actions.
7. Use one writer for an overlapping change set. Parallelize read-only discovery and review only when useful and allowed by repository instructions.
8. Prefer built-in agent roles and ordinary task delegation. Add custom project agents only for narrow, recurring responsibilities that justify persistent instructions.

If repository instructions impose a stricter rule, follow the stricter rule.

## Phase 1: assess and propose

### 1. Resolve scope and instructions

- Identify the repository root and the exact project in scope.
- Read the applicable instruction chain completely, including `AGENTS.md`, nearer scoped instruction files, and any referenced mandatory policy or architecture documents.
- Record the user's current authorization and explicit prohibitions.
- Detect unrelated or user-owned working-tree changes with read-only Git commands when the project is a Git repository. If it is not a Git repository, record Git status as unavailable; do not describe it as clean. Do not modify user-owned changes.

### 2. Inspect representative evidence

Inspect enough evidence to understand the project without exhaustively reading every file:

- README and architecture or decision records;
- manifests, build configuration, and dependency boundaries;
- top-level modules and representative entry points;
- tests, CI, lint, type-check, release, and security tooling;
- existing `.codex`, `.agents`, or agent-team configuration;
- ownership, risk, and trust boundaries visible in the repository.

Do not browse the network merely to assess a local repository. Browse only when the user requests current external facts or when another governing instruction requires it.

Do not run tests during Phase 1 by default. Test runners can create caches, coverage files, logs, or other artifacts even when source code is unchanged. Prefer inspecting test configuration and existing evidence. Run a test only when it is necessary to make the assessment and the resulting filesystem effects are within the user's authorization.

### 3. Decide whether a custom team is warranted

Evaluate independent dimensions such as architecture, security, implementation, validation/release, code navigation, data, product, operations, or documentation. Propose a role only when all are true:

- the responsibility recurs across multiple future tasks;
- it has a distinct evidence set or review lens;
- its instructions can be narrow and testable;
- it will reduce risk or coordination cost more than it adds overhead.

Combine overlapping roles. For a small or low-risk repository, recommend no custom agents or a very small team.

Apply the scoring rubric to specialist roles. A coordinator is a governance role: retain one only when multiple retained roles, approval boundaries, or cross-role integration make it necessary, and explain that exception explicitly.

Read [references/evaluation-and-validation.md](references/evaluation-and-validation.md) when scoring roles, drafting the proposal, or preparing verification.

### 4. Produce an approval-ready proposal

Assign a short proposal ID such as `agent-team-v1`. Present:

- project snapshot and evidence inspected;
- whether custom agents are justified;
- role matrix with purpose, inputs, outputs, write authority, model/reasoning recommendation, and why each role is persistent;
- coordinator ownership and single-writer rule;
- concurrency and handoff design;
- exact files to create or edit;
- validation and activation plan;
- risks, alternatives, and intentionally excluded capabilities;
- the precise authority needed for Phase 2.

A recommendation to create no custom agents is a valid proposal. In that case, set the proposed file-operation list to `none` and explain which future changes would justify reassessment.

End Phase 1 by asking the user to approve, revise, or reject the identified proposal. Stop. Do not create placeholder configuration while waiting.

## Approval gate

Proceed only when the user clearly approves the identified proposal or provides an equally exact replacement containing the roles, permissions, and file scope. If approval is partial or changes the proposal, publish a revised proposal ID and request approval again.

Approval of project-agent files does not imply approval for visible tasks, Git operations, external services, privileged actions, or production execution.

## Phase 2: create the approved team

### 1. Freeze the implementation scope

- Restate the approved proposal ID, role set, permissions, and file allowlist.
- Recheck applicable instructions and current working-tree state.
- Refuse scope expansion discovered during implementation; return with a revised proposal instead.

### 2. Implement minimally

Use the repository's supported Codex configuration format. Inspect existing project configuration and locally available authoritative examples first. If the project has no configuration and the supported schema cannot be verified, report `BLOCKED` instead of guessing a TOML structure. Prefer focused changes such as:

- `.codex/config.toml` for project agent registration and concurrency controls;
- `.codex/agents/<role>.toml` for narrow role contracts;
- a concise `AGENTS.md` addition only when durable repository-wide rules are missing;
- a small operations or testing note only when activation is not self-evident.

Each role must state its responsibility, evidence, output contract, write boundary, prohibited actions, escalation conditions, and interaction with the coordinator. Make read-only reviewers genuinely read-only. Give production-code write authority to only one role for an overlapping change set.

Do not duplicate large architecture documents in agent prompts. Reference canonical repository files and encode only the durable role behavior needed to use them.

### 3. Keep agent types distinct

- Project custom agents are reusable role definitions loaded by eligible tasks.
- Temporary subagents are internal workers for a bounded current task.
- Visible independent tasks are user-owned conversations in the sidebar.

Creating one type does not create the others. If visible long-lived tasks are approved, create them as a separate activation step and explain that they do not automatically share complete conversation history.

## Phase 3: validate and activate

Run proportionate offline verification before reporting success:

1. Parse every TOML file with an available standard parser. First locate existing offline runtimes, including workspace-provided dependency runtimes when the environment exposes them. Do not download or install a parser. If no parser is available after safe local discovery, report `BLOCKED` and do not claim configuration validation.
2. Confirm all configured agent files exist and names/descriptions are unique.
3. Confirm write permissions match the approved role matrix and there is no overlapping multi-writer design.
4. Search for forbidden capabilities, secrets, personal paths, provider-specific credentials, implicit policy bypasses, and unapproved network or production instructions.
5. Confirm each role inherits or explicitly references the applicable repository instructions and canonical documents.
6. Run repository configuration tests, lint, or other relevant offline checks when available.
7. After separate user approval to create a visible task, start a fresh trusted task for load testing because existing tasks may not hot-reload new project configuration. Without that approval, stop at `CONFIGURED` after static validation.
8. When activation is approved, run a coordination smoke test: discovery, at least one independent review dimension, implementation only if authorized, and validation handoff.

If a check fails, do not declare the team ready. Fix only within the approved file scope; otherwise return to the approval gate.

## Final handoff

Report:

1. outcome and approved proposal ID;
2. evidence and files inspected;
3. files changed;
4. tests and exact results;
5. role and architecture risks;
6. authorization boundaries and operations not performed;
7. activation instructions and recommended next task.

State whether the team is merely configured, successfully loaded in a separately approved fresh task, or fully coordination-tested. These are different completion levels. Do not downgrade a statically valid `CONFIGURED` result merely because visible-task activation was not authorized; record activation as not performed.
