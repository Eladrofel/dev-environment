# MCP Gateway - Local Docker Setup

A simplified setup for running the MCP (Model Context Protocol) Gateway locally using Docker Compose.

## What is MCP Gateway?

The MCP Gateway provides unified access to multiple MCP servers through a single endpoint. It dynamically manages MCP server containers and provides a streaming interface for AI tools and applications.

## Features

This setup includes the following MCP servers:
- **exa** - AI-powered web search (via gateway)
- **fetch** - Web content fetching and downloading (via gateway)
- **playwright** - Web automation and browser control (via gateway)
- **semgrep** - Code security scanning (via gateway)
- **ref** - Documentation and reference lookup (standalone HTTP server)

## Prerequisites

- Docker and Docker Compose installed
- API keys for the services you want to use (see Environment Setup)

## Quick Start

1. **Clone this repository:**
   ```bash
   git clone https://github.com/eladrofel/mcp-gateway.git
   cd mcp-gateway
   ```

2. **Set up environment variables:**
   ```bash
   cp .env.example .env
   ```
   
3. **Edit `.env` file with your API keys:**
   ```bash
   # Required for Exa search functionality
   EXA_API_KEY=your_actual_exa_api_key
   
   # Required for Ref documentation search
   REF_API_KEY=your_actual_ref_api_key
   ```

4. **Start the MCP Gateway:**
   ```bash
   docker-compose up -d
   ```

5. **Verify it's running:**
   ```bash
   docker-compose ps
   docker-compose logs -f mcp-gateway
   ```

The services will be available at:
- **MCP Gateway**: `http://localhost:8811/mcp` (exa, fetch, playwright, semgrep)
- **Ref MCP Server**: `http://localhost:8080/mcp` (ref tools)

## Connecting MCP Clients

To connect MCP clients (like Claude Desktop, Cursor, etc.) to the running services, use the following configuration:

```json
{
  "mcpServers": {
    "Docker MCP Gateway": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "http://localhost:8811/mcp"
      ]
    },
    "Ref Documentation Server": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "http://localhost:8080/mcp"
      ]
    }
  }
}
```

**Notes**: 
- Both configurations use `mcp-remote` to connect to HTTP MCP servers
- The gateway provides exa, fetch, playwright, and semgrep tools
- The ref server runs separately to avoid container management issues

## Environment Setup

### Required API Keys

#### Exa API Key
- Sign up at [https://exa.ai/](https://exa.ai/)
- Get your API key from [https://dashboard.exa.ai/](https://dashboard.exa.ai/)
- Add to `.env`: `EXA_API_KEY=your_key_here`

#### Ref API Key
- Sign up at [https://ref.dev/](https://ref.dev/) (free)
- Get your API key from your dashboard
- Add to `.env`: `REF_API_KEY=your_key_here`

### Optional Configuration

#### Playwright Display (GUI Testing)
```bash
DISPLAY=:99  # Default for headless operation
```

#### Pieces Integration
```bash
PIECES_API_BASE_URL=http://localhost:1000  # Default Pieces OS URL
```

## Usage

### Docker Compose Commands

```bash
# Start the gateway
docker-compose up -d

# Stop the gateway
docker-compose down

# View logs
docker-compose logs -f mcp-gateway

# Check status
docker-compose ps

# Restart the gateway
docker-compose restart
```

### Health Check

The gateway includes a health check endpoint:
```bash
curl http://localhost:8811/health
```

### Dynamic Server Management

The gateway automatically manages MCP server containers:
- Servers start on-demand when tools are requested
- Unused servers are automatically stopped to save resources
- All configuration is handled dynamically

## Architecture

### MCP Gateway (Dynamic Mode)
The gateway manages most MCP servers in **dynamic mode**:
- Single gateway container manages exa, fetch, playwright, and semgrep
- Servers are spawned as needed using Docker-in-Docker
- Automatic resource management and cleanup
- Simplified configuration and maintenance

### Ref Server (Standalone)
The ref server runs **separately** for the following reasons:
- **Container Lifecycle**: Ref server runs in HTTP mode and creates persistent containers that don't clean up properly when managed by the gateway
- **Resource Management**: Running separately ensures proper container cleanup on compose down
- **Stability**: Avoids orphaned ref containers accumulating over time
- **Direct Access**: Provides direct HTTP access at `http://localhost:8080/mcp`

## Troubleshooting

### Container Not Starting
```bash
# Check logs for errors
docker-compose logs mcp-gateway

# Verify environment variables
docker-compose config
```

### API Key Issues
- Verify your API keys are correct in `.env`
- Check that `.env` file is in the same directory as `docker-compose.yml`
- Ensure no extra spaces or quotes around the keys

### Docker Socket Permissions
On some systems, you might need to add your user to the docker group:
```bash
sudo usermod -aG docker $USER
# Log out and back in for changes to take effect
```

### Port Conflicts
If port 8811 is already in use:
1. Change the port mapping in `docker-compose.yml`:
   ```yaml
   ports:
     - "8812:8811"  # Use 8812 instead
   ```
2. Restart: `docker-compose up -d`


## Development

### Adding New MCP Servers
To add more MCP servers, edit the `--servers` command in `docker-compose.yml`:
```yaml
command:
  - --transport=streaming
  - --port=8811
  - --servers=exa,fetch,playwright,ref,semgrep,your-new-server
```

### Custom Configuration
For advanced configuration, see the original [Docker MCP documentation](https://github.com/docker/mcp).

## Security Notes

- The `.env` file is ignored by git to protect your API keys
- Never commit real API keys to version control
- The gateway runs with restricted privileges and resource limits
- Consider using Docker secrets for production deployments

## License

This setup configuration is provided as-is. The underlying MCP Gateway is part of the Docker MCP project.

## Contributing

Feel free to submit issues or pull requests to improve this setup.