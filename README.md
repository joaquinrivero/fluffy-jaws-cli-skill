# fluffy-jaws-cli-skill

A runtime-agnostic skill for Adobe FluffyJaws. Wraps the `fj` CLI so an agent can
query FluffyJaws or start its MCP server without pulling raw output into the main
conversation.

```
SKILL.md      # the skill
references/   # API, MCP, FluffyPacks, Python docs
```

## Install

Copy this directory into whatever skills directory your runtime reads:

```bash
git clone git@github.com:joaquinrivero/fluffy-jaws-cli-skill.git ~/.../skills/fluffyjaws
```

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

Other flags: `--model <name>`, `--fluffypack-slug`, `--session <id>`,
`--api <url>`. Full list: `fj --help`.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Auth error | `fj login`, or export `FJ_SESSION_ID` |
| Connectivity / timeout | Check VPN; if the runtime sandboxes network, retry outside the sandbox |
| `fj` not found | Reinstall; confirm `/usr/local/bin` is on `$PATH` |
