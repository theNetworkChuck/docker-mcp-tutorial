# 🐛 Troubleshooting Guide

Common issues and solutions for Docker MCP servers.

## Quick Diagnostics

Run these commands first:

```bash
# Check Docker is running
docker ps

# Check MCP toolkit
docker mcp --help

# List MCP servers
docker mcp server list

# Check secrets
docker mcp secret list

# Verify images
docker images | grep mcp
```

## Common Issues

### 🔴 Tools Not Appearing in Claude

**Symptoms:**

- MCP gateway shows in Claude but no tools
- Tools were working but disappeared
- Some tools show but not others

**Solutions:**

1. **Restart Claude completely:**
   - Quit Claude (Cmd+Q / Alt+F4)
   - Not just closing the window!
   - Relaunch Claude

2. **Check Docker image exists:**

   ```bash
   docker images | grep your-server-name
   ```

   If missing, rebuild:

   ```bash
   docker build -t your-server-name .
   ```

3. **Verify catalog entry:**

   ```bash
   cat ~/.docker/mcp/catalogs/custom.yaml
   ```

   Check for:
   - Correct image name
   - Tools list populated
   - No syntax errors

4. **Check registry:**

   ```bash
   cat ~/.docker/mcp/registry.yaml
   ```

   Ensure your server is listed under `registry:` key

5. **Validate Claude config:**

   ```bash
   # macOS
   cat ~/Library/Application\ Support/Claude/claude_desktop_config.json
   
   # Windows
   type %APPDATA%\Claude\claude_desktop_config.json
   ```

   Look for custom catalog in args

### 🔴 Docker MCP Toolkit Not Available

**Symptoms:**

- No MCP option in Docker Desktop settings
- `docker mcp` command not found

**Solutions:**

1. **Update Docker Desktop:**
   - Check for updates in Docker Desktop
   - Download latest from docker.com

2. **Enable Beta Features:**
   - Settings → Beta Features
   - Enable "Docker MCP Toolkit"
   - Apply & Restart

3. **Reset Docker Desktop:**
   - Troubleshoot → Reset to factory defaults
   - Warning: This removes all containers/images!

### 🔴 Build Failures

**Symptoms:**

- `docker build` fails with errors
- Package installation fails
- Dockerfile syntax errors

**Solutions:**

1. **Check Dockerfile syntax:**

   ```dockerfile
   # Correct
   FROM python:3.11-slim
   
   # Wrong (typo)
   FORM python:3.11-slim
   ```

2. **Network issues:**

   ```bash
   # Build with no cache
   docker build --no-cache -t server-name .
   
   # Use different mirror
   docker build --build-arg PIP_INDEX_URL=https://pypi.org/simple -t server-name .
   ```

3. **Requirements issues:**

   ```bash
   # Test requirements locally
   pip install -r requirements.txt
   ```

### 🔴 Authentication Errors

**Symptoms:**

- API calls failing with 401/403
- "Token not configured" errors
- Secret not found

**Solutions:**

1. **Check secret is set:**

   ```bash
   docker mcp secret list
   ```

2. **Set secret correctly:**

   ```bash
   docker mcp secret set API_KEY="your-actual-key"
   ```

3. **Verify environment variable name:**
   - Check server code for exact name
   - Check catalog for secret mapping
   - Names must match exactly!

4. **Test locally:**

   ```bash
   export API_KEY="your-key"
   python your_server.py
   ```

### 🔴 Container Crashes

**Symptoms:**

- Tools appear but don't work
- "Server error" messages
- Container exits immediately

**Solutions:**

1. **Check logs:**

   ```bash
   # Find container ID
   docker ps -a | grep your-server
   
   # View logs
   docker logs [container-id]
   ```

2. **Test server directly:**

   ```bash
   # Run interactively
   docker run -it your-server-name /bin/bash
   
   # Then test Python
   python your_server.py
   ```

3. **Common causes:**
   - Missing dependencies
   - Import errors
   - Syntax errors in Python
   - Wrong Python version

### 🔴 Permission Errors

**Symptoms:**

- "Permission denied" errors
- Cannot access Docker socket
- Cannot write files

**Solutions:**

1. **Fix Docker socket (Linux/Mac):**

   ```bash
   sudo chmod 666 /var/run/docker.sock
   ```

2. **Add user to docker group:**

   ```bash
   sudo usermod -aG docker $USER
   newgrp docker
   ```

3. **Windows WSL issues:**
   - Ensure WSL 2 is enabled
   - Check Docker Desktop WSL integration
   - Run as Administrator

### 🔴 Gateway Connection Issues

**Symptoms:**

- SSE transport not working
- Cannot connect remotely
- n8n integration failing

**Solutions:**

1. **Check port is open:**

   ```bash
   # Is gateway running?
   docker ps | grep mcp-gateway
   
   # Test connection
   curl http://localhost:8811
   ```

2. **Firewall issues:**

   ```bash
   # macOS
   sudo pfctl -d  # Disable firewall temporarily
   
   # Windows
   # Check Windows Defender Firewall
   ```

3. **Correct startup command:**

   ```bash
   docker run -p 8811:8811 docker/mcp-gateway --transport sse
   ```

## Platform-Specific Issues

### macOS

**Apple Silicon Issues:**

- Some images need platform flag:

  ```bash
  docker build --platform linux/amd64 -t server .
  ```

**Permissions:**

- Grant Docker full disk access in System Preferences

### Windows

**WSL 2 Issues:**

```powershell
# Check WSL version
wsl --status

# Update WSL
wsl --update

# Set default version
wsl --set-default-version 2
```

**Path Issues:**

- Use double backslashes in JSON configs
- Or use forward slashes

### Linux

**Docker Daemon:**

```bash
# Check status
sudo systemctl status docker

# Restart
sudo systemctl restart docker

# Enable on boot
sudo systemctl enable docker
```

## Debug Commands

### Verbose Logging

```bash
# Build with verbose output
docker build --progress=plain -t server .

# Run with debug
DEBUG=true python server.py
```

### Test MCP Protocol

```bash
# List tools
echo '{"jsonrpc":"2.0","method":"tools/list","id":1}' | python server.py

# Call a tool
echo '{"jsonrpc":"2.0","method":"tools/call","params":{"name":"tool_name","arguments":{}},"id":2}' | python server.py
```

### Clean Slate

```bash
# Remove all MCP images
docker images | grep mcp | awk '{print $3}' | xargs docker rmi

# Clear secrets
docker mcp secret list | tail -n +2 | awk '{print $1}' | xargs -I {} docker mcp secret delete {}

# Rebuild everything
cd your-server && docker build -t your-server .
```

## Error Messages Explained

**"Gateway panic"**

- Usually a syntax error in server code
- Check for multi-line docstrings (use single-line only)

**"No such image"**

- Docker image not built
- Wrong image name in catalog

**"Transport error"**

- MCP protocol issue
- Check JSON formatting

**"Tool not found"**

- Tool not decorated with @mcp.tool()
- Tool not listed in catalog

**"Invalid parameters"**

- Type hints too complex
- Use simple string parameters

## Getting Help

### Logs to Collect

1. Docker Desktop version: `docker --version`
2. Claude logs: Help → Show Logs
3. Container logs: `docker logs [container]`
4. Your Dockerfile and server.py
5. Catalog and registry files

### Where to Get Help

- GitHub Issues on this repo
- Docker Community Forums
- NetworkChuck Discord
- Stack Overflow with `docker-mcp` tag

## Prevention Tips

1. **Always test locally first:**

   ```bash
   python your_server.py
   ```

2. **Use the builder prompt correctly:**
   - Provide clear requirements
   - Include API documentation
   - Specify all tools needed

3. **Follow naming conventions:**
   - Lowercase server names
   - No spaces in image names
   - Match names exactly everywhere

4. **Keep it simple:**
   - Start with one tool
   - Add complexity gradually
   - Test after each addition

---

*Remember: Most issues are typos or missing restarts. When in doubt, restart everything!*
