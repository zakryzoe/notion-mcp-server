# 🎯 Notion MCP Server - Quick Start

## ⚡ Quick Test (HTTP Mode)

1. **Set your token:**
   ```bash
   export NOTION_TOKEN=ntn_your_token_here
   ```

2. **Start server:**
   ```bash
   ./test-server.sh http
   ```

3. **Test it:**
   ```bash
   # In another terminal
   curl http://localhost:3000/health
   ```

## 📋 Get Your Notion Token

1. Visit: https://www.notion.so/profile/integrations
2. Create integration → Copy the token (starts with `ntn_`)
3. In Notion: Go to a page → "..." → "Connections" → Add your integration

## 🎮 Run Modes

### STDIO (for Claude Desktop/Cursor)
```bash
export NOTION_TOKEN=ntn_your_token_here
npm start
```

### HTTP (for testing/web apps)
```bash
export NOTION_TOKEN=ntn_your_token_here
npm start -- --transport http --port 3000
```

## 🔌 Connect to Claude Desktop

Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (Mac)
or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

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

## ✅ You're all set!

The server is built and ready to run. Just:
1. Get your Notion token
2. Run `./test-server.sh http` to test
3. Configure your MCP client
4. Start using Notion with AI!

See [LOCAL_SETUP.md](LOCAL_SETUP.md) for detailed instructions.
