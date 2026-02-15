# Lesson 9: Creating and Deploying Remote Servers

[Jupyter Notebook](L9/L9.ipynb)

## Setup

## Setup

```sh
cd L9/mcp_project
uv init
uv venv
source .venv/bin/activate
```

## Run Docker

```bash
# Start
docker compose up --build

# Start in background
# docker compose up --build -d
```

```bash
# Stop
docker compose down
```


## Run inspector

```bash
npx -y @modelcontextprotocol/inspector
```

Change the following in the inspector.

```text
Transport Type
  Streamable HTTP

URL
  http://localhost:8001/mcp
```

## Using Render.com

See repo [hsalami/remote-research](https://github.com/hsalami/remote-research)
