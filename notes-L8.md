# Lesson 8: Configuring Servers for Claude Desktop

MCP servers can be used by other applications, like Claude Desktop, and agents.

## Setup

```sh
cd L8/mcp_project
uv init
uv venv
source .venv/bin/activate
uv run mcp_chatbot.py
```

## Configure Claude Desktop

Go to Settings..Developers and click edit config. Paste in this json. Note you'll need to update the paths and if you are not using Volta, give it another path for npx.

```json
"mcpServers": {
    "filesystem": {
      "command": "/Users/REPLACE-ME/.volta/bin/npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/Users/REPLACE-ME/code/mcp-deep-learning-course/L7/mcp_project/"
      ]
    },
    "research": {
      "command": "/Users/REPLACE-ME/.local/bin/uv",
      "args": [
        "--directory",
        "/Users/REPLACE-ME/code/mcp-deep-learning-course/L7/mcp_project/",
        "run",
        "research_server.py"
      ]
    },
    "fetch": {
      "command": "/Users/REPLACE-ME/.local/bin/uvx",
      "args": [
        "mcp-server-fetch"
      ]
    }
  }
```

## Claude Desktop prompt

```text
Use the fetch tool visit deeplearning.ai and find an interesting topic about machine learning on that webpage.

Then research two papers on arxiv about that topic and summarize the main topics covered.

Finally, generate a web based quiz application with a set of flashcards based on the key topics in the papers.
```
