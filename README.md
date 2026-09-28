# fluffy-jaws-cli-skill

Portable FluffyJaws skill for agent runtimes. Wraps the `fj` CLI so an assistant
can query FluffyJaws or start its MCP server without pulling raw output into the
main conversation context.

```
.codex/
├── AGENTS.md                      # skill + agent registry
├── agents/fluffyjaws-agent.md     # delegated-query prompt contract
└── skills/fluffyjaws/
    ├── SKILL.md
    └── references/                # API, MCP, FluffyPacks, Python docs
```

The skill file is runtime-agnostic. Copy `.codex/skills/fluffyjaws/` into
whatever skills directory your runtime reads.

## Prerequisites

- VPN connected (FluffyJaws is an internal Adobe service)
- `curl -fsSL https://api.fluffyjaws.adobe.com/api/cli/install.sh | bash`
- `fj login`, or set `FJ_SESSION_ID`

## Direct CLI patterns

```bash
fj chat --fast "Summarize in 3 bullets: <question>"      # factual lookup
fj chat --thinking "Answer concisely: <question>"        # deeper reasoning
fj mcp                                                   # MCP stdio server
```

Other flags: `--model <name>`, `--pack`/`--fluffypack-slug`, `--session <id>`,
`--api <url>`. Full list: `fj --help`.

## Agent response format

```
Question: <reframed question>
Answer: <distilled answer>
Confidence: high | medium | low
```

Raw CLI output is never included unless explicitly requested.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Auth error | `fj login`, or export `FJ_SESSION_ID` |
| Connectivity / timeout | Check VPN; if the runtime sandboxes network, retry outside the sandbox |
| `fj` not found | Reinstall; confirm `/usr/local/bin` is on `$PATH` |
