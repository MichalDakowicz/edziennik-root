---
name: create-ticket-pm
description: >
  Interactive engineering ticket generator with a PM discovery phase. Asks 2-3
  scoping questions first, then produces a GitHub-ready markdown ticket with
  wiki references, TDD blueprint, critic gate, and Fibonacci story points.
---

# Skill: create-ticket-pm

## Trigger

Match only:

`^create-ticket-pm(?:\s+(.+))?$`

Captured value:
- optional `RAW_DESCRIPTION`

## Objective

Ask targeted PM discovery questions before drafting the ticket, then generate the same output structure as `create-ticket.md` with the answers embedded into scope, acceptance criteria, and TDD planning.

## Discovery Rules

| Rule | Requirement |
|---|---|
| Question count | Ask `2` or `3` questions only |
| Scope | Questions must uncover hidden requirements, audience, or constraints |
| Precision | Each question must influence the final ticket |
| No waste | Do not ask what the repo or description already makes obvious |

## PM Discovery Phase

Ask `2-3` questions selected from:
- primary user or audience
- success outcome
- non-obvious constraint
- edge-case priority
- delivery tradeoff
- compatibility expectation

Algorithm:
1. if input is vague, ask `3`
2. if input is moderately specific, ask `2`
3. wait for answers
4. convert answers into explicit ticket constraints

## Post-Discovery Research

After answers:
1. scan `docs/wiki/` for related patterns or old bugs
2. inspect repo structure as needed
3. identify likely impact zones
4. integrate discovery answers into the final ticket

## Required Output Structure

Use the same sections and order as `create-ticket.md`:
1. Title
2. Summary
3. User Story
4. Technical Hints & Wiki References
5. Acceptance Criteria
6. Steps to Reproduce or Verification Steps
7. TDD Blueprint
8. Definition of Done
9. Story Points

## Mandatory Enhancements Over create-ticket

The generated ticket must explicitly encode:
- PM answers as scope constraints
- audience-specific acceptance criteria
- edge cases surfaced during discovery
- architectural or delivery preferences provided by the user

## Internal Virtual Roles

| Role | Responsibility |
|---|---|
| `Tester/Architect` | Turns discovery answers into pre-implementation tests |
| `Coder` | Ensures ticket is buildable and not over-scoped |
| `Critic (QA & Security)` | Checks completeness, ambiguity, and future regression risk |

Use the same formula:

`Score = (Requirements Met + TDD Test Coverage + Code Cleanliness) / 3`

If estimated score is `< 90`:
- refine the ticket before returning it
- tighten acceptance criteria
- improve TDD blueprint
- add missing wiki references or risk notes

## Story Point Rule

Assign exactly one Fibonacci value:
`1, 2, 3, 5, 8, 13, 21`

Add exactly one sentence of justification.

## Output Contract

Two-phase behavior:
1. first response: only the `2-3` PM discovery questions
2. after answers: return the finalized markdown ticket only
