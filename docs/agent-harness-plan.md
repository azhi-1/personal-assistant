# Agent Harness Plan

## Harness Meaning

In this project, "harness" should mean a repeatable coordination rig around multiple agents, not a single chat window.

The harness should define:

- who scouts
- who builds
- who reviews correctness
- what artifacts each agent must leave behind
- how context is passed without relying on one provider's memory
- how failures escalate

## Available Agents

DeepSeek via OpenCode:

- Best for high-volume scouting, fetch, broad code search, first-pass implementation, and build attempts.
- If build fails, it should switch to scout mode and leave a failure report with exact commands, errors, and suspected files.

Gemini via AGY CLI:

- Best for high-quota broad exploration, alternative solution enumeration, documentation mining, and second scout opinions.

Codex:

- Best for implementation discipline, repo-aware edits, tests, integration shape, and keeping changes scoped.

Claude Code:

- Best for correctness review, sharp design critique, and final high-confidence edits where token budget is worth spending.

Grok:

- Best for web lookup and low-risk external information gathering.

## Artifact Contract

Every agent task should leave one of these artifacts:

- `docs/scouts/YYYY-MM-DD-topic.md`
- `docs/plans/YYYY-MM-DD-topic.md`
- `docs/reviews/YYYY-MM-DD-topic.md`
- `docs/failures/YYYY-MM-DD-topic.md`

Each artifact should include:

- objective
- commands run
- files inspected or changed
- confirmed facts
- hypotheses
- blockers
- next smallest step

This prevents context from being trapped inside one model's chat history.

## Suggested Workflow

1. Scout

   DeepSeek or Gemini reads competitors, docs, APIs, and existing code. It produces a scout report, not production code unless the route is obvious.

2. Build

   DeepSeek may attempt broad implementation when the spec is clear. If it gets stuck, it stops and writes a failure report.

3. Correctness Pass

   Codex reads the report, checks architecture, writes scoped code, and adds tests.

4. Review

   Claude reviews changed behavior, edge cases, persistence semantics, and test gaps.

5. Stabilize

   Codex applies final fixes and records validation commands.

## First Project Milestones

Milestone 0: Memory architecture decision

- Confirm Nocturne-based or fresh implementation.
- Decide vector provider.
- Decide namespace/domain policy.

Milestone 1: Minimal memory MCP

- boot memory
- read/create/update/delete
- lexical search
- semantic search
- recall context builder

Milestone 2: Consolidation

- near memory capture
- remote summary generation
- embedding persistence
- recall scoring
- review/rollback

Milestone 3: Agent personality

- persona boot memories
- user preference memories
- memory write policy
- conflict resolution workflow

Milestone 4: RimWorld bridge

- connect mature RimWorld MCP
- isolate game state from personal identity memory
- add game-specific recall package

## Default Directory Layout

```text
personal-assistant/
  README.md
  docs/
    memory-scout-report.md
    agent-harness-plan.md
    scouts/
    plans/
    reviews/
    failures/
  src/
  tests/
```

## Rules For Agent Handoffs

Do not hand off vague state like "look at the vector stuff".

Use concrete anchors:

- file paths
- function names
- command outputs
- API payload examples
- failing test names
- exact TODO boundary

For build failures, include:

- command
- cwd
- exit code
- important stderr
- whether dependencies were installed
- whether network was needed

