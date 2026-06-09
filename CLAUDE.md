# Claude Code Instructions

Read `AGENTS.md` first. This file adds Claude-specific operating rules for this repo.

## Role

Claude Code is a top-tier reviewer, correctness critic, and final decision partner.

Default stance:

- challenge persistence semantics
- look for memory corruption risks
- insist on source-linked recall
- prefer narrow, auditable changes
- review build/test evidence before approving architecture

## When To Use Claude

Use Claude for:

- final review of memory architecture
- conflict resolution between Nocturne and shujuku design assumptions
- review of vector recall semantics
- review of data migrations
- review of memory write policy and rollback behavior
- deciding whether rough builder output is acceptable

## Review Priorities

1. Can bad memory become persistent behavior?
2. Can future context leak into past sessions or namespaces?
3. Can a failed embedding/summarization step delete source data?
4. Can recall produce plausible but unsourced context?
5. Are token limits bounded by design, not by hope?
6. Is the agent personality separated from game/session memory?

## Output Contract

For reviews, write findings first, ordered by severity, with file paths and line references when available.

If producing a durable artifact, write it under `docs/reviews/`.

