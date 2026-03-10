---
name: fluffyjaws
description: Sends one-shot chat messages or starts an MCP server via the fj CLI. Use when the user wants to chat with FluffyJaws AI, query it with a question, or configure it as an MCP server for Cursor, Codex, or Claude.
---

# fluffyjaws

AI chat and MCP server via the `fj` command.

**Auth:** `fj login` or set `FJ_SESSION_ID`.

## Commands

- `fj chat "question"` - one-shot chat query
- `fj mcp` - start MCP stdio server

## Key Options

- `--model <name>` - fast-mode model
- `--reasoning fast|thinking` - reasoning mode
- `--thinking` / `--fast` - shorthand reasoning flags
- `--api <url>` - API host override
- `--session <id>` - override session cookie

## Working Rules

1. Prefer one consolidated `fj chat` call.
2. Ask FluffyJaws for a concise answer so only the distilled result comes back.
3. Use `--fast` for factual lookups and `--thinking` for multi-step reasoning.
4. If `fj chat` fails because of auth, timeout, or connectivity, report that directly.
5. If sandboxed networking causes a false connectivity failure, retry the same command outside the sandbox before blaming VPN access.

Run `fj --help` for full reference.
