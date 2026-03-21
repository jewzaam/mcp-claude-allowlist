# mcp-claude-allowlist

MCP server that exposes Claude Code's allowed permissions from `settings.json` files. Lets MCP clients query which tool permissions a user has pre-approved — globally and per-project.

## How It Works

Claude Code stores permission allowlists in three locations:

| File | Scope |
|------|-------|
| `~/.claude/settings.json` | Global — applies to all projects |
| `<project>/.claude/settings.json` | Project — shared, checked into git |
| `<project>/.claude/settings.local.json` | Project — personal, gitignored |

This server reads the `permissions.allow` array from each file and exposes them via three MCP tools.

## Requirements

- Python 3.10+

## Installation

```bash
git clone <repo-url>
cd mcp-claude-allowlist
make install
```

For development (includes test/lint tooling):

```bash
make install-dev
```

## Configure Claude Code

`make install` places the `mcp-claude-allowlist` executable inside the virtualenv. Claude Code needs the full path to find it — a bare command name only works if the venv is activated or on your `PATH`.

**User-scoped** (available in all projects, stored in `~/.claude.json`):

```bash
# Linux / macOS
claude mcp add --scope user allowlist -- /path/to/mcp-claude-allowlist/.venv/bin/mcp-claude-allowlist

# Windows
claude mcp add --scope user allowlist -- C:/path/to/mcp-claude-allowlist/.venv/Scripts/mcp-claude-allowlist.exe
```

**Project-scoped** (stored in `.mcp.json` in the project root):

```bash
# Linux / macOS
claude mcp add --scope project allowlist -- /path/to/mcp-claude-allowlist/.venv/bin/mcp-claude-allowlist

# Windows
claude mcp add --scope project allowlist -- C:/path/to/mcp-claude-allowlist/.venv/Scripts/mcp-claude-allowlist.exe
```

Verify the server is registered:

```bash
claude mcp list
```

## Tools

### `get_global_allowed_permissions`

Returns permissions from `~/.claude/settings.json`.

No arguments.

```json
{
  "source": "global",
  "count": 2,
  "permissions": ["Bash(ls:*)", "WebSearch"],
  "timestamp": "2026-03-19T10:00:00.000000"
}
```

### `get_local_allowed_permissions`

Returns project-local permissions from both `.claude/settings.json` and `.claude/settings.local.json`, plus a deduplicated merge.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `project_dir` | string | cwd | Project directory path |

```json
{
  "source": "local",
  "project_dir": "/home/user/myproject",
  "settings": ["Bash(make test:*)"],
  "settings_local": ["Bash(ls:*)"],
  "merged": ["Bash(make test:*)", "Bash(ls:*)"],
  "timestamp": "2026-03-19T10:00:00.000000"
}
```

### `get_allowed_permissions`

Returns the full deduplicated merge of global + local permissions. Use this when you want the complete picture.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `project_dir` | string | cwd | Project directory path |

```json
{
  "source": "merged",
  "project_dir": "/home/user/myproject",
  "global_count": 2,
  "local_count": 1,
  "total_count": 3,
  "permissions": ["Bash(ls:*)", "WebSearch", "Bash(make test:*)"],
  "timestamp": "2026-03-19T10:00:00.000000"
}
```

## Development

```bash
make check       # Run all checks (format, lint, typecheck, test, coverage)
make test         # Run tests
make lint         # Run ruff linter
make typecheck    # Run mypy
make format       # Auto-format code
make coverage     # Run tests with coverage report
```

## License

Apache 2.0
