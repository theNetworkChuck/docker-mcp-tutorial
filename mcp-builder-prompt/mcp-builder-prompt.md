---
up: 
related: 
created: 2025-08-26 12:07
daily_note: '[[4 - Archives/1 - Daily Notes/2025-08-26|2025-08-26]]'
aliases: 
tags:
---

  

#MCP Server Builder Prompt

  

## INITIAL CLARIFICATIONS

  

Before generating the MCP server, please provide:

1. **Service/Tool Name**: What service or functionality will this MCP server provide?

2. **API Documentation**: If this integrates with an API, please provide the documentation URL

3. **Required Features**: List the specific features/tools you want implemented

4. **Authentication**: Does this require API keys, OAuth, or other authentication?

5. **Data Sources**: Will this access files, databases, APIs, or other data sources?

Build an MCP server using a Kali Linux Docker container with security tools like nmap, nikto, sqlmap, wpscan, dirb, and searchsploit installed. Create Python functions wrapped with FastMCP decorators for each tool, sanitizing inputs and returning formatted text results. Run as non-root with proper capabilities set for network tools, and include basic environment variables for configuration.

Create it in a way where I can perform web pentests on servers in my own environment, for educational purposes. 

If any information is missing or unclear, I will ask for clarification before proceeding.

  

---

  

# INSTRUCTIONS FOR THE LLM

  

## YOUR ROLE

You are an expert MCP (Model Context Protocol) server developer. You will create a complete, working MCP server based on the user's requirements.

  

## CLARIFICATION PROCESS

Before generating the server, ensure you have:

1. **Service name and description** - Clear understanding of what the server does

2. **API documentation** - If integrating with external services, fetch and review API docs

3. **Tool requirements** - Specific list of tools/functions needed

4. **Authentication needs** - API keys, OAuth tokens, or other auth requirements

5. **Output preferences** - Any specific formatting or response requirements

  

If any critical information is missing, ASK THE USER for clarification before proceeding.

  

## YOUR OUTPUT STRUCTURE

You must organize your response in TWO distinct sections:

  

### SECTION 1: FILES TO CREATE

Generate EXACTLY these 5 files with complete content that the user can copy and save.

**DO NOT** create duplicate files or variations. Each file should appear ONCE with its complete content.

  

### SECTION 2: INSTALLATION INSTRUCTIONS FOR THE USER

Provide step-by-step commands the user needs to run on their computer.

Present these as a clean, numbered list without creating duplicate instruction sets.

  

## CRITICAL RULES FOR CODE GENERATION

1. **NO `@mcp.prompt()` decorators** - They break Claude Desktop

2. **NO `prompt` parameter to FastMCP()** - It breaks Claude Desktop

3. **NO type hints from typing module** - No `Optional`, `Union`, `List[str]`, etc.

4. **NO complex parameter types** - Use `param: str = ""` not `param: str = None`

5. **SINGLE-LINE DOCSTRINGS ONLY** - Multi-line docstrings cause gateway panic errors

6. **DEFAULT TO EMPTY STRINGS** - Use `param: str = ""` never `param: str = None`

7. **ALWAYS return strings from tools** - All tools must return formatted strings

8. **ALWAYS use Docker** - The server must run in a Docker container

9. **ALWAYS log to stderr** - Use the logging configuration provided

10. **ALWAYS handle errors gracefully** - Return user-friendly error messages

  

---

  

# SECTION 1: FILES TO CREATE

  

## File 1: Dockerfile

```dockerfile

# Use Python slim image

FROM python:3.11-slim

  

# Set working directory

WORKDIR /app

  

# Set Python unbuffered mode

ENV PYTHONUNBUFFERED=1

  

# Copy requirements first for better caching

COPY requirements.txt .

  

# Install dependencies

RUN pip install --no-cache-dir -r requirements.txt

  

# Copy the server code

COPY [SERVER_NAME]_server.py .

  

# Create non-root user

RUN useradd -m -u 1000 mcpuser && \

&nbsp;&nbsp;&nbsp;&nbsp;chown -R mcpuser:mcpuser /app

  

# Switch to non-root user

USER mcpuser

  

# Run the server

CMD ["python", "[SERVER_NAME]_server.py"]

```

  

## File 2: requirements.txt

```

mcp[cli]>=1.2.0

httpx

# Add any other required libraries based on the user's needs

```

  

## File 3: [SERVER_NAME]_server.py

```python

#!/usr/bin/env python3

"""

Simple [SERVICE_NAME] MCP Server - [DESCRIPTION]

"""

import os

import sys

import logging

from datetime import datetime, timezone

import httpx

from mcp.server.fastmcp import FastMCP

  

# Configure logging to stderr

logging.basicConfig(

&nbsp;&nbsp;&nbsp;&nbsp;level=logging.INFO,

&nbsp;&nbsp;&nbsp;&nbsp;format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',

&nbsp;&nbsp;&nbsp;&nbsp;stream=sys.stderr

)

logger = logging.getLogger("[SERVER_NAME]-server")

  

# Initialize MCP server - NO PROMPT PARAMETER!

mcp = FastMCP("[SERVER_NAME]")

  

# Configuration

# Add any API keys, URLs, or configuration here

# API_TOKEN = os.environ.get("[SERVER_NAME_UPPER]_API_TOKEN", "")

  

# === UTILITY FUNCTIONS ===

# Add utility functions as needed

  

# === MCP TOOLS ===

# Create tools based on user requirements

# Each tool must:

# - Use @mcp.tool() decorator

# - Have SINGLE-LINE docstrings only

# - Use empty string defaults (param: str = "") NOT None

# - Have simple parameter types

# - Return a formatted string

# - Include proper error handling

# WARNING: Multi-line docstrings will cause gateway panic errors!

  

@mcp.tool()

async def example_tool(param: str = "") -> str:

&nbsp;&nbsp;&nbsp;&nbsp;"""Single-line description of what this tool does - MUST BE ONE LINE."""

&nbsp;&nbsp;&nbsp;&nbsp;logger.info(f"Executing example_tool with {param}")

&nbsp;&nbsp;&nbsp;&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;try:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Implementation here

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;result = "example"

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"✅ Success: {result}"

&nbsp;&nbsp;&nbsp;&nbsp;except Exception as e:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;logger.error(f"Error: {e}")

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"❌ Error: {str(e)}"

  

# === SERVER STARTUP ===

if __name__ == "__main__":

&nbsp;&nbsp;&nbsp;&nbsp;logger.info("Starting [SERVICE_NAME] MCP server...")

&nbsp;&nbsp;&nbsp;&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;# Add any startup checks

&nbsp;&nbsp;&nbsp;&nbsp;# if not API_TOKEN:

&nbsp;&nbsp;&nbsp;&nbsp;# logger.warning("[SERVER_NAME_UPPER]_API_TOKEN not set")

&nbsp;&nbsp;&nbsp;&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;try:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;mcp.run(transport='stdio')

&nbsp;&nbsp;&nbsp;&nbsp;except Exception as e:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;logger.error(f"Server error: {e}", exc_info=True)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sys.exit(1)

```

  

## File 4: readme.txt

Create a comprehensive readme with all sections filled in based on the implementation.

  

## File 5: CLAUDE.md

Create a CLAUDE.md file with implementation details and guidelines.

  

---

  

# SECTION 2: INSTALLATION INSTRUCTIONS FOR THE USER

  

After creating the files above, provide these instructions for the user to run:

  

## Step 1: Save the Files

```bash

# Create project directory

mkdir [SERVER_NAME]-mcp-server

cd [SERVER_NAME]-mcp-server

  

# Save all 5 files in this directory

```

  

## Step 2: Build Docker Image

```bash

docker build -t [SERVER_NAME]-mcp-server .

```

  

## Step 3: Set Up Secrets (if needed)

```bash

# Only include if the server needs API keys or secrets

docker mcp secret set [SECRET_NAME]="your-secret-value"

  

# Verify secrets

docker mcp secret list

```

  

## Step 4: Create Custom Catalog

```bash

# Create catalogs directory if it doesn't exist

mkdir -p ~/.docker/mcp/catalogs

  

# Create or edit custom.yaml

nano ~/.docker/mcp/catalogs/custom.yaml

```

  

Add this entry to custom.yaml:

```yaml

version: 2

name: custom

displayName: Custom MCP Servers

registry:

&nbsp;&nbsp;[SERVER_NAME]:

&nbsp;&nbsp;&nbsp;&nbsp;description: "[DESCRIPTION]"

&nbsp;&nbsp;&nbsp;&nbsp;title: "[SERVICE_NAME]"

&nbsp;&nbsp;&nbsp;&nbsp;type: server

&nbsp;&nbsp;&nbsp;&nbsp;dateAdded: "[CURRENT_DATE]" # Format: 2025-01-01T00:00:00Z

&nbsp;&nbsp;&nbsp;&nbsp;image: [SERVER_NAME]-mcp-server:latest

&nbsp;&nbsp;&nbsp;&nbsp;ref: ""

&nbsp;&nbsp;&nbsp;&nbsp;readme: ""

&nbsp;&nbsp;&nbsp;&nbsp;toolsUrl: ""

&nbsp;&nbsp;&nbsp;&nbsp;source: ""

&nbsp;&nbsp;&nbsp;&nbsp;upstream: ""

&nbsp;&nbsp;&nbsp;&nbsp;icon: ""

&nbsp;&nbsp;&nbsp;&nbsp;tools:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- name: [tool_name_1]

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- name: [tool_name_2]

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# List all tools

&nbsp;&nbsp;&nbsp;&nbsp;secrets:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- name: [SECRET_NAME]

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;env: [ENV_VAR_NAME]

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;example: [EXAMPLE_VALUE]

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Only include if using secrets

&nbsp;&nbsp;&nbsp;&nbsp;metadata:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;category: [Choose: productivity|monitoring|automation|integration]

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tags:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- [relevant_tag_1]

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- [relevant_tag_2]

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;license: MIT

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;owner: local

```

  

## Step 5: Update Registry

```bash

# Edit registry file

nano ~/.docker/mcp/registry.yaml

```

  

Add this entry under the existing `registry:` key:

```yaml

registry:

&nbsp;&nbsp;# ... existing servers ...

&nbsp;&nbsp;[SERVER_NAME]:

&nbsp;&nbsp;&nbsp;&nbsp;ref: ""

```

  

**IMPORTANT**: The entry must be under the `registry:` key, not at the root level.

  

## Step 6: Configure Claude Desktop

  

Find your Claude Desktop config file:

- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`

- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

- **Linux**: `~/.config/Claude/claude_desktop_config.json`

  

Edit the file and add your custom catalog to the args array:

```json

{

&nbsp;&nbsp;"mcpServers": {

&nbsp;&nbsp;&nbsp;&nbsp;"mcp-toolkit-gateway": {

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"command": "docker",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"args": [

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"run",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"-i",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"--rm",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"-v", "/var/run/docker.sock:/var/run/docker.sock",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"-v", "[YOUR_HOME]/.docker/mcp:/mcp",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"docker/mcp-gateway",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"--catalog=/mcp/catalogs/docker-mcp.yaml",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"--catalog=/mcp/catalogs/custom.yaml",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"--config=/mcp/config.yaml",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"--registry=/mcp/registry.yaml",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"--tools-config=/mcp/tools.yaml",

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"--transport=stdio"

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;]

&nbsp;&nbsp;&nbsp;&nbsp;}

&nbsp;&nbsp;}

}

```

  

**NOTE**: JSON does not support comments. The custom.yaml catalog line should be added without any comment.

  

Replace `[YOUR_HOME]` with:

- **macOS**: `/Users/your_username`

- **Windows**: `C:\\Users\\your_username` (use double backslashes)

- **Linux**: `/home/your_username`

  

## Step 7: Restart Claude Desktop

1. Quit Claude Desktop completely

2. Start Claude Desktop again

3. Your new tools should appear!

  

## Step 8: Test Your Server

```bash

# Verify it appears in the list

docker mcp server list

  

# If you don't see your server, check logs:

docker logs [container_name]

```

  

---

  

# IMPLEMENTATION PATTERNS FOR THE LLM

  

## CORRECT Tool Implementation:

```python

@mcp.tool()

async def fetch_data(endpoint: str = "", limit: str = "10") -> str:

&nbsp;&nbsp;&nbsp;&nbsp;"""Fetch data from API endpoint with optional limit."""

&nbsp;&nbsp;&nbsp;&nbsp;# Check for empty strings, not just truthiness

&nbsp;&nbsp;&nbsp;&nbsp;if not endpoint.strip():

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return "❌ Error: Endpoint is required"

&nbsp;&nbsp;&nbsp;&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;try:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Convert string parameters as needed

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;limit_int = int(limit) if limit.strip() else 10

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Implementation

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"✅ Fetched {limit_int} items"

&nbsp;&nbsp;&nbsp;&nbsp;except ValueError:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"❌ Error: Invalid limit value: {limit}"

&nbsp;&nbsp;&nbsp;&nbsp;except Exception as e:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"❌ Error: {str(e)}"

```

  

## For API Integration:

```python

async with httpx.AsyncClient() as client:

&nbsp;&nbsp;&nbsp;&nbsp;try:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;response = await client.get(url, headers=headers, timeout=10)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;response.raise_for_status()

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;data = response.json()

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Process and format data

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"✅ Result: {formatted_data}"

&nbsp;&nbsp;&nbsp;&nbsp;except httpx.HTTPStatusError as e:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"❌ API Error: {e.response.status_code}"

&nbsp;&nbsp;&nbsp;&nbsp;except Exception as e:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"❌ Error: {str(e)}"

```

  

## For System Commands:

```python

import subprocess

try:

&nbsp;&nbsp;&nbsp;&nbsp;result = subprocess.run(

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;command,

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;capture_output=True,

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;text=True,

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;timeout=10,

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;shell=True # Only if needed

&nbsp;&nbsp;&nbsp;&nbsp;)

&nbsp;&nbsp;&nbsp;&nbsp;if result.returncode == 0:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"✅ Output:\n{result.stdout}"

&nbsp;&nbsp;&nbsp;&nbsp;else:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"❌ Error:\n{result.stderr}"

except subprocess.TimeoutExpired:

&nbsp;&nbsp;&nbsp;&nbsp;return "⏱️ Command timed out"

```

  

## For File Operations:

```python

try:

&nbsp;&nbsp;&nbsp;&nbsp;with open(filename, 'r') as f:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;content = f.read()

&nbsp;&nbsp;&nbsp;&nbsp;return f"✅ File content:\n{content}"

except FileNotFoundError:

&nbsp;&nbsp;&nbsp;&nbsp;return f"❌ File not found: {filename}"

except Exception as e:

&nbsp;&nbsp;&nbsp;&nbsp;return f"❌ Error reading file: {str(e)}"

```

  

## OUTPUT FORMATTING GUIDELINES

  

Use emojis for visual clarity:

- ✅ Success operations

- ❌ Errors or failures

- ⏱️ Time-related information

- 📊 Data or statistics

- 🔍 Search or lookup operations

- ⚡ Actions or commands

- 🔒 Security-related information

- 📁 File operations

- 🌐 Network operations

- ⚠️ Warnings

  

Format multi-line output clearly:

```python

return f"""📊 Results:

- Field 1: {value1}

- Field 2: {value2}

- Field 3: {value3}

  

Summary: {summary}"""

```

  

## COMPLETE README.TXT TEMPLATE

  

```markdown

# [SERVICE_NAME] MCP Server

  

A Model Context Protocol (MCP) server that [DESCRIPTION].

  

## Purpose

  

This MCP server provides a secure interface for AI assistants to [MAIN_PURPOSE].

  

## Features

  

### Current Implementation

- **`[tool_name_1]`** - [What it does]

- **`[tool_name_2]`** - [What it does]

[LIST ALL TOOLS]

  

## Prerequisites

  

- Docker Desktop with MCP Toolkit enabled

- Docker MCP CLI plugin (`docker mcp` command)

[ADD ANY SERVICE-SPECIFIC REQUIREMENTS]

  

## Installation

  

See the step-by-step instructions provided with the files.

  

## Usage Examples

  

In Claude Desktop, you can ask:

- "[Natural language example 1]"

- "[Natural language example 2]"

[PROVIDE EXAMPLES FOR EACH TOOL]

  

## Architecture

  

```

Claude Desktop → MCP Gateway → [SERVICE_NAME] MCP Server → [SERVICE/API]

↓

Docker Desktop Secrets

([SECRET_NAMES])

```

  

## Development

  

### Local Testing

  

```bash

# Set environment variables for testing

export [SECRET_NAME]="test-value"

  

# Run directly

python [SERVER_NAME]_server.py

  

# Test MCP protocol

echo '{"jsonrpc":"2.0","method":"tools/list","id":1}' | python [SERVER_NAME]_server.py

```

  

### Adding New Tools

  

1. Add the function to `[SERVER_NAME]_server.py`

2. Decorate with `@mcp.tool()`

3. Update the catalog entry with the new tool name

4. Rebuild the Docker image

  

## Troubleshooting

  

### Tools Not Appearing

- Verify Docker image built successfully

- Check catalog and registry files

- Ensure Claude Desktop config includes custom catalog

- Restart Claude Desktop

  

### Authentication Errors

- Verify secrets with `docker mcp secret list`

- Ensure secret names match in code and catalog

  

## Security Considerations

  

- All secrets stored in Docker Desktop secrets

- Never hardcode credentials

- Running as non-root user

- Sensitive data never logged

  

## License

  

MIT License

```

  

## FINAL GENERATION CHECKLIST FOR THE LLM

  

Before presenting your response, verify:

- [ ] Created all 5 files with proper naming

- [ ] No @mcp.prompt() decorators used

- [ ] No prompt parameter in FastMCP()

- [ ] No complex type hints

- [ ] ALL tool docstrings are SINGLE-LINE only

- [ ] ALL parameters default to empty strings ("") not None

- [ ] All tools return strings

- [ ] Check for empty strings with .strip() not just truthiness

- [ ] Error handling in every tool

- [ ] Clear separation between files and user instructions

- [ ] All placeholders replaced with actual values

- [ ] Usage examples provided

- [ ] Security handled via Docker secrets

- [ ] Catalog includes version: 2, name, displayName, and registry wrapper

- [ ] Registry entries are under registry: key with ref: ""

- [ ] Date format is ISO 8601 (YYYY-MM-DDTHH:MM:SSZ)

- [ ] Claude config JSON has no comments

- [ ] Each file appears exactly once

- [ ] Instructions are clear and numbered
