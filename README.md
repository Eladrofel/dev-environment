# Development Environment

This repository contains all the configurations and tools needed to set up my local development environment on macOS.

## Structure

```
dev-environment/
├── mcp-gateway/          # MCP (Model Context Protocol) Gateway setup
└── README.md            # This file
```

## Components

### MCP Gateway (`mcp-gateway/`)
Docker Compose setup for running multiple MCP servers locally, providing unified access to AI tools and services.

**Services included:**
- **Exa** - AI-powered web search
- **Fetch** - Web content downloading
- **Playwright** - Web automation and browser control  
- **Ref** - Documentation and reference lookup
- **Semgrep** - Code security scanning

See [`mcp-gateway/README.md`](./mcp-gateway/README.md) for detailed setup instructions.

## Quick Start

1. **Clone this repository:**
   ```bash
   git clone https://github.com/eladrofel/dev-environment.git
   cd dev-environment
   ```

2. **Set up MCP Gateway:**
   ```bash
   cd mcp-gateway
   cp .env.example .env
   # Edit .env with your API keys
   docker-compose up -d
   ```

## Adding New Components

When adding new development tools or services:

1. Create a new directory with a descriptive name
2. Include a `README.md` with setup instructions
3. Add any necessary configuration files
4. Update this root README with the new component

## Environment Requirements

- **macOS** (primary development platform)
- **Docker & Docker Compose**
- **Git**

## Contributing

This is a personal development environment, but feel free to use it as inspiration for your own setup!