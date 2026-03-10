# fluffy-jaws-cli-skill

Portable FluffyJaws skill and agent definitions for Claude Code and Codex runtimes. Wraps the `fj` CLI so AI assistants can query FluffyJaws or start its MCP server without polluting the main conversation context.

## What's inside

```
.
├── .claude/
│   ├── skills/fluffyjaws/SKILL.md    # Claude Code skill
│   └── agents/fluffyjaws-agent.md    # Claude Code subagent (model: haiku)
│
└── .codex/
    ├── AGENTS.md                     # Codex skill + agent registry
    ├── agents/fluffyjaws-agent.md    # Codex agent prompt contract
    └── skills/fluffyjaws/SKILL.md    # Codex skill
```

## How it works

Both runtimes expose the same two primitives:

| Primitive | Purpose |
|-----------|---------|
| **Skill** | Instructions for calling `fj chat` or `fj mcp` from within the AI session |
| **Agent** | Runs the query in an isolated subprocess so raw output never enters the parent context |

The isolation boundary is the `fj` process itself — the agent runs one `fj chat` call, distills the answer, and returns only a short structured response.

## Prerequisites

- [`fj` CLI](https://fj.adobe.com) installed and on `$PATH`
- Authenticated: run `fj login` or set the `FJ_SESSION_ID` environment variable
- VPN connected (FluffyJaws is an internal Adobe service)

## Usage

### Claude Code

The `fluffyjaws` skill is auto-loaded. Trigger it naturally:

> "Ask FluffyJaws about AEM Cloud Manager pipeline failures"

Or invoke it directly:

```
/fluffyjaws What causes AEM Cloud Manager build step failures?
```

The `fluffyjaws-agent` subagent runs with `haiku` model to keep costs low and context clean.

### Codex

Codex uses `.codex/AGENTS.md` to register the skill. Mention FluffyJaws in your prompt and Codex will follow the skill instructions to run `fj chat` and return a distilled answer.

### Direct CLI patterns

```bash
# Fast factual lookup
fj chat --fast "Summarize in 3 bullets: <question>"

# Deep reasoning
fj chat --thinking "Answer concisely with uncertainty: <question>"

# Start MCP server (for Cursor, Claude Desktop, etc.)
fj mcp
```

## Agent response format

The agent always returns:

```
Question: <reframed question>
Answer:   <distilled answer>
Confidence: High | Medium | Low
```

Raw CLI output is never included unless explicitly requested.

## Key options

| Flag | Description |
|------|-------------|
| `--fast` | Faster, lighter model — good for factual lookups |
| `--thinking` | Deeper reasoning mode |
| `--model <name>` | Use a specific model by name |
| `--session <id>` | Override the session cookie |
| `--api <url>` | Override the API host |

## Runtime comparison

The same skill and agent pattern is implemented in both runtimes, but their capabilities differ significantly.

| Capability | Claude Code | Codex |
|---|---|---|
| **Skill loading** | Native — auto-loaded from `.claude/skills/` | Convention — read from `.codex/AGENTS.md` |
| **Agent execution** | Native subagent, runs as a real subprocess | Prompt contract only, no runtime-level isolation |
| **Model selection per agent** | Yes — `model: haiku` in frontmatter | No — model is set at environment/launch level |
| **Tool restrictions per agent** | Yes — `tools: Bash, Skill` in frontmatter | No — Codex uses whatever tools are available |
| **Context isolation** | Runtime-enforced (separate agent process) | By convention (agent is instructed to keep output compact) |
| **Network sandbox** | No sandbox, full connectivity | Sandboxed by default — `fj` may fail; retry with escalated permissions |
| **Skill invocation** | `/fluffyjaws <question>` slash command | Mention FluffyJaws in the prompt; Codex loads the skill file |

### What this means in practice

**Claude Code** can assign a cheap, fast model (`haiku`) specifically to the FluffyJaws agent while the main conversation uses a more capable model. The agent boundary is enforced by the runtime, so raw `fj` output truly never enters the parent context.

**Codex** does not support per-agent model selection or native subagent spawning from markdown files. The agent file (`.codex/agents/fluffyjaws-agent.md`) is a prompt contract — Codex reads it as instructions and is expected to follow the rules by convention. Context isolation depends on the model following the "return only the distilled answer" instruction, not on a hard runtime boundary.

> If context isolation and cost control matter to your workflow, Claude Code provides stronger guarantees. For Codex, the `--fast` flag on `fj chat` is the main lever for reducing response weight.

## Troubleshooting

**Auth error** — run `fj login` or export `FJ_SESSION_ID=<your-session-id>`.

**Connectivity / timeout in Codex** — Codex sandboxes network access. If `fj chat` fails inside the sandbox, retry with escalated permissions or run it outside the sandbox.

**`fj` not found** — ensure the CLI is installed and available on `$PATH`.
