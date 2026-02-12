# L4

## Setup

```sh
cd L4/mcp_project
uv init
uv venv
source .venv/bin/activate
uv add mcp arxiv
```

## MCP Inspector

```bash
npx -y @modelcontextprotocol/inspector uv run research_server.py
```
