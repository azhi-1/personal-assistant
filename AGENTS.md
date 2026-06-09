# Personal Assistant Agent Instructions

This repo is building a long-term-memory private assistant centered on MCP memory. The first priority is the memory layer; RimWorld and other MCP integrations come after the assistant can remember reliably with bounded token usage.

## Workspace

- Primary repo: `C:\Users\pc\Desktop\workplace\personal-assistant`
- Competitor/reference repos:
  - `C:\Users\pc\Desktop\workplace\nocturne_memory`
  - `C:\Users\pc\Desktop\workplace\shujuku`
- Allowed wider workspace: `C:\Users\pc\Desktop\workplace`

Do not rewrite or delete competitor repos unless explicitly asked. Treat them as read-mostly references.

## Strategic Direction

Use `nocturne_memory` as the likely structural memory base, because it already has MCP tools, URI graph memory, namespace isolation, boot presets, lexical search, review, and rollback.

Do not assume Nocturne is complete for this project. Its current search path is lexical full-text search, not semantic vector search. Add a vector memory layer inspired by `shujuku`, but do not copy SillyTavern-specific worldbook/chat-layer mechanics into the MCP design.

Preferred memory shape:

- boot memory: persona and core user/assistant identity
- working memory: small current session state
- near episodic memory: recent raw events, not yet consolidated
- remote semantic memory: settled summaries embedded with Qwen embedding and optionally reranked
- recall package: compact, source-linked context returned through MCP

## Agent Roles

Codex is a top-tier implementer and integration decision maker. Codex should make scoped code changes, add tests, validate behavior, and keep the architecture coherent.

Claude Code is a top-tier reviewer, correctness critic, and final decision partner. Claude should be used where semantic correctness, persistence risks, and design quality matter more than volume.

DeepSeek/OpenCode is the rough builder and high-volume scout. It may attempt broad implementation quickly. If the build fails or the path becomes unclear, it must stop rough building and produce a scout or failure report instead of continuing to churn.

Gemini/AGY is a high-volume scout and option enumerator. It should compare approaches, read docs, fetch external references, and produce decision-ready reports.

Grok is a lightweight web scout. It can collect external facts, setup notes, and basic compatibility data, but should not be the final authority on core architecture.

## Rough Build To Scout Rule

Rough building is allowed only when the implementation path is obvious enough to try quickly.

If rough building hits any of these conditions, switch to scout mode:

- dependency or build failure not resolved in one short attempt
- unclear API contract
- unclear persistence semantics
- hidden host/runtime dependency
- repeated type/test failures without a clear fix
- conflict between Nocturne and shujuku assumptions

Scout output must go under `docs/scouts/` or `docs/failures/` and include exact commands, files inspected, confirmed facts, hypotheses, blockers, and the next smallest step.

## Artifact Contract

Use these directories for handoff artifacts:

- `docs/scouts/`
- `docs/plans/`
- `docs/reviews/`
- `docs/failures/`

Every non-trivial agent run should leave a durable artifact when it discovers information that future agents need.

Use templates in `docs/agents/`.

## Breakpoints

Read `docs/agents/breakpoints.md` before making architectural or persistence changes.

Breakpoints are durable construction-critical memory. They record decisions, confirmed facts, and constraints that future agents must not contradict after a session switch or handoff.

Add or update a breakpoint when a new fact would prevent future agents from rebuilding the wrong thing, repeating a failure, deleting source memory too early, or mixing assistant identity with game/session memory.

## Current Known Environment

Grok configured `../nocturne_memory`:

- created isolated `.venv` with Python 3.14.3
- installed `backend/requirements.txt`
- ran frontend `npm install` and Vite build; `frontend/dist` is ready
- triggered first startup logic
- generated `config.json`
- copied `demo.db` to `nocturne_data.db`
- verified core imports: `mcp_server`, `run_sse`, `web_app`

Before assuming the environment still works, run a lightweight smoke check and record results.

## Development Rules

- Read local context before editing.
- Prefer small, reversible changes.
- Do not copy SillyTavern-specific storage or worldbook mechanics into the MCP service unless the design explicitly calls for a bridge adapter.
- Keep memory writes reviewable and source-linked.
- Add tests for persistence, recall ranking, rollback, and degraded fallback paths.
- Do not put secrets or API keys in repo files.

## First Milestones

1. Confirm whether to fork/vendor Nocturne or build a narrower MCP from scratch.
2. Add semantic vector provider abstraction.
3. Add semantic memory tables and migrations.
4. Expose lexical and semantic recall MCP tools.
5. Add consolidation from near memory to remote summaries.
6. Bridge RimWorld only after memory behavior is stable.
