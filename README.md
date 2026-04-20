# mcp-claude-allowlist

MCP server that exposes Claude Code's allowed permissions from `settings.json` files, so Claude can check which tools are pre-approved before calling them.

## Setup

Requires Python 3.10+ and [uv](https://docs.astral.sh/uv/).

```bash
git clone <repo-url>
cd mcp-claude-allowlist
make install
claude mcp add --scope user allowlist -- mcp-claude-allowlist
```

`make install` uses `uv tool install`, which gives the package an isolated venv under `~/.local/share/uv/tools/` and places the binary in `~/.local/bin/`.

Verify with `claude mcp list`.

## Tools

| Tool | Description |
|------|-------------|
| `get_allowed_permissions` | Deduplicated merge of global + project-local permissions. The one to use. |
| `get_global_allowed_permissions` | Only `~/.claude/settings.json`. |
| `get_local_allowed_permissions` | Only project `.claude/settings.json` and `.claude/settings.local.json`. |

## Development

```bash
make install-dev  # Install with test/lint tooling into local .venv
make check        # Run all checks (format, lint, typecheck, test, coverage)
```

## License

Apache 2.0
