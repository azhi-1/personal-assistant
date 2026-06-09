# Agent Operating Model

## Decision Hierarchy

Codex and Claude are the top-tier execution, review, and decision agents.

DeepSeek and Gemini maximize exploration bandwidth.

Grok handles low-risk web scouting and environment notes.

## Role Table

| Agent | Primary Role | Allowed To Build | Must Handoff When |
|---|---|---:|---|
| Codex | scoped implementation, tests, integration decisions | yes | task is complete or blocked by external input |
| Claude Code | correctness review, persistence review, final critique | yes, selectively | review is complete or blockers are explicit |
| DeepSeek/OpenCode | rough builder, scout, fetcher | yes, rough only | build fails or assumptions become unclear |
| Gemini/AGY | broad scout, comparison, planning | limited | report is decision-ready |
| Grok | web scout, basic setup helper | no core architecture | facts are collected |

## Rough Build To Scout

Rough build is useful because a failed build still reveals dependency edges, hidden assumptions, and missing contracts.

The rule is:

1. Try the smallest plausible vertical slice.
2. If it works, leave evidence and let Codex/Claude harden it.
3. If it fails, stop expanding the implementation.
4. Write a scout or failure report with exact evidence.

## Artifact Names

Use date-prefixed names:

- `docs/scouts/2026-06-08-nocturne-vector-route.md`
- `docs/plans/2026-06-08-memory-mcp-milestone-1.md`
- `docs/reviews/2026-06-08-vector-schema-review.md`
- `docs/failures/2026-06-08-nocturne-smoke-failure.md`

## Required Handoff Fields

Every artifact should include:

- objective
- scope
- commands run
- files inspected
- confirmed facts
- hypotheses
- decisions or recommendations
- blockers
- next smallest step

## Breakpoint Rule

If a report creates a durable technical fact, update `docs/agents/breakpoints.md`.

Examples:

- "Nocturne remains the base."
- "Semantic recall must use remote summaries, not raw row chunks."
- "This migration must never delete source events before embedding succeeds."
- "RimWorld memory must be namespace/domain isolated from assistant identity."

