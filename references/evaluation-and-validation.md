# Evaluation and validation reference

Use this reference during Phase 1 role selection and Phase 3 verification.

## Role selection rubric

Score each candidate role from 0 to 2 on every dimension:

| Dimension | 0 | 1 | 2 |
| --- | --- | --- | --- |
| Recurrence | One-off | Occasional | Expected across milestones |
| Distinct evidence | Same as another role | Partly distinct | Clearly separate evidence set |
| Risk reduction | Cosmetic | Helpful | Protects a material boundary or release gate |
| Testability | Subjective | Partly checkable | Has explicit evidence and acceptance checks |
| Coordination value | Adds overhead | Neutral | Reduces conflicts or serial bottlenecks |

Normally keep a custom role only when it scores at least 7 of 10 and does not substantially overlap another retained role. Treat this as a decision aid, not a substitute for repository instructions.

The coordinator is a governance exception to the specialist-role threshold. Keep one only when two or more retained roles, approval boundaries, or integration ownership require it. A small project may validly receive a zero-custom-agent proposal with a file-operation list of `none`.

## Default role shapes

Use these as candidates, not a mandatory team:

| Shape | Appropriate when | Default authority |
| --- | --- | --- |
| Coordinator | Multiple roles or approval boundaries require integration | Plans and integrates; owns user authorization |
| Code scout | Navigation and impact analysis recur across modules | Read-only |
| Architecture reviewer | Layering, ADRs, or frozen baselines matter | Read-only |
| Security reviewer | Credentials, processes, isolation, privacy, or fail-closed behavior matter | Read-only |
| Implementation engineer | Approved production changes need a bounded owner | Single scoped writer |
| Validation/release reviewer | Tests, reproducibility, evidence, or release gates are substantial | Read-only except approved test artifacts |

Add domain-specific roles only when repository evidence supports them. Do not invent a role merely to make the team appear complete.

## Proposal matrix

For every proposed role include:

| Field | Required content |
| --- | --- |
| Name | Stable, unambiguous role name |
| Persistent reason | Recurrent value supported by repository evidence |
| Reads | Canonical files, modules, tests, or evidence |
| Produces | Concrete handoff or artifact |
| Writes | None, or a narrow file/module allowlist |
| Prohibitions | Network, secrets, Git, production, or project-specific boundaries |
| Escalates when | Conditions requiring coordinator or user decision |
| Runtime recommendation | Built-in/custom role, model class, reasoning level, sandbox |

Also list the exact proposed file operations. An unspecified wildcard is not an approval-ready allowlist.

## Verification matrix

### Static structure

- Required project instruction files were read.
- Configuration syntax parses successfully.
- Every configured agent target exists.
- Role identifiers and display names are unique.
- Instructions are narrow, imperative, and reference canonical project rules.

Locate existing offline parsers before declaring tool unavailability, including workspace-provided dependency runtimes where exposed. Never install or download a parser merely to pass validation. If syntax cannot be parsed after safe discovery, use `BLOCKED` rather than `CONFIGURED`.

### Authority

- Every write-capable role has an explicit allowlist.
- At most one role writes an overlapping production change set.
- Review roles cannot silently become writers.
- Git, network, credentials, accounts, profiles, ACLs, providers, production, and destructive actions remain separately gated.

### Behavioral tests

Test at least these scenarios when practical:

1. Small project: the skill recommends no custom team or a minimal team.
2. Complex project: independent review boundaries produce justified, non-overlapping roles.
3. Proposal-only request: no files are changed before approval.
4. Partial approval: the skill revises and re-requests approval rather than inferring consent.
5. Approved creation: only allowlisted files change and validation is run.
6. Activation prompt: after `CONFIGURED`, the skill actively asks whether to use temporary subagents, visible role tasks, or no activation.
7. No implied consent: configuration approval alone creates neither subagent threads nor visible tasks.
8. Temporary activation: only needed roles are spawned, concurrency is respected, and one writer is preserved.
9. Visible activation mechanism: use a user-owned visible task operation such as `create_thread`, never an internal `spawn_agent` substitute.
10. Visible initialization: every selected role gets a durable `<project> | <role>` title, an initialization-only prompt, a `READY` response, and then an idle waiting state.
11. Visible handoff: return task identifiers and openable links or creation markers, explain separate transcripts, and ask whether to pin the conversations.
12. No autonomous loop: visible role conversations receive later work only through explicit user or coordinator follow-up; they do not continuously run or automatically message one another.

During proposal-only testing, avoid commands that can create `__pycache__`, coverage data, tool caches, or logs. If the project is not a Git repository, report Git status as unavailable rather than clean.

### Completion labels

- `PROPOSED`: assessment complete; awaiting approval.
- `CONFIGURED`: approved files created and statically validated.
- `LOADED`: a fresh task confirms the configuration is available.
- `TASKS-READY`: selected long-lived visible role conversations completed `READY` initialization and are idle awaiting follow-up.
- `TEAM-VERIFIED`: coordination smoke test passes.
- `BLOCKED`: an approval, dependency, or safety gate prevents further progress.

Never collapse these labels into a single ambiguous "done" state.
