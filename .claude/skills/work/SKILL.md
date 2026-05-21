---
name: work
description: >
  Autonomous GitHub issue workflow with wiki lookup, strict TDD, internal
  multi-agent loop, critic gate, Git automation, PR creation, and wiki update.
---

# Skill: work

## Trigger

Match only:

`^work\[(\d+)\]$`

Captured value:
- `TASK_ID`

## Objective

Resolve GitHub issue `TASK_ID` end-to-end with:
- wiki-assisted research
- strict RED -> GREEN -> REFACTOR
- critic gate `>= 90`
- branch, commit, push, PR
- GitHub issue/project update
- wiki update for reusable fixes or patterns

## Non-Negotiables

| Rule | Requirement |
|---|---|
| TDD first | No production code before failing tests |
| Virtual roles | Use `Tester/Architect`, `Coder`, `Critic` |
| Critic gate | Must pass before push/PR |
| Wiki loop | Read first, write back if reusable knowledge emerges |
| GitHub | Use `gh` for issue/project/PR actions |
| CLAUDE.md | All existing repo rules remain active |

## State Flow

`INITIALIZED -> WIKI_CHECK -> RESEARCHING -> CODING_TDD -> CRITIC_REVIEW -> GIT_PUSH -> WIKI_UPDATE -> COMPLETED`

## Phase 1: Wiki Check & Research

### 1. Wiki scan
Search `docs/wiki/` for:
- issue keywords
- subsystem names
- past bug patterns
- auth/perf/security rules
- testing guidance
- architecture notes

Summarize only:
- prior root causes
- prevention rules
- reusable test patterns

### 2. GitHub issue scan
Run:
- `gh issue view TASK_ID`

Extract:
- title
- body
- labels
- acceptance cues
- linked branches/PRs
- explicit edge cases

### 3. Repo scan
Identify:
- affected modules
- current tests
- neighboring conventions
- likely regression zones

Research output must end with:
- scope
- assumptions
- file targets
- test entry points
- risk notes

## Phase 2: TDD Engine

### Sub-Agent: Tester/Architect
Must:
1. break requirements into behaviors
2. define failing tests first
3. identify RED expectation
4. capture edge cases
5. map acceptance criteria to tests

RED policy:
- tests written before production code
- test run must fail for the intended reason
- failure must be acknowledged before GREEN starts

### Sub-Agent: Coder
After RED is confirmed:
1. write minimum implementation
2. keep diff narrow
3. run tests until green
4. refactor only after green

### Sub-Agent: Critic (QA & Security)
Evaluate:
- requirements completeness
- TDD integrity
- regression risk
- code cleanliness
- security implications
- wiki completeness

Compute:

`Score = (Requirements Met + TDD Test Coverage + Code Cleanliness) / 3`

Output:

| Field | Requirement |
|---|---|
| `Requirements Met` | `0-100` |
| `TDD Test Coverage` | `0-100` |
| `Code Cleanliness` | `0-100` |
| `Score` | arithmetic mean |
| `Verdict` | `PASS` or `REJECT` |
| `Next Owner` | `Tester/Architect` or `Coder` |

If `Score < 90`:
- reject
- list exact gaps
- provide concrete repair guidance
- send work back into RED/GREEN/REFACTOR loop

Repeat until `PASS`.

## Phase 3: Git Automation

After Critic PASS:
1. create branch `feature/issue-TASK_ID`
2. stage relevant changes only
3. commit with semantic message
4. push branch
5. open PR
6. link issue in PR body
7. include tests and critic score in PR summary

Preferred commit prefixes:
- `feat(issue-TASK_ID): ...`
- `fix(issue-TASK_ID): ...`
- `refactor(issue-TASK_ID): ...`
- `test(issue-TASK_ID): ...`

Never add AI attribution trailers.

## Phase 4: Wiki & GitHub Update

### Wiki update rule
Update `docs/wiki/` if work produced:
- root-cause insight
- reusable prevention rule
- architecture decision
- repeatable test strategy
- recurring bug mitigation

Suggested file names:
- `preventing-<bug-slug>.md`
- `pattern-<architecture-slug>.md`
- `testing-<subsystem-slug>.md`

Required wiki sections:
1. Problem
2. Root Cause
3. Prevention Rule
4. Implementation Pattern
5. Required Tests
6. Related Issue / PR

### GitHub issue update
On the issue:
- post a comprehensive summary comment
- mention tests were written first
- include critic score
- link PR
- link wiki update if created
- move issue to `Done` or `In Review` per project policy

## Completion Checklist

| Gate | Must Pass |
|---|---|
| Wiki consulted | Yes |
| Issue researched | Yes |
| Failing tests written first | Yes |
| Tests green | Yes |
| Refactor complete | Yes |
| Critic score `>= 90` | Yes |
| Branch/commit/push/PR complete | Yes |
| Wiki updated if applicable | Yes |
| Issue updated | Yes |

## Final Output Contract

Return a compact execution report with:
- issue ID
- files changed
- tests added
- critic score
- PR URL
- wiki page updated or `not needed`
- final issue status
