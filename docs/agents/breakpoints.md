# Breakpoints

Breakpoints are the project's durable technical memory.

They record construction-critical facts that future agents must not rediscover, contradict, or accidentally undo after a session switch, model switch, or handoff.

## Definition

A breakpoint is a stable checkpoint in the project knowledge graph:

- a decision that changes future implementation
- a confirmed technical fact
- a dangerous constraint
- an architecture boundary
- a failure that should not be repeated
- an environment fact required to reproduce work
- a compatibility rule between Nocturne, shujuku, MCP clients, or future game bridges

Breakpoints are not general notes. They are the facts that prevent agents from fighting the project or each other.

## Read Order

Every serious agent session should read these before acting:

1. `AGENTS.md`
2. `docs/agents/operating-model.md`
3. `docs/agents/breakpoints.md`
4. the current task artifact under `docs/scouts/`, `docs/plans/`, `docs/reviews/`, or `docs/failures/`

## Breakpoint Format

Each breakpoint must include:

- ID: stable identifier
- Status: `active`, `superseded`, or `rejected`
- Date:
- Owner:
- Decision / Fact:
- Evidence:
- Consequence:
- Do Not:
- Supersedes:

## Active Breakpoints

### BP-0001: Memory First, Game Bridges Later

- Status: active
- Date: 2026-06-08
- Owner: Codex
- Decision / Fact: The first deliverable is a stable long-term memory MCP layer. RimWorld MCP integration comes after memory recall, write policy, and consolidation are stable.
- Evidence: User stated the first game will be RimWorld, but mentioned it as bigger-picture compatibility context. The immediate goal is memory retention with low token usage.
- Consequence: Do not begin RimWorld bridge implementation before the memory system has a validated MCP surface.
- Do Not: Do not let game integration decisions pollute core assistant identity memory.
- Supersedes: none

### BP-0002: Nocturne Is The Structural Base Candidate, Not The Complete Solution

- Status: active
- Date: 2026-06-08
- Owner: Codex
- Decision / Fact: `../nocturne_memory` is mature enough to be the likely structural base because it has MCP tools, URI graph memory, namespace isolation, boot presets, lexical search, review, and rollback.
- Evidence: `../nocturne_memory/backend/mcp_server.py`, `../nocturne_memory/backend/db/models.py`, `../nocturne_memory/backend/db/search.py`, and existing tests/migrations/admin UI.
- Consequence: Prefer extending or adapting Nocturne before building a memory server from scratch.
- Do Not: Do not assume Nocturne already solves semantic recall.
- Supersedes: none

### BP-0003: Vector Recall Is Required For The Target Outcome

- Status: active
- Date: 2026-06-08
- Owner: Codex
- Decision / Fact: Lexical search alone is insufficient for "low token, does not forget". The assistant needs semantic vector recall using Qwen embedding and optional rerank.
- Evidence: Nocturne `search_memory` says it is lexical full-text search, not semantic search. shujuku's vector-crossfire mechanism demonstrates bounded recall by selecting relevant memory subsets.
- Consequence: The project must add a vector subsystem with embedding, similarity search, optional rerank, and compact recall package generation.
- Do Not: Do not present FTS/BM25 as equivalent to semantic memory.
- Supersedes: none

### BP-0004: Borrow shujuku's Idea, Not Its Host Binding

- Status: active
- Date: 2026-06-08
- Owner: Codex
- Decision / Fact: shujuku's vector-crossfire design is useful, but its implementation is bound to SillyTavern worldbooks, chat layers, ST files, and table exports.
- Evidence: Relevant files include `../shujuku/src/service/vector/summary-vector-index-runtime.ts`, `../shujuku/src/service/worldbook/injection-engine-custom.ts`, and `../shujuku/docs/向量交火-纪要索引-功能汇总.md`.
- Consequence: The MCP version should expose recall through MCP tools and local memory storage, not worldbook overwrites.
- Do Not: Do not copy ST-specific persistence mechanics into the core MCP service.
- Supersedes: none

### BP-0005: Prefer Near Memory Plus Remote Summary Memory

- Status: active
- Date: 2026-06-08
- Owner: Codex
- Decision / Fact: The better long-term architecture is near raw memory plus remote settled summary memory. Recent facts remain near memory; older settled facts are summarized, embedded, and recalled semantically.
- Evidence: `../shujuku/plans/remote_memory_vector_rearchitecture_plan.md` independently identifies this as a cleaner architecture than row-level chunk recall.
- Consequence: Design the vector schema around remote memory batches/summaries, not only raw row chunks.
- Do Not: Do not delete source near memory until summary generation, embedding, and state write have all succeeded.
- Supersedes: none

### BP-0006: Codex And Claude Are Top-Tier Decision Agents

- Status: active
- Date: 2026-06-08
- Owner: Codex
- Decision / Fact: Codex and Claude Code are the primary implementation/review/decision agents. DeepSeek and Gemini are high-volume scouts/builders; Grok is a lightweight web scout.
- Evidence: User explicitly stated Codex and Claude are top-tier executors, reviewers, and decision makers.
- Consequence: Rough builder output must be hardened or reviewed by Codex/Claude before becoming architectural baseline.
- Do Not: Do not let rough builder output become final merely because it compiles.
- Supersedes: none

### BP-0007: Rough Build Failure Must Become Scout Evidence

- Status: active
- Date: 2026-06-08
- Owner: Codex
- Decision / Fact: DeepSeek/OpenCode may rough build. If build fails or the route becomes unclear, it must switch to scout mode and write a report.
- Evidence: User defined the rough building -> scout workflow. Project docs encode it in `AGENTS.md` and `OPENCODE.md`.
- Consequence: Failed build attempts should create `docs/failures/YYYY-MM-DD-topic.md` or `docs/scouts/YYYY-MM-DD-topic.md`.
- Do Not: Do not keep retrying blindly after repeated failures.
- Supersedes: none

### BP-0008: Nocturne Environment Has Been Bootstrapped

- Status: active
- Date: 2026-06-08
- Owner: Grok, verified by Codex
- Decision / Fact: `../nocturne_memory` has a local `.venv`, installed backend requirements, built frontend `dist`, generated `config.json`, and migrated `demo.db` to `nocturne_data.db`.
- Evidence: User supplied Grok setup summary. Codex verified imports with `..\nocturne_memory\.venv\Scripts\python.exe -c "... import mcp_server, run_sse, web_app ..."`, output `nocturne-import-ok`.
- Consequence: Future agents should use the existing venv and smoke-check it before reinstalling dependencies.
- Do Not: Do not overwrite `nocturne_data.db` or assume `demo.db` is the live writable database.
- Supersedes: none

### BP-0009: Root Instruction Files Are The Harness Entrypoints

- Status: active
- Date: 2026-06-08
- Owner: Codex
- Decision / Fact: The harness uses root instruction files plus `docs/agents/`, not hidden directories as the primary rule source.
- Evidence: `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `OPENCODE.md`, and `docs/agents/tool-entrypoints.md` now exist. Hidden folders may not be automatically loaded across tools.
- Consequence: Each new agent session should explicitly read its root instruction file and `docs/agents/breakpoints.md`.
- Do Not: Do not rely on `.codex/`, `.claude/`, `.gemini/`, or `.deepseek/` until a tool-specific loader is confirmed.
- Supersedes: none

## Adding A Breakpoint

Add a breakpoint when a future agent would waste time or cause damage if it missed the fact.

Keep it short. Link the detailed report elsewhere.

