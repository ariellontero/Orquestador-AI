# Multi-Agent Orchestrator

A terminal dashboard (TUI) that dispatches tasks to multiple AI coding agents working in parallel on your codebase. Define tasks in a simple Markdown file, and the orchestrator assigns them to agents, tracks progress, handles rate limits, and manages dependencies.

![orchestrator-demo](https://img.shields.io/badge/TUI-blessed-cyan)

## Supported Agents

| Agent | CLI | How it connects |
|-------|-----|----------------|
| **Claude Code** | `claude` | Pipe mode (`-p`), stream-json output |
| **Codex** (OpenAI) | `codex` | Exec mode (`--yolo`), stdin prompt |
| **Gemini CLI** | `gemini` | Approval mode yolo, stream-json |
| **Cursor** | `agent` | Yolo mode, stdin prompt |
| **OpenCode** | `opencode` | Run mode, JSON output |
| **Abacus AI** | `abacusai` | Print mode (`-p`), file-piped prompt |
| **Custom** | any | Configure `command` or `args` in config |

## Quick Start

```bash
# 1. Clone and install
git clone https://github.com/ariellontero/Orquestador-AI.git
cd multi-agent-orchestrator
npm install

# 2. Configure your project
#    Edit orchestrator.config.json with your repos and agents

# 3. Add tasks to QUEUE.md

# 4. Run
node orchestrator.js
```

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| **S** | Start / Resume |
| **P** | Pause / Resume |
| **R** | Reload queue from QUEUE.md |
| **Q** | Quit (kills all agents) |

## Configuration

Edit `orchestrator.config.json`:

```json
{
  "projectName": "My Project",
  "maxConcurrent": 3,
  "pollIntervalSeconds": 30,
  "taskTimeoutMinutes": 30,

  "repos": {
    "api": "/path/to/api-repo",
    "frontend": "/path/to/frontend-repo"
  },

  "agents": {
    "Backend": {
      "cli": "claude",
      "defaultRepo": "api",
      "model": "sonnet",
      "instructionsFile": "agents/BACKEND.md"
    },
    "Frontend": {
      "cli": "claude",
      "defaultRepo": "frontend",
      "model": "sonnet",
      "instructionsFile": "agents/FRONTEND.md"
    },
    "Codex": {
      "cli": "codex",
      "defaultRepo": "api",
      "instructionsFile": "agents/CODEX.md"
    }
  }
}
```

### Agent Config Options

| Field | Required | Description |
|-------|----------|-------------|
| `cli` | Yes | CLI type: `claude`, `codex`, `gemini`, `cursor`, `opencode`, `abacusai`, or any custom CLI name |
| `defaultRepo` | Yes | Key from `repos` map — where this agent works by default |
| `model` | No | Model override (e.g., `sonnet`, `opus`) — only for Claude |
| `instructionsFile` | No | Path to a Markdown file with agent-specific instructions |
| `command` | No | Full custom command override (e.g., `my-cli --flag1 --flag2`) |
| `args` | No | Array of args for generic CLI agents |

### Adding a Custom Agent

Any CLI that accepts a prompt via stdin and exits with code 0 on success can be an agent:

```json
{
  "agents": {
    "MyAgent": {
      "cli": "my-custom-cli",
      "args": ["--no-interactive", "--format", "json"],
      "defaultRepo": "api"
    }
  }
}
```

## Queue Format (QUEUE.md)

Tasks are defined in `QUEUE.md` using a pipe-separated format:

```markdown
## Pending

TASK-001 | Fix login bug | Backend | P1 | api | Fix the 401 error on /auth/login endpoint
TASK-002 | Add dark mode | Frontend | P2 | frontend | Implement dark mode toggle in header
TASK-003 | Write tests | Codex | P2 | api | Add unit tests for auth module > after:TASK-001

## In Progress


## Completed
```

### Fields

| Field | Description |
|-------|-------------|
| `id` | Unique task ID (e.g., `TASK-001`) |
| `title` | Short description |
| `agent` | Agent name (must match a key in `agents` config) |
| `priority` | `P1` (high), `P2` (medium), `P3` (low) |
| `repo` | Repository key (must match a key in `repos` config) |
| `description` | Detailed task description |

### Dependencies

Append `> after:TASK-NNN` to the description to block a task until another completes:

```
TASK-003 | Write tests | Backend | P2 | api | Test the auth module > after:TASK-001
```

### Sections

- **`## Pending`** — Tasks waiting to be picked up
- **`## In Progress`** — (managed by the orchestrator)
- **`## Completed`** — Finished tasks (auto-moved by orchestrator)

## Optional Files

| File | Purpose |
|------|---------|
| `agents/BACKEND.md` | Instructions injected into the Backend agent's prompt |
| `agents/FRONTEND.md` | Instructions injected into the Frontend agent's prompt |
| `AGENT-PROTOCOL.md` | Shared rules injected into all agents |
| `TASKS.md` | Detailed task specs (matched by `### TASK-NNN` headers) |
| `briefs/TASK-001-BRIEF.md` | Detailed brief for a specific task |
| `progress/PROGRESS-Backend.md` | Agent progress file (agents update this) |

## Features

- **Parallel execution** — All idle agents run simultaneously
- **Dependency chains** — `> after:TASK-NNN` blocks until dependency completes
- **Rate limit handling** — Auto-detects 429s and retries at reset time (up to 10 retries)
- **Auto-retry** — Failed tasks retry up to 2 times (10 for rate limits)
- **Live output** — See each agent's stdout in split panels
- **Cost tracking** — Tracks `total_cost_usd` from Claude stream-json
- **Budget cap** — `--max-budget=N` stops when spending exceeds $N
- **Hot reload** — Press **R** to reload QUEUE.md without restarting
- **Persistent state** — Completed tasks survive TUI restart
- **Dead process detection** — Heartbeat checks every 15s

## CLI Options

```
node orchestrator.js [options]

Options:
  --paused         Start paused (press S to start)
  --max-budget=N   Stop after spending $N
  --help           Show help
```

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `SKIP_PERMISSIONS` | `false` | Set to `true` to use `--dangerously-skip-permissions` for Claude |

## How It Works

1. Reads `QUEUE.md` for pending tasks
2. For each idle agent with a matching task, generates a prompt (brief) containing:
   - Task description and context
   - Agent-specific instructions (from `instructionsFile`)
   - Protocol rules (from `AGENT-PROTOCOL.md`)
   - Detailed brief (from `briefs/TASK-NNN-BRIEF.md`)
3. Spawns the agent's CLI with the prompt via stdin
4. Streams output to the TUI panel and log file
5. On exit code 0: moves task to Completed, triggers next task
6. On failure: retries or marks as permanently failed
7. Checks dependencies before launching blocked tasks

## Logs

All output is logged to `logs/`:
- `orchestrator-YYYY-MM-DD.log` — Orchestrator events
- `TASK-NNN-AgentName-timestamp.log` — Full agent output per task

## License

CC BY-NC
