# Memory Scout Report

## Objective

Prove whether `../nocturne_memory` can be used as the memory base for `personal-assistant`, whether a vector layer is necessary, and which parts of `../shujuku` should inform the design.

## Verdict

Clear with constraints.

Use `nocturne_memory` as the structural memory base, but do not treat it as the finished long-context solution. It is mature around graph memory, MCP exposure, full-text search, namespaces, presets, and review/rollback. It does not currently implement semantic vector retrieval. For the desired "low token, does not forget" private assistant, a vector layer is necessary.

## Confirmed Facts

`nocturne_memory` has a real MCP server at `../nocturne_memory/backend/mcp_server.py`.

Its exposed memory tools include:

- `read_memory(uri)`
- `create_memory(parent_uri, content, priority, disclosure, title?)`
- `update_memory(uri, old_string?, new_string?, append?, priority?, disclosure?)`
- `delete_memory(uri)`
- `search_memory(query, domain?, limit?)`

`read_memory("system://boot")` loads boot memories from active presets. This is directly useful for persona bootstrapping.

The data model in `../nocturne_memory/backend/db/models.py` is graph-shaped:

- `Node`: stable conceptual identity
- `Memory`: versioned content
- `Edge`: parent-child relation with `priority` and `disclosure`
- `Path`: materialized URI route with `namespace`
- `GlossaryKeyword`: keyword-to-node trigger binding
- `SearchDocument`: derived full-text search row
- `MemoryAccessLog`: read frequency and sequence tracking
- `Preset`: active boot URI set

`search_memory` explicitly says it is lexical full-text search, not semantic search. The backing implementation is SQLite FTS5 or PostgreSQL tsvector in `../nocturne_memory/backend/db/search.py`, with BM25-like ranking in SQLite.

`nocturne_memory` has tests under `../nocturne_memory/backend/tests`, a `pytest.ini`, Docker deployment files, frontend admin UI, migrations, and rollback/review APIs. That is a strong maturity signal.

The local environment did not have `pytest`, so tests were not executed in this pass.

`shujuku` has an implemented vector-crossfire path. Relevant anchors:

- `../shujuku/src/service/vector/summary-vector-index-runtime.ts`
- `../shujuku/src/service/vector/vector-memory-config.ts`
- `../shujuku/src/data/gateways/vector-embedding-gateway.ts`
- `../shujuku/src/data/gateways/vector-rerank-gateway.ts`
- `../shujuku/src/service/worldbook/injection-engine-custom.ts`
- `../shujuku/docs/向量交火-纪要索引-功能汇总.md`

The current `shujuku` mechanism is:

- generate query keywords from recent context and current user input
- embed `userInput + keywords`
- cosine-search older summary chunks
- optionally rerank candidates
- force include recent fixed rows
- overwrite the SillyTavern worldbook "纪要索引" entry with a compact Markdown table
- protect that entry from ordinary full-table export once the vector threshold is met

The later `shujuku` plan at `../shujuku/plans/remote_memory_vector_rearchitecture_plan.md` points toward a better long-term architecture:

- near memory remains raw and recent
- remote memory is generated as settled long-term summaries
- only remote summaries are vectorized
- recall returns entire remote summaries, not individual line chunks
- original near-memory rows are deleted only after summary generation, embedding, and state write all succeed

## Recommendation

Do not clone `shujuku`'s SillyTavern-specific implementation into the MCP service.

Adopt the idea, not the host binding:

- Nocturne handles durable structured memory and MCP tools.
- A new vector subsystem handles semantic retrieval and token-bound recall.
- The recall result should be MCP-readable memory context, not a SillyTavern worldbook overwrite.

The best first architecture is a hybrid:

- Boot memory: explicit persona/core URIs from Nocturne presets.
- Working memory: recent dialogue/session notes, kept small.
- Near episodic memory: raw recent events not yet consolidated.
- Remote semantic memory: settled summaries embedded with `Qwen/Qwen3-Embedding` and optionally reranked by `Qwen/Qwen3-Reranker-8B`.
- Recall package: compact, scored, source-linked context returned to the agent on demand.

## Hazards

`nocturne_memory` README Chinese text displayed mojibake under the current terminal encoding. Source files are still readable enough; use UTF-8 aware reads when extracting Chinese docs.

`nocturne_memory` currently auto-builds frontend on first MCP run unless `SKIP_FRONTEND_BUILD` is set. Formal integration should control this for CLI clients.

`shujuku` vector code assumes SillyTavern APIs, worldbook entries, chat layers, and ST files. These must be replaced, not wrapped blindly.

Qwen embedding/rerank endpoints are external dependencies. The MCP must support rate limits, batching, retry, checksum/idempotency, and degraded lexical fallback.

Memory writes are high-risk because bad memory becomes persistent behavior. Keep Nocturne's disclosure, priority, review, and rollback semantics.

## Implementation Plan

1. Fork or vendor `nocturne_memory` into this repo as the initial MCP memory server.
2. Add a vector module with provider abstraction:
   - `embed(texts[])`
   - `rerank(query, docs[])`
   - SiliconFlow/OpenAI-compatible HTTP payloads first.
3. Add tables for semantic memory:
   - `memory_embeddings`
   - `memory_chunks`
   - `remote_memory_batches`
   - `recall_events`
4. Implement `semantic_search_memory(query, domain?, limit?, min_score?)`.
5. Implement `build_recall_context(user_input, recent_context?)`.
6. Keep `search_memory` as lexical fallback and debug tool.
7. Add a consolidation path:
   - collect near events
   - summarize settled facts
   - embed summary chunks
   - write remote batch
   - mark source events archived only after all writes succeed
8. Expose a minimal admin/CLI harness before building game bridges.

## Validation Hooks

Run after dependencies are installed:

```powershell
python -m pytest ../nocturne_memory/backend/tests -q
npm --prefix ../shujuku install
npm --prefix ../shujuku run test
```

For this repo's future MCP:

```powershell
python -m pytest tests -q
python -m personal_assistant_memory smoke
```

## Open Questions

- Should `personal-assistant` fork `nocturne_memory`, depend on it as a submodule, or reimplement a narrower subset?
- Which provider will host Qwen models first: SiliconFlow, local vLLM, DashScope, or another OpenAI-compatible endpoint?
- Should agent personality memory and game memory share one namespace with domains, or use separate namespaces?

