# Lesson 7: Adding Prompt and Resource Features

## Setup

```sh
cd L7/mcp_project
uv init
uv venv
source .venv/bin/activate
uv run mcp_chatbot.py
```

## Prompts

User should not have to do prompt engineering. `@mcp.prompt` allows developers to provide a template prompt for the user. See `generate_search_prompt` in [research_servery.py](L7/mcp_project/research_server.py)

On the chatbot, it can call to get a list of prompts and resources, in addition to the tools we saw earlier.

```python
await session.list_prompts()
await session.list_resources()

await session.list_tools()
```

On the server methods are decorated

```python
@mcp.prompt()
def generate_search_prompt

@mcp.resource("papers://{topic}")
def get_topic_papers(topic: str) -> str:

@mcp.resource("papers://folders")
def get_available_folders() -> str:

@mcp.tool()
def extract_info(paper_id: str) -> str:

@mcp.tool()
def search_papers(topic: str, max_results: int = 5) -> List[str]:
```

