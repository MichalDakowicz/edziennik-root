---
name: create-ticket
description: >
  Autonomous engineering ticket generator. Converts raw feature or bug
  descriptions into structured GitHub-ready markdown tickets with TDD blueprint,
  wiki references, critic gate, acceptance criteria, and Fibonacci story points.
---

# Skill: create-ticket

## Trigger

Match only:

`^create-ticket\s+(.+)$`

Captured value:
- `RAW_DESCRIPTION`

## Objective

Turn raw text into an implementation-ready markdown ticket that:
- is clear enough for autonomous execution
- references relevant wiki knowledge
- encodes TDD-first delivery
- includes critic-based Definition of Done
- assigns exactly one Fibonacci story point value
- is intended to be created immediately in GitHub Project `https://github.com/users/MichalDakowicz/projects/2`
- maps the chosen Fibonacci story point value into the GitHub Project `Estimate` field

## Pre-Generation Research

Before drafting:
1. scan `docs/wiki/` for related bugs, patterns, or architecture notes
2. inspect repo structure only as needed to identify likely modules
3. classify request as:
   - feature
   - bug
   - refactor
   - hardening
   - docs with code impact
4. prepare the ticket so it can be created immediately as a GitHub issue and added to GitHub Project `https://github.com/users/MichalDakowicz/projects/2`

## Required Ticket Structure

Produce exactly these sections in this order.

### Title
Short, specific, action-oriented.

### Summary
1-3 lines describing the problem or desired capability.

### User Story
Use exact format:

`As a <role>, I want <capability>, so that <outcome>.`

### Technical Hints & Wiki References
Include:
- likely subsystems or file areas
- known patterns to follow
- relevant `docs/wiki/...` pages
- known risks or regression zones

If no wiki page exists:
- state `No direct wiki match found`
- suggest a future wiki page only if recurrence risk is meaningful

### Acceptance Criteria
Use a flat checklist of observable outcomes.

### Steps to Reproduce / Verification Steps
- for bugs: `Steps to Reproduce`
- for non-bugs: `Verification Steps`

### TDD Blueprint
Must define tests to write before implementation:
- unit tests
- integration tests
- regression tests
- edge-case tests

For each test cluster, state:
- purpose
- target behavior
- expected RED condition

### Definition of Done
Must include:
- all acceptance criteria pass
- tests were written first
- RED -> GREEN -> REFACTOR completed
- critic score `>= 90`
- wiki updated if reusable fix/pattern was introduced
- lint/type/test pipeline green
- issue created and added to GitHub Project `https://github.com/users/MichalDakowicz/projects/2`

### Story Points
Assign exactly one from:
`1, 2, 3, 5, 8, 13, 21`

Add one sentence of justification.

When the issue is created in GitHub Project `https://github.com/users/MichalDakowicz/projects/2`, the selected story point value must also be written into the project's `Estimate` field.

## Internal Quality Rules

Internally simulate:

| Role | Responsibility |
|---|---|
| `Tester/Architect` | Ensures TDD blueprint is concrete and front-loaded |
| `Coder` | Checks ticket is implementable with minimal ambiguity |
| `Critic (QA & Security)` | Ensures completeness, risk coverage, and wiki awareness |

Use the same formula:

`Score = (Requirements Met + TDD Test Coverage + Code Cleanliness) / 3`

For ticket generation:
- `Code Cleanliness` means clarity and maintainability of the implementation path
- if internal estimate is `< 90`, improve the ticket before returning it

## Story Point Heuristic

| Points | Use When |
|---|---|
| `1` | Tiny copy/config/test-only adjustment |
| `2` | Small isolated change |
| `3` | Small-medium feature or bug with limited risk |
| `5` | Multi-file change with moderate edge cases |
| `8` | Cross-layer work or meaningful regression risk |
| `13` | Broad feature with several unknowns |
| `21` | Too large; use only when decomposition is clearly needed |

## Output Contract

Return markdown only, ready to paste into GitHub, with this order:
1. Title
2. Summary
3. User Story
4. Technical Hints & Wiki References
5. Acceptance Criteria
6. Steps to Reproduce or Verification Steps
7. TDD Blueprint
8. Definition of Done
9. Story Points

The generated ticket must be written as an issue body suitable for immediate placement into GitHub Project `https://github.com/users/MichalDakowicz/projects/2`.
