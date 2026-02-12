# Lesson 6: Connecting the MCP Chatbot to Reference Servers

[L6 Jupyter Notebook](L6/L6.ipynb)

## Reference Servers by Anthropic

[modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers?tab=readme-ov-file)

We will use the filesystem and the fetch MCP servers in addition to our research server.

```json
{
    "mcpServers": {

        "filesystem": {
            "command": "npx",
            "args": [
                "-y",
                "@modelcontextprotocol/server-filesystem",
                "."
                # . means isolated to current directory
            ]
        },
        "research": {
            "command": "uv",
            "args": ["run", "research_server.py"]
        },
        "fetch": {
            "command": "uvx",
            "args": ["mcp-server-fetch"]
        }
    }
}
```

## Setup

```sh
cd L6/mcp_project
uv init
uv venv
source .venv/bin/activate
uv run mcp_chatbot.py
```

## Query

```text
Fetch the content of this website: https://modelcontextprotocol.io/docs/concepts/architecture and save the content in the file "mcp_summary.md", create a visual diagram that summarizes the content of "mcp_summary.md" and save it in a text file
```

```text
Fetch deeplearning.ai and find an interesting term. Search for 2 papers around the term and then summarize your findings and write them to a file called results.txt
```


