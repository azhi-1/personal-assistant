# Tool Entrypoints

## Recommendation

Use root instruction files as the primary entrypoints:

- Codex: `AGENTS.md`
- Claude Code: `CLAUDE.md` plus `AGENTS.md`
- Gemini / AGY: `GEMINI.md` plus `AGENTS.md`
- OpenCode / DeepSeek: `OPENCODE.md` plus `AGENTS.md`

Do not rely on hidden folders like `.codex/`, `.claude/`, `.gemini/`, or `.deepseek/` as the primary rule system unless that specific tool is known to load them automatically.

Hidden folders can be added later for tool-specific settings or commands, but project policy should remain visible in root docs.

## Why

This project needs cross-agent consistency. A single visible instruction layer prevents role drift:

- `AGENTS.md` defines shared strategy and role hierarchy.
- tool-specific files define the agent's expected behavior.
- `docs/agents/` holds reusable templates and operating rules.

## Prompt Loading Fallback

If an agent does not auto-load its file, start the session by pasting:

```text
Read AGENTS.md and your tool-specific instruction file before acting.
Follow docs/agents/operating-model.md.
Read docs/agents/breakpoints.md before architectural or persistence work.
Leave scout/failure/review artifacts when you discover information future agents need.
```

Then add the matching role prompt from `docs/agents/prompts/`.
