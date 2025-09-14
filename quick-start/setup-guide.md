# ⚡ Quick Start Guide - 5 Minutes to Your First MCP Server

Get your first MCP server running in under 5 minutes!

## Prerequisites Check (30 seconds)

✅ **Docker Desktop** installed and running  
✅ **Claude Desktop** installed  
✅ Terminal/Command Prompt open  

## Step 1: Enable Docker MCP Toolkit (1 minute)

1. Open Docker Desktop
2. Go to **Settings** → **Beta Features**
3. Enable **"Docker MCP Toolkit"**
4. Click **Apply & Restart**

## Step 2: Build the Dice Roller Example (1 minute)

```bash
# Clone this repository (or download the ZIP)
git clone https://github.com/thenetworkchuck/docker-mcp-tutorial.git
cd docker-mcp-tutorial

# Navigate to the dice roller example
cd examples/dice-roller

# Build the Docker image
docker build -t dice-mcp-server .
```

## Step 3: Create Custom Catalog (1 minute)

```bash
# Create the catalogs directory
mkdir -p ~/.docker/mcp/catalogs

# Create custom catalog file
cat > ~/.docker/mcp/catalogs/custom.yaml << 'EOF'
version: 2
name: custom
displayName: Custom MCP Servers
registry:
  dice:
    description: "Dice rolling for tabletop games"
    title: "Dice Roller"
    type: server
    dateAdded: "2025-01-01T00:00:00Z"
    image: dice-mcp-server:latest
    ref: ""
    tools:
      - name: flip_coin
      - name: roll_dice
      - name: roll_custom
      - name: roll_stats
      - name: roll_advantage
      - name: roll_disadvantage
      - name: roll_check
      - name: roll_initiative
    metadata:
      category: productivity
      tags:
        - gaming
        - dice
        - randomization
EOF
```

## Step 4: Update Registry (30 seconds)

```bash
# Add to registry
echo "  dice:" >> ~/.docker/mcp/registry.yaml
echo '    ref: ""' >> ~/.docker/mcp/registry.yaml
```

## Step 5: Configure Claude Desktop (1 minute)

### macOS:
```bash
# Edit Claude config
nano ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

### Windows (PowerShell):
```powershell
# Edit Claude config
notepad "$env:APPDATA\Claude\claude_desktop_config.json"
```

Add this configuration (replace `[YOUR_USERNAME]` with your actual username):

```json
{
  "mcpServers": {
    "mcp-toolkit-gateway": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-v", "/var/run/docker.sock:/var/run/docker.sock",
        "-v", "/Users/[YOUR_USERNAME]/.docker/mcp:/mcp",
        "docker/mcp-gateway",
        "--catalog=/mcp/catalogs/docker-mcp.yaml",
        "--catalog=/mcp/catalogs/custom.yaml",
        "--config=/mcp/config.yaml",
        "--registry=/mcp/registry.yaml",
        "--tools-config=/mcp/tools.yaml",
        "--transport=stdio"
      ]
    }
  }
}
```

**Note for Windows:** Use `C:\\Users\\[YOUR_USERNAME]` with double backslashes

## Step 6: Test It! (30 seconds)

1. **Restart Claude Desktop** (Quit completely and reopen)
2. Open a new chat
3. Click the tools icon (or press Cmd/Ctrl+I)
4. You should see "mcp-toolkit-gateway" with dice rolling tools
5. Try it: "Roll 2d6+3 for damage"

## 🎉 Success!

You now have a working MCP server! Claude can now roll dice for you.

## Troubleshooting

**Tools not appearing?**
- Make sure Docker Desktop is running
- Verify the Docker image built successfully: `docker images | grep dice`
- Check Claude logs: Help → Show Logs

**Permission errors?**
- Make sure Docker Desktop has necessary permissions
- On Mac: System Preferences → Security & Privacy

**Still stuck?**
- Check the full [troubleshooting guide](../docs/troubleshooting.md)
- Watch the video tutorial for visual guidance

## What's Next?

- Build your own MCP server using the [MCP Builder Prompt](../mcp-builder-prompt/)
- Learn about [custom server development](../docs/custom-servers.md)
- Explore the [Docker MCP Gateway](../docs/docker-gateway.md)

---

🎥 **Need visual help?** Watch NetworkChuck's full tutorial on YouTube!
