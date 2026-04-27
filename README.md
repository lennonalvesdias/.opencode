# opencode config

Personal OpenCode configuration with a multi-agent orchestration system, plugin extensions, and an opinionated philosophy for code quality.

## Structure

```
.config/opencode/
├── opencode.jsonc          # Main config: models, agents, permissions, MCP
├── agent/                  # Agent system prompts
│   ├── coder.md
│   ├── researcher.md
│   ├── reviewer.md
│   └── scribe.md
├── command/                # Slash commands
│   ├── commit.md
│   ├── review.md
│   └── plannotator-*.md
├── skills/                 # Loadable skill modules
│   ├── code-philosophy/
│   ├── frontend-philosophy/
│   ├── plan-protocol/
│   ├── plan-review/
│   ├── code-review/
│   └── karpathy-guidelines/
└── plugin/                 # TypeScript plugins
    ├── background-agents.ts
    ├── workspace-plugin.ts
    └── worktree.ts
```

## Agent System

Two orchestrators delegate to four specialist agents. Orchestrators cannot edit files or run commands — they only coordinate.

### Orchestrators

| Agent | Model | Role |
|-------|-------|------|
| `plan` | claude-opus-4.7 | Strategic planning, no implementation |
| `build` | claude-sonnet-4.6 | Implementation orchestration via delegation |

### Specialists

| Agent | Model | Role |
|-------|-------|------|
| `coder` | claude-sonnet-4.6 | Writes and edits code, runs builds/tests |
| `researcher` | claude-sonnet-4.6 | External research: docs, GitHub, web |
| `explore` | claude-haiku-4.5 | Read-only codebase exploration |
| `reviewer` | claude-opus-4.6 | Code and plan review |
| `scribe` | claude-haiku-4.5 | Documentation, commits, prose |

### Delegation vs Task

The `background-agents` plugin adds async delegation on top of OpenCode's native `task` tool:

- **`delegate(prompt, agent)`** — for read-only agents (`explore`, `researcher`). Returns immediately with an ID; result is persisted to disk and the orchestrator is notified on completion.
- **`task`** — for write-capable agents (`coder`, `scribe`). Native OpenCode tool with undo/branching support.

## Slash Commands

| Command | Description |
|---------|-------------|
| `/commit [hint]` | Stage changes and create a conventional commit |
| `/review [file\|recent]` | Run a full code review via the `reviewer` agent |
| `/plannotator-annotate` | Open interactive annotation UI |
| `/plannotator-review` | Review plan annotations |
| `/plannotator-last` | Show last annotation |

## Skills

Skills are loaded on demand by agents using the `skill` tool.

| Skill | Used by | Description |
|-------|---------|-------------|
| `code-philosophy` | `coder`, `reviewer` | The 5 Laws of Elegant Defense |
| `frontend-philosophy` | `coder`, `reviewer` | The 5 Pillars of Intentional UI |
| `code-review` | `reviewer` | 4-layer review methodology |
| `plan-protocol` | `plan`, `build` | Plan format and citation rules |
| `plan-review` | `reviewer` | Plan-specific review criteria |
| `karpathy-guidelines` | all (via `philosophy/AGENTS.md`) | 4 meta-behavioral principles: Think Before Coding, Simplicity First, Surgical Changes, Goal-Driven Execution |

> `karpathy-guidelines` is embedded directly into `philosophy/AGENTS.md` so its principles are always active — no explicit skill load required. The skill file serves as detailed reference.

### The 5 Laws (code-philosophy)

1. **Early Exit** — handle edge cases at the top with guard clauses
2. **Parse, Don't Validate** — parse inputs at the boundary; trust types internally
3. **Atomic Predictability** — pure functions where possible; same input = same output
4. **Fail Fast, Fail Loud** — invalid states throw immediately with descriptive errors
5. **Intentional Naming** — code reads like an English sentence

## MCP Servers

| Server | Type | Purpose |
|--------|------|---------|
| `vercel` | Remote | Vercel deployments and project management |
| `railway` | Local (`bunx`) | Railway deployments |
| `context7` | Remote | Library documentation lookup (disabled by default) |
| `exa` | Remote | Web search (disabled by default) |
| `gh_grep` | Remote | GitHub code search (disabled by default) |

MCP tools disabled globally are selectively enabled per agent via `permission` blocks. For example, `context7_*` and `exa_*` are only available to `researcher`.

## Plugins

| Plugin | Version | Description |
|--------|---------|-------------|
| `@tarquinen/opencode-dcp` | 1.2.7 | Dynamic context pruning |
| `@franlol/opencode-md-table-formatter` | 0.0.3 | Markdown table formatting |
| `@plannotator/opencode` | 0.19.1 | Plan annotation UI |

Custom plugins in `plugin/`:

- **`background-agents`** — async delegation system with result persistence, timeout handling, and batched notifications
- **`workspace-plugin`** — plan state management with strict schema validation
- **`worktree`** — git worktree creation and lifecycle management for isolated AI sessions

## Permission Model

Permissions follow a global → agent-specific hierarchy. Global defaults:

```jsonc
"permission": {
  "task": "deny",       // orchestrators only
  "webfetch": "deny",   // researcher only
  "worktree_*": "deny", // orchestrators only
  "context7_*": "deny", // researcher only
  "exa_*": "deny"       // researcher only
}
```

Each agent overrides only what it needs. Write-capable agents (`coder`, `scribe`) have `bash: "allow"` scoped to build/test commands. Read-only agents (`explore`, `reviewer`) have `edit: "deny"`, `write: "deny"`, and a bash whitelist.

## Models

```jsonc
"model": "github-copilot/claude-sonnet-4.6",       // default
"small_model": "github-copilot/claude-haiku-4.5"   // metadata, lightweight tasks
```

Provider is `github-copilot` (no separate API key required). Switch to `anthropic/` prefix for direct API access.
