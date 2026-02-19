# 🔨 Building Custom MCP Servers

Learn how to create your own MCP servers from scratch using NetworkChuck's MCP Builder Prompt.

## Overview

MCP servers are Docker containers that expose tools to AI assistants. You can build servers for:

- API integrations (weather, stocks, databases)
- System tools (file management, network utilities)
- Custom business logic
- Automation workflows

## Using the MCP Builder Prompt

### Step 1: Prepare Your Requirements

Before using the prompt, gather:

1. **Service name** - What will you call your server?
2. **API documentation** - Links to any APIs you'll use
3. **Tool list** - What specific functions do you need?
4. **Authentication** - API keys, OAuth requirements
5. **Example usage** - How will users interact with it?

### Step 2: Use the Prompt Template

1. Open `mcp-builder-prompt/mcp-builder-prompt.md`
2. Copy the entire prompt
3. Add your requirements at the indicated section
4. Paste into Claude, ChatGPT, or your preferred AI

### Step 3: Generate Your Server

The AI will create 5 files:

- `Dockerfile` - Container configuration
- `requirements.txt` - Python dependencies  
- `[name]_server.py` - Main server code
- `readme.txt` - Documentation
- `CLAUDE.md` - Integration guide

## Example: Weather MCP Server

### Input to the Prompt

```text
I want to build a weather MCP server that:
- Gets current weather for any city
- Gets 5-day forecast
- Converts between Celsius and Fahrenheit
- Uses the OpenWeatherMap API
```

### Generated Structure

```python
@mcp.tool()
async def get_weather(city: str = "") -> str:
    """Get current weather for a city."""
    # API call to OpenWeatherMap
    # Return formatted weather data
```

## Building Process

### 1. Create Project Directory

```bash
mkdir weather-mcp-server
cd weather-mcp-server
```

### 2. Save Generated Files

Save all 5 files from the AI output

### 3. Build Docker Image

```bash
docker build -t weather-mcp-server .
```

### 4. Add Secrets (if needed)

```bash
docker mcp secret set OPENWEATHER_API_KEY="your-key-here"
```

### 5. Register in Catalog

Add to `~/.docker/mcp/catalogs/custom.yaml`

### 6. Update Registry

Add to `~/.docker/mcp/registry.yaml`

### 7. Configure Client

Update Claude/Cursor configuration

### 8. Test

Restart client and test your new tools!

## Server Architecture

```text
Client (Claude/Cursor)
        ↓
Docker MCP Gateway
        ↓
Your MCP Server Container
        ↓
External APIs/Services
```

## Best Practices

### Code Structure

- One tool per function
- Clear, single-line docstrings
- Comprehensive error handling
- Informative return messages

### Security

- Never hardcode credentials
- Use Docker secrets for API keys
- Validate all inputs
- Run as non-root user

### Performance

- Add timeouts to API calls
- Cache responses when appropriate
- Use async operations
- Handle rate limits gracefully

## Common Patterns

### API Integration

```python
async with httpx.AsyncClient() as client:
    response = await client.get(url, headers=headers)
    response.raise_for_status()
    return format_response(response.json())
```

### File Operations

```python
try:
    with open(filename, 'r') as f:
        content = f.read()
    return f"✅ File read successfully"
except FileNotFoundError:
    return f"❌ File not found: {filename}"
```

### Command Execution

```python
result = subprocess.run(
    command,
    capture_output=True,
    text=True,
    timeout=10
)
return result.stdout if result.returncode == 0 else result.stderr
```

## Advanced Topics

### Multi-Tool Servers

Combine related tools in one server:

- Database operations (create, read, update, delete)
- Project management (tasks, projects, time tracking)
- DevOps tools (deploy, monitor, scale)

### Stateful Operations

While MCP is stateless, you can:

- Use external databases
- Write to files
- Maintain sessions via IDs

### Complex Workflows

Chain tools together:

1. Fetch data from API
2. Process/transform data
3. Store results
4. Send notifications

## Debugging

### Local Testing

```bash
# Run server directly
python your_server.py

# Test MCP protocol
echo '{"jsonrpc":"2.0","method":"tools/list","id":1}' | python your_server.py
```

### Check Logs

```bash
# View container logs
docker logs $(docker ps -a | grep your-server | awk '{print $1}')
```

### Common Issues

- **Import errors**: Check requirements.txt
- **Tool not found**: Verify @mcp.tool() decorator
- **Auth failures**: Check secret names match

## Examples from the Video

### Toggl Timer Integration

- Start/stop timers
- View time entries
- Track projects
- API authentication with tokens

### Kali Linux Tools

- Network scanning (nmap)
- Web testing (nikto, dirb)
- Security assessments
- Running system commands safely

## Resources

- [MCP Builder Prompt](../mcp-builder-prompt/mcp-builder-prompt.md)
- [Example: Dice Roller](../examples/dice-roller/)
- [Docker MCP Gateway Docs](docker-gateway.md)
- [MCP Protocol Specification](https://modelcontextprotocol.io/)

## Next Steps

1. Start with a simple API integration
2. Add error handling and validation
3. Implement multiple related tools
4. Share your creation with the community!

Remember: The MCP Builder Prompt does the heavy lifting - you just need to describe what you want!
