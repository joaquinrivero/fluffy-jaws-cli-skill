---
name: fluffyjaws
description: Use the fj CLI to query FluffyJaws with minimal context usage. Use when the user wants FluffyJaws answers, compact delegated research, or the MCP server startup command.
---

# fluffyjaws

Use `fj` as an external delegated worker. Keep the main conversation small by passing one precise question to FluffyJaws and returning only the distilled result.

## Setup

Requires VPN (internal Adobe service). Install:
`curl -fsSL https://api.fluffyjaws.adobe.com/api/cli/install.sh | bash`

Auth: `fj login` or set `FJ_SESSION_ID`.

## Commands

- `fj chat --fast "question"` for factual lookups
- `fj chat --thinking "question"` for deeper reasoning
- `fj mcp` to start the MCP stdio server

## Workflow

1. Consolidate the user request into one clear question.
2. Choose `--fast` or `--thinking`.
3. Run `fj chat` once.
4. Summarize the result for the user without pasting long raw output.

## Constraints

- Default to 1 call.
- Never exceed 2 `fj chat` calls unless the user explicitly wants iteration.
- Report auth or transport failures directly.
- If the command fails because of sandboxed networking, retry outside the sandbox when the query is important.

## Reference

Read only when `fj chat` is not enough:

- `references/endpoints.md` — full `/api/v1/*` catalog with schemas
- `references/docs/api.md` — streaming chat API, SSE events, auth flavours
- `references/docs/mcp.md` — MCP client setup (`fj-mcp`, Codex, Cursor)
- `references/docs/fluffypacks.md`, `fluffypack-builder.md` — packs
- `references/docs/python.md` — Python client
- `references/docs/register-app.md`, `slack-channels.md` — app registration, Slack

## Output Contract

Return only:

- the reframed question
- the distilled answer
- confidence if useful
