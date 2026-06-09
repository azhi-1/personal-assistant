# Personal Assistant

长期记忆私人 agent 助手实验区。

当前目标不是先做 RimWorld 桥接，而是先把记忆层打稳：用 MCP 暴露人格化、跨模型、长期记忆能力，再把不同游戏或工具 MCP 接进来。

## Current Direction

- 基底候选：`../nocturne_memory`
- 向量方案参考：`../shujuku`
- 推荐路线：以 Nocturne 的 URI 图谱记忆、namespace、审计回滚、MCP 工具为基础，新增 Qwen embedding/rerank 驱动的语义远记忆召回层。
- 第一阶段交付：一个可被 Codex、Claude Code、Gemini CLI、OpenCode 共用的 memory MCP server。

## Docs

- [Memory Scout Report](docs/memory-scout-report.md)
- [Agent Harness Plan](docs/agent-harness-plan.md)
- [Agent Operating Model](docs/agents/operating-model.md)
- [Breakpoints](docs/agents/breakpoints.md)
- [Tool Entrypoints](docs/agents/tool-entrypoints.md)
- [Nocturne Environment Status](docs/nocturne-env-status.md)
