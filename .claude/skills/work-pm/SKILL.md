---
name: work-pm
description: >
  Interactive GitHub issue workflow with a mandatory PM pre-flight, then full
  autonomous wiki-assisted TDD execution, critic gate, Git automation, PR
  creation, and wiki update.
---

# Skill: work-pm

## Trigger

Match only:

`^work-pm\[(\d+)\]$`

Captured value:
- `TASK_ID`

## Objective

Before any GitHub or implementation action:
- ask exactly 3 PM-grade scoping questions
- treat answers as hard parameters
- then execute the same engine as `.agents/skills/work/SKILL.md`

## Hard Rules

| Rule | Requirement |
|---|---|
| Exact question count | Ask exactly 3 questions |
| Ask first | No GitHub lookup or code before answers |
| TDD first | RED before GREEN |
| Critic gate | `Score >= 90` required |
| Wiki loop | Same read/write behavior as `work` |

## PM Pre-flight

Ask exactly 3 precise questions covering:
1. business outcome
2. highest-priority edge case
3. architecture, UX, or API preference

Question rules:
- each must change implementation or tests
- avoid overlap
- avoid codebase trivia discoverable from files
- keep answerable in short form

After answers:
- store them as `PM_PARAMETERS`
- inject them into acceptance criteria, tests, and tradeoff decisions

## State Flow

`INITIALIZED -> PM_PREFLIGHT -> WIKI_CHECK -> RESEARCHING -> CODING_TDD -> CRITIC_REVIEW -> GIT_PUSH -> WIKI_UPDATE -> COMPLETED`

## Execution Engine

After PM pre-flight, run `work` logic with these additions.

### Tester/Architect
Must convert `PM_PARAMETERS` into:
- explicit acceptance tests
- edge-case tests
- tradeoff-aware RED plan

### Coder
Must implement minimum code consistent with:
- PM answers
- repo conventions
- existing safety constraints

### Critic (QA & Security)
Must verify:
- requirements were met
- TDD was honored
- PM answers materially affected test plan or implementation
- code is clean and safe

Compute:

`Score = (Requirements Met + TDD Test Coverage + Code Cleanliness) / 3`

If `Score < 90`:
- reject
- name whether failure came from requirements, tests, or implementation
- route back to the correct role

## Git + Wiki + GitHub

Follow `work` for:
- branch creation
- semantic commit
- push
- PR
- issue update
- wiki update

Additionally include in issue comment and PR:
- the 3 PM answers
- how they changed scope, tests, or implementation

## Completion Checklist

| Gate | Must Pass |
|---|---|
| Exactly 3 PM questions asked | Yes |
| PM answers captured | Yes |
| Wiki consulted | Yes |
| Failing tests written first | Yes |
| Critic score `>= 90` | Yes |
| PR opened | Yes |
| Wiki updated if applicable | Yes |
| Issue updated | Yes |

## Final Output Contract

Return:
- issue ID
- PM answers snapshot
- files changed
- tests added first
- critic score
- PR URL
- wiki page updated or `not needed`
- final issue status
