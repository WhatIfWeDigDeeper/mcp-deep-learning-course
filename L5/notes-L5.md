# L5

## Setup

```sh
cd L5/mcp_project
uv init
uv venv
source .venv/bin/activate
uv add anthropic python-dotenv nest_asyncio
```

## Run chatbot

```bash
uv run mcp_chatbot.py
```

Search query:

```text
can you search for papers around physics and find just two of them for me
```
