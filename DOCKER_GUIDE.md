# 🐳 Running Notion MCP Server in Docker

## Quick Start

### 1. Make sure your .env file exists with your NOTION_TOKEN

```bash
cat .env
```

Should contain:
```
NOTION_TOKEN=ntn_your_token_here
```

### 2. Build the Docker image

```bash
docker compose -f docker-compose.local.yml build
```

### 3. Run the server

**HTTP Mode (recommended for testing):**
```bash
docker compose -f docker-compose.local.yml up
```

The server will be available at: http://localhost:3000/mcp
Health check: http://localhost:3000/health

**STDIO Mode (for MCP clients):**
```bash
docker compose up
```

### 4. Test the server

In another terminal:
```bash
# Health check
curl http://localhost:3000/health

# Should return: {"status":"healthy","timestamp":"...","transport":"http","port":3000}
```

## Docker Commands Reference

```bash
# Build the image
docker compose -f docker-compose.local.yml build

# Run in foreground (see logs)
docker compose -f docker-compose.local.yml up

# Run in background (detached)
docker compose -f docker-compose.local.yml up -d

# View logs
docker compose -f docker-compose.local.yml logs -f

# Stop the server
docker compose -f docker-compose.local.yml down

# Rebuild and restart
docker compose -f docker-compose.local.yml up --build

# Remove everything (including volumes)
docker compose -f docker-compose.local.yml down -v
```

## Alternative: Run directly with Docker (without compose)

```bash
# Build
docker build -t notion-mcp-server .

# Run HTTP mode
docker run -p 3000:3000 \
  -e NOTION_TOKEN=ntn_your_token_here \
  notion-mcp-server \
  --transport http --port 3000

# Run STDIO mode (for piping commands)
docker run -i \
  -e NOTION_TOKEN=ntn_your_token_here \
  notion-mcp-server
```

## Using with MCP Clients via Docker

For Claude Desktop or Cursor, add this to your config:

```json
{
  "mcpServers": {
    "notionApi": {
      "command": "docker",
      "args": [
        "run",
        "--rm",
        "-i",
        "-e",
        "NOTION_TOKEN=ntn_your_token_here",
        "notion-mcp-server"
      ]
    }
  }
}
```

Or use the official image from Docker Hub:

```json
{
  "mcpServers": {
    "notionApi": {
      "command": "docker",
      "args": [
        "run",
        "--rm",
        "-i",
        "-e",
        "NOTION_TOKEN=ntn_your_token_here",
        "mcp/notion"
      ]
    }
  }
}
```

## Troubleshooting

### Port already in use
If port 3000 is already in use, change it in docker-compose.local.yml:
```yaml
ports:
  - "8080:3000"  # Use port 8080 on your host
```

### NOTION_TOKEN not being read
Make sure your .env file doesn't have quotes around the value:
```bash
# Correct
NOTION_TOKEN=ntn_123456789

# Incorrect
NOTION_TOKEN="ntn_123456789"
```

### Permission denied
Make sure Docker has permission to read your .env file:
```bash
chmod 644 .env
```

### View container logs
```bash
docker compose -f docker-compose.local.yml logs notion-mcp-server
```

## Testing the Server

Once running, test with curl:

```bash
# Health check
curl http://localhost:3000/health

# Initialize MCP session (if auth token was auto-generated, check logs for the token)
curl -H "Authorization: Bearer your-auth-token" \
     -H "Content-Type: application/json" \
     -H "mcp-session-id: test-123" \
     -d '{
       "jsonrpc": "2.0",
       "method": "initialize",
       "params": {
         "protocolVersion": "2024-11-05",
         "capabilities": {},
         "clientInfo": {"name": "test", "version": "1.0.0"}
       },
       "id": 1
     }' \
     http://localhost:3000/mcp
```

## Benefits of Using Docker

✅ Isolated environment - doesn't affect your system Node.js
✅ Consistent behavior across different machines
✅ Easy to deploy and scale
✅ No need to install Node.js locally
✅ Easy cleanup - just remove the container

🎉 You're all set with Docker!
