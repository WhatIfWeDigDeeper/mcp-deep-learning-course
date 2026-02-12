# Model Context Protocol (MCP) - Architecture

Overview

## Scope
The Model Context Protocol (MCP) includes the following projects:

- **MCP Specification**: A specification of MCP that outlines the implementation requirements for clients and servers
- **MCP SDKs**: SDKs for different programming languages that implement MCP
- **MCP Development Tools**: Tools for developing MCP servers and clients, including the MCP Inspector
- **MCP Reference Server Implementations**: Reference implementations of MCP servers
> **Note**: MCP focuses solely on the protocol for context exchange—it does not dictate how AI applications use LLMs or manage the provided context.
>
## Core Concepts of MCP

### Participants

MCP follows a client-server architecture with three key participants:

1. **MCP Host**: The AI application that coordinates and manages one or multiple MCP clients (e.g., Claude Code, Claude Desktop, Visual Studio Code)
2. **MCP Client**: A component that maintains a connection to an MCP server and obtains context from an MCP server for the MCP host to use
3. **MCP Server**: A program that provides context to MCP clients
**Key Points:**
- Local MCP servers use STDIO transport and typically serve a single MCP client
- Remote MCP servers use Streamable HTTP transport and typically serve many MCP clients
- MCP servers can execute locally or remotely

### Layers

MCP consists of two layers:

#### 1. Data Layer
Defines the JSON-RPC based protocol for client-server communication, including:
- **Lifecycle management**: Connection initialization, capability negotiation, and connection termination
- **Server features**: Tools, resources, and prompts
- **Client features**: Sampling from host LLM, user input elicitation, and logging
- **Utility features**: Notifications and progress tracking
#### 2. Transport Layer
Manages communication channels and authentication between clients and servers. Supports two mechanisms:
- **STDIO transport**: Uses standard input/output streams for direct process communication between local processes
- **Streamable HTTP transport**: Uses HTTP POST for client-to-server messages with optional Server-Sent Events for streaming capabilities

## Data Layer Protocol

### Lifecycle Management

MCP is a stateful protocol that requires lifecycle management to negotiate capabilities that both client and server support. The initialization sequence includes:
1. **Protocol Version Negotiation**: Ensures both client and server use compatible protocol versions
2. **Capability Discovery**: Allows each party to declare supported features
3. **Identity Exchange**: Provides identification and versioning information
**Example Initialization Request:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "initialize",
  "params": {
    "protocolVersion": "2025-06-18",
    "capabilities": {
      "elicitation": {}
    },
    "clientInfo": {
      "name": "example-client",
      "version": "1.0.0"
    }
  }
}
```

### Primitives

MCP primitives define what clients and servers can offer each other.

#### Server Primitives (provided by servers):

1. **Tools**: Executable functions that AI applications can invoke to perform actions
   - Examples: file operations, API calls, database queries
   - Methods: `tools/list`, `tools/call`
2. **Resources**: Data sources that provide contextual information to AI applications
   - Examples: file contents, database records, API responses
   - Methods: `resources/list`, `resources/read`
3. **Prompts**: Reusable templates that help structure interactions with language models
   - Examples: system prompts, few-shot examples
   - Methods: `prompts/list`, `prompts/get`

#### Client Primitives (provided by clients):

1. **Sampling**: Allows servers to request language model completions from the client\'s AI application
   - Method: `sampling/complete`
2. **Elicitation**: Allows servers to request additional information from users
   - Method: `elicitation/request`
3. **Logging**: Enables servers to send log messages to clients for debugging and monitoring
#### Utility Primitives:
- **Tasks (Experimental)**: Durable execution wrappers that enable deferred result retrieval and status tracking

### Notifications

The protocol supports real-time notifications to enable dynamic updates between servers and clients. Notifications are sent as JSON-RPC 2.0 notification messages (without expecting a response).
**Example:**
- When a server\'s available tools change, it can send tool update notifications (`tools/list_changed`) to inform connected clients

## Example Workflow

### Step 1: Initialization
The client sends an `initialize` request to establish the connection and negotiate supported features.
**Client declares capabilities:**
- `"elicitation": {}` - Can receive `elicitation/create` method calls
**Server declares capabilities:**
- `"tools": {"listChanged": true}` - Supports tools primitive AND can send notifications
- `"resources": {}` - Supports resources primitive

### Step 2: Tool Discovery
The client discovers available tools by sending a `tools/list` request.
**Example Request:**
```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "tools/list"
}
```
**Example Response:**
Returns an array of tools with metadata:
- `name`: Unique identifier for the tool
- `title`: Human-readable display name
- `description`: Detailed explanation of the tool
- `inputSchema`: JSON Schema defining expected input parameters

### Step 3: Tool Execution
The client executes a tool using the `tools/call` method.
**Example Request:**
```json
{
  "jsonrpc": "2.0",
  "id": 3,
  "method": "tools/call",
  "params": {
    "name": "weather_current",
    "arguments": {
      "location": "San Francisco",
      "units": "imperial"
    }
  }
}
```
**Example Response:**
Returns a content array with structured results:
```json
{
  "jsonrpc": "2.0",
  "id": 3,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "Current weather in San Francisco: 72°F, Partly Cloudy"
      }
    ]
  }
}
```

### Step 4: Notifications
Servers can send notifications to clients about changes:
**Example Tool List Change Notification:**
```json
{
  "jsonrpc": "2.0",
  "method": "tools/list_changed"
}
```

## How MCP Works in AI Applications

1. **Initialization**: The AI application\'s MCP client manager establishes connections to configured servers and stores their capabilities
2. **Tool Discovery**: The AI application fetches available tools from all connected MCP servers and combines them into a unified tool registry
3. **Tool Execution**: When the language model decides to use a tool during a conversation, the AI application intercepts the tool call, routes it to the appropriate MCP server, executes it, and returns the results back to the LLM
4. **Real-time Updates**: Servers can notify clients about capability changes, allowing the AI application to dynamically update its available tools and resources
## Key Features
- **Flexible Content System**: Tool responses return an array of content objects, allowing for rich, multi-format responses (text, images, resources, etc.)
- **Dynamic Discovery**: Listings (`*/list` methods) are dynamic, allowing servers to update available capabilities at runtime
- **Model-Independent**: MCP allows server authors to stay model-independent by using client sampling capabilities instead of including language model SDKs
- **Standardized Communication**: Uses JSON-RPC 2.0 as the underlying RPC protocol for all interactions
-
