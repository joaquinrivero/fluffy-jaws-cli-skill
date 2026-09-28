# FluffyJaws

## Skills

### Available skills

- `fluffyjaws`: Query FluffyJaws through the `fj` CLI for compact delegated research or to provide the MCP server startup command. File: `./.codex/skills/fluffyjaws/SKILL.md`

## How To Use Skills

- Trigger rule: Use `fluffyjaws` when the user wants an answer from FluffyJaws, a compact external lookup through `fj`, the `fj mcp` startup command, or delegated research that should stay out of the main coding context.
- Discovery rule: Load only the skill file you need. Do not read unrelated files by default.
- Runtime note: the agent file is a prompt contract, not a native subagent.

## Agents

### Available agents

- `fluffyjaws-agent`: Prompt contract for delegated FluffyJaws queries. Use when you want to keep the FluffyJaws step separate from the main coding context. File: `./.codex/agents/fluffyjaws-agent.md`

> Note: a prompt contract, not a native subagent. The runtime follows it by convention, not by enforced isolation.

## Runtime Model

The isolation boundary is the `fj` process:

1. Load `./.codex/skills/fluffyjaws/SKILL.md`.
2. Run `fj chat` with one consolidated question.
3. Keep the raw output out of the main reply.
4. Return only the distilled answer.

If the user asks for the MCP server command, provide `fj mcp`.

If `fj` fails with connectivity errors and the query matters, retry outside the sandbox before assuming VPN access is broken.
