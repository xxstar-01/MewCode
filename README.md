# MewCode

A terminal AI coding assistant with a Textual-powered TUI, multi-agent teams, and deep tool integration.

## Features

- **Terminal TUI** — Rich Textual interface with chat history, tool call blocks, and permission dialogs
- **Multi-Provider** — Supports Anthropic Claude, OpenAI, and OpenAI-compatible APIs
- **Multi-Agent Teams** — Spawn sub-agents and coordinate teams with shared task boards and inter-agent messaging
- **MCP Integration** — Connect to Model Context Protocol servers for extended tool capabilities
- **Skills** — Pluggable skill system with built-in and project-local skills
- **Worktrees** — Isolated git worktrees for safe parallel agent execution
- **Memory** — Persistent file-based memory with relevance recall
- **Hooks** — Event-driven hooks (startup, shutdown, tool execution)
- **Permission Modes** — default, accept-edits, plan, YOLO — cycle with `Shift+Tab`
- **Plan Mode** — Propose changes for user approval before execution
- **Sandbox** — Optional OS-level sandbox (bubblewrap on Linux, Seatbelt on macOS)
- **Session Management** — Resume past sessions with summaries
- **Non-Interactive Mode** — Run prompts from CLI with `-p` flag (supports JSON streaming output)
- **Remote Mode** — WebSocket server with browser UI on `localhost:18888`

## Installation

### Prerequisites

- Python 3.11+
- [uv](https://docs.astral.sh/uv/) (recommended) or pip

### Setup

```bash
# Clone the repository
git clone https://github.com/xxstar-01/MewCode.git
cd MewCode

# Install with uv
uv sync

# Or with pip
pip install -e .
```

### Configuration

Create `config.yaml` in your project root or `.mewcode/config.yaml`:

```yaml
providers:
  - name: claude
    protocol: anthropic
    base_url: https://api.anthropic.com/v1
    model: claude-sonnet-5-20250901
    # api_key: sk-ant-...  # or set ANTHROPIC_API_KEY env var

permission_mode: default  # default | accept-edits | plan | bypass
```

API keys are resolved from environment variables:
- Anthropic: `ANTHROPIC_API_KEY`
- OpenAI / OpenAI-compatible: `OPENAI_API_KEY`

## Usage

### Interactive Mode

```bash
uv run mewcode
```

### Non-Interactive Mode

```bash
uv run mewcode -p "Explain the authentication flow in this project"
uv run mewcode -p "..." --output-format stream-json  # NDJSON streaming output
```

### Remote Mode

```bash
uv run mewcode --remote
# Then open http://localhost:18888
```

### Permission Modes

| Mode | Description |
|------|-------------|
| `default` | Ask before running commands |
| `accept-edits` | Auto-approve file edits, ask for commands |
| `plan` | Propose changes for review before execution |
| `bypass` | Auto-approve everything |

Cycle through modes with `Shift+Tab` in the TUI.

## Architecture

```
mewcode/
├── app.py              # Textual TUI application
├── agent.py            # Core agent loop
├── client.py           # LLM provider clients (Anthropic, OpenAI)
├── config.py           # YAML config loading
├── conversation.py     # Conversation state management
├── tools/              # Tool implementations (Bash, Read/Write/Edit, Grep, Glob, etc.)
├── agents/             # Sub-agent system (loader, task manager, trace)
├── teams/              # Multi-agent team coordination
├── memory/             # File-based memory with auto-recall
├── skills/             # Pluggable skill system
├── hooks/              # Event hooks engine
├── mcp/                # MCP client and tool wrapper
├── permissions/        # Permission checker, sandbox, rules
├── sandbox/            # OS-level sandbox (bwrap, seatbelt)
├── worktree/           # Git worktree isolation
├── commands/           # Slash commands (/help, /clear, /memory, etc.)
└── filehistory/        # File modification tracking
```

## Dependencies

- [Textual](https://github.com/Textualize/textual) — TUI framework
- [Anthropic SDK](https://github.com/anthropics/anthropic-sdk-python) — Claude API client
- [OpenAI SDK](https://github.com/openai/openai-python) — OpenAI API client
- [MCP SDK](https://github.com/modelcontextprotocol/python-sdk) — Model Context Protocol
- [Pydantic](https://github.com/pydantic/pydantic) — Data validation
- [PyYAML](https://github.com/yaml/pyyaml) — Config parsing

## License

MIT
