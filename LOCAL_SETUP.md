# Running Notion MCP Server Locally

## Prerequisites
✅ Node.js v18+ (You have v18.19.1)
✅ npm (You have v9.2.0)
✅ Dependencies installed
✅ Project built

## Setup Steps

### 1. Get Your Notion Integration Token

1. Go to https://www.notion.so/profile/integrations
2. Create a new **internal** integration (or use an existing one)
3. Copy the "Internal Integration Secret" (starts with `ntn_`)
4. In Notion, connect your integration to the pages/databases you want to access:
   - Go to a page → Click "..." → "Connections" → Add your integration

### 2. Configure Environment

Create a `.env` file in this directory with your Notion token:

```bash
NOTION_TOKEN=ntn_your_token_here
```

### 3. Run the Server

#### Option A: Using STDIO transport (for MCP clients like Claude Desktop)

```bash
# With NOTION_TOKEN environment variable
NOTION_TOKEN=ntn_your_token_here npm start

# Or if you created a .env file
source .env && npm start
```

#### Option B: Using HTTP transport (for web-based testing)

```bash
# Default port 3000
NOTION_TOKEN=ntn_your_token_here npm start -- --transport http

# Custom port
NOTION_TOKEN=ntn_your_token_here npm start -- --transport http --port 8080

# With authentication token
NOTION_TOKEN=ntn_your_token_here npm start -- --transport http --auth-token "your-secret-token"
```

### 4. Test the Server

If using HTTP transport, you can test with:

```bash
# Health check
curl http://localhost:3000/health

# MCP request (replace with your auth token if you set one)
curl -H "Authorization: Bearer your-auth-token" \
     -H "Content-Type: application/json" \
     -H "mcp-session-id: test-session" \
     -d '{"jsonrpc": "2.0", "method": "initialize", "params": {"protocolVersion": "2024-11-05", "capabilities": {}, "clientInfo": {"name": "test", "version": "1.0.0"}}, "id": 1}' \
     http://localhost:3000/mcp
```

### 5. Configure MCP Client (e.g., Claude Desktop)

Add to your `.cursor/mcp.json` or `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "notionApi": {
      "command": "node",
      "args": ["/home/zakryz/Exploration/notion-mcp-server/bin/cli.mjs"],
      "env": {
        "NOTION_TOKEN": "ntn_your_token_here"
      }
    }
  }
}
```

Or use npx to run from npm registry:

```json
{
  "mcpServers": {
    "notionApi": {
      "command": "npx",
      "args": ["-y", "@notionhq/notion-mcp-server"],
      "env": {
        "NOTION_TOKEN": "ntn_your_token_here"
      }
    }
  }
}
```

## Available Commands

```bash
# Show help
npm start -- --help

# Run with stdio (default)
npm start

# Run with HTTP transport
npm start -- --transport http

# Run with HTTP on custom port
npm start -- --transport http --port 8080

# Run with HTTP and custom auth token
npm start -- --transport http --auth-token "my-secret-token"
```

## Troubleshooting

### Missing NOTION_TOKEN
If you see errors about missing authentication, make sure you've set the `NOTION_TOKEN` environment variable.

### Permission Errors in Notion
Make sure you've connected your integration to the pages/databases in Notion that you want to access.

### Port Already in Use (HTTP transport)
If port 3000 is already in use, specify a different port:
```bash
npm start -- --transport http --port 8080
```

## Examples

Once running, you can ask your MCP client (like Claude) to:

- "Comment 'Hello MCP' on page 'Getting started'"
- "Add a page titled 'Notion MCP' to page 'Development'"
- "Search for pages about 'project planning'"
- "List all databases in my workspace"

## More Information

See the main [README.md](README.md) for full documentation.
