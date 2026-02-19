# 📦 Complete Installation Guide

Detailed platform-specific instructions for setting up Docker MCP servers.

## Table of Contents

- [macOS Installation](#macos-installation)
- [Windows Installation](#windows-installation)
- [Linux Installation](#linux-installation)
- [Verifying Installation](#verifying-installation)
- [Common Issues](#common-issues)

---

## macOS Installation

### Step 1: Install Docker Desktop

1. Download Docker Desktop for Mac:
   - **Apple Silicon (M1/M2/M3)**: [Download here](https://desktop.docker.com/mac/main/arm64/Docker.dmg)
   - **Intel Mac**: [Download here](https://desktop.docker.com/mac/main/amd64/Docker.dmg)

2. Open the downloaded `.dmg` file
3. Drag Docker to Applications folder
4. Launch Docker from Applications
5. Accept the service agreement

### Step 2: Enable MCP Toolkit

1. Click Docker icon in menu bar
2. Open **Preferences** → **Beta Features**
3. Enable **"Docker MCP Toolkit"**
4. Click **Apply & Restart**

### Step 3: Install Claude Desktop

1. Download from [claude.ai/download](https://claude.ai/download)
2. Open the `.dmg` file
3. Drag Claude to Applications
4. Launch Claude and sign in

### Step 4: Configure MCP Connection

```bash
# Create MCP directories
mkdir -p ~/.docker/mcp/catalogs

# Edit Claude configuration
nano ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

Add the MCP gateway configuration (see Quick Start guide for details).

---

## Windows Installation

### Prerequisites

- Windows 10 64-bit: Pro, Enterprise, or Education (Build 19041 or higher)
- Windows 11 64-bit: Home, Pro, Enterprise, or Education

### Step 1: Enable WSL 2

Open PowerShell as Administrator:

```powershell
# Enable WSL
wsl --install

# Set WSL 2 as default
wsl --set-default-version 2

# Restart your computer
```

### Step 2: Install Docker Desktop

1. Download [Docker Desktop for Windows](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe)
2. Run the installer
3. Ensure "Use WSL 2 instead of Hyper-V" is selected
4. Complete installation and restart

### Step 3: Configure Docker

1. Launch Docker Desktop
2. Go to **Settings** → **General**
3. Ensure "Use the WSL 2 based engine" is checked
4. Go to **Beta Features**
5. Enable **"Docker MCP Toolkit"**
6. Apply & Restart

### Step 4: Install Claude Desktop

1. Download from [claude.ai/download](https://claude.ai/download)
2. Run the installer
3. Launch Claude and sign in

### Step 5: Configure MCP Connection

Open PowerShell:

```powershell
# Create MCP directories
mkdir -Force "$HOME\.docker\mcp\catalogs"

# Edit Claude configuration
notepad "$env:APPDATA\Claude\claude_desktop_config.json"
```

**Important for Windows paths:** Use double backslashes in JSON:

```json
"C:\\Users\\YourUsername\\.docker\\mcp"
```

---

## Linux Installation

### Supported Distributions

- Ubuntu 20.04 LTS or later
- Debian 11 or later
- Fedora 36 or later
- Arch Linux (latest)

### Step 1: Install Docker

#### Ubuntu/Debian

```bash
# Update package index
sudo apt-get update

# Install prerequisites
sudo apt-get install ca-certificates curl gnupg

# Add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Set up repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Add user to docker group
sudo usermod -aG docker $USER
newgrp docker
```

#### Fedora

```bash
# Install Docker
sudo dnf -y install dnf-plugins-core
sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Start Docker
sudo systemctl start docker
sudo systemctl enable docker

# Add user to docker group
sudo usermod -aG docker $USER
```

### Step 2: Install Docker Desktop (Optional but Recommended)

Download the .deb or .rpm package from [Docker Desktop for Linux](https://docs.docker.com/desktop/install/linux-install/)

```bash
# For Ubuntu/Debian
sudo dpkg -i docker-desktop-*.deb

# For Fedora
sudo rpm -i docker-desktop-*.rpm
```

### Step 3: Install Claude Desktop

Currently, Claude Desktop requires running through a compatibility layer on Linux. Alternatively, use:

- Cursor IDE with MCP support
- LM Studio
- Custom MCP clients

### Step 4: Configure MCP

```bash
# Create MCP directories
mkdir -p ~/.docker/mcp/catalogs

# Edit configuration (for Cursor or other clients)
nano ~/.config/[client-name]/mcp_config.json
```

---

## Verifying Installation

### Check Docker Installation

```bash
# Check Docker version
docker --version

# Check Docker is running
docker ps

# Test Docker with hello-world
docker run hello-world
```

### Check MCP Toolkit

```bash
# Check if MCP commands are available
docker mcp --help

# List available MCP servers
docker mcp server list

# Check secrets management
docker mcp secret list
```

### Test MCP Connection

1. Build a test server:

```bash
cd examples/dice-roller
docker build -t test-mcp .
```

2. Check if it appears in Docker:

```bash
docker images | grep test-mcp
```

3. Restart your MCP client (Claude/Cursor)
4. Check if tools appear

---

## Common Issues

### Docker Desktop Won't Start

**macOS:**

- Check System Preferences → Security & Privacy
- Ensure Docker has necessary permissions
- Try resetting Docker: Troubleshoot → Reset to factory defaults

**Windows:**

- Ensure virtualization is enabled in BIOS
- Check WSL 2 is properly installed: `wsl --status`
- Run as Administrator

**Linux:**

- Check Docker daemon: `sudo systemctl status docker`
- Verify user is in docker group: `groups`

### MCP Toolkit Not Available

- Ensure you're on the latest Docker Desktop version
- Beta features must be enabled
- Try reinstalling Docker Desktop

### Tools Not Appearing in Claude

1. Verify Docker image exists: `docker images`
2. Check catalog file syntax
3. Ensure registry.yaml is properly formatted
4. Restart Claude Desktop completely (not just close window)

### Permission Errors

**macOS/Linux:**

```bash
# Fix Docker socket permissions
sudo chmod 666 /var/run/docker.sock

# Or add user to docker group
sudo usermod -aG docker $USER
```

**Windows:**

- Run Docker Desktop as Administrator
- Check WSL 2 integration in Docker settings

### Build Failures

Common causes:

- Network issues downloading packages
- Dockerfile syntax errors
- Missing dependencies

Debug with:

```bash
# Verbose build output
docker build --no-cache --progress=plain -t test .

# Check build logs
docker logs [container-id]
```

---

## Next Steps

✅ Installation complete? Now:

1. Try the [Quick Start Guide](../quick-start/setup-guide.md)
2. Build a custom server with the [MCP Builder Prompt](../mcp-builder-prompt/)
3. Learn about [Docker MCP Gateway](docker-gateway.md)

Need help? Check the [Troubleshooting Guide](troubleshooting.md) or watch the video tutorial!
