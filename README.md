# BlakPort

<div align="center">

**FastAPI Gateway for Ollama with Advanced Agent Architecture**

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104+-green.svg)](https://fastapi.tiangolo.com/)

[Installation](#installation) • [Quick Start](#quick-start) • [Documentation](#documentation) • [API Reference](#api-reference)

</div>

## Overview

BlakPort is a comprehensive FastAPI wrapper for Ollama that transforms your local LLM setup into a powerful, production-ready API gateway. It provides advanced agent-based features including smart routing, conversation memory, response caching, security controls, and tool execution.

## Key Features

- 🚀 **Multiple Streaming Formats** - NDJSON, SSE, and plain text streaming
- 🧠 **Conversation Memory** - SQLite-based context management
- 🎯 **Smart Routing** - Rule-based and semantic model selection
- ⚡ **Response Caching** - Redis or in-memory caching with TTL
- 🔒 **Security** - API keys, rate limiting, IP filtering, audit logging
- 📊 **Monitoring** - Metrics, latency tracking, cache statistics
- 🛠️ **Tools** - Safe shell commands and AWS operations
- 🎨 **Web UI** - Playground and admin interfaces

## Installation

### From PyPI

```bash
pip install blakport
```

### With Optional Dependencies

```bash
# With Redis support
pip install blakport[redis]

# With AWS support
pip install blakport[aws]

# With all optional dependencies
pip install blakport[all]
```

### From Source

```bash
git clone https://github.com/oguzhancoskun/blakport.git
cd blakport
pip install -e .
```

## Quick Start

### 1. Prerequisites

Ensure Ollama is installed and running:

```bash
ollama serve
```

### 2. Initialize Configuration

```bash
blakport init
```

### 3. Start Server

```bash
blakport start
```

### 4. Test

```bash
curl http://localhost:8000/api/health
```

### 5. Use Playground

Open `http://localhost:8000/static/playground.html` in your browser.

## CLI Commands

```bash
blakport start          # Start server
blakport stop           # Stop server
blakport status         # Show status
blakport log            # View logs
blakport init           # Initialize config
blakport configure      # Update config
```

## API Usage

### Basic Generation

```bash
curl -X POST http://localhost:8000/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3",
    "prompt": "What is Python?"
  }'
```

### Streaming

```bash
curl -X POST "http://localhost:8000/api/generate?stream_format=sse" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3",
    "prompt": "Tell me a story",
    "stream": true
  }'
```

### With Memory

```bash
curl -X POST http://localhost:8000/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3",
    "prompt": "My name is Alice",
    "memory": true,
    "conversation_id": "conv-123"
  }'
```

## Agent Architecture

BlakPort uses a modular agent architecture:

- **Persona Agent** - Global system prompts and user profiles
- **Stream Agent** - Multiple streaming formats
- **Memory Agent** - Conversation context management
- **Router Agent** - Smart model selection
- **Cache Agent** - Response caching
- **Security Agent** - Authentication and rate limiting
- **Metrics Agent** - Performance monitoring
- **Tool Agent** - Safe external actions

## Configuration

Configuration is hierarchical (CLI > ENV > User Config > Defaults):

```yaml
# ~/.blakport/config.yaml
persona:
  system_prompt: "You are a helpful assistant"
  
router:
  enabled: true
  default_model: "llama3"
  
cache:
  enabled: true
  ttl: 3600
  
security:
  api_key_required: false
  rate_limiting:
    enabled: true
    default_per_minute: 60
```

See [CONFIG_GUIDE.md](CONFIG_GUIDE.md) for detailed configuration options.

## API Reference

Complete API documentation: [API.md](API.md)

### Core Endpoints

- `GET /api/health` - Health check
- `GET /api/tags` - List models
- `POST /api/generate` - Generate text

### Agent Endpoints

- `POST /api/router/route` - Route prompt
- `GET /api/router/rules` - Get routing rules
- `GET /api/tools/list` - List tools
- `POST /api/tools/execute` - Execute tool
- `GET /api/metrics` - Get metrics
- `GET /api/security/api-keys` - List API keys

## Features

See [FEATURES.md](FEATURES.md) for complete feature list.

## Documentation

- [API Reference](API.md) - Complete API documentation
- [Features](FEATURES.md) - Features and capabilities
- [Configuration Guide](CONFIG_GUIDE.md) - Configuration options
- [Tools Guide](TOOLS_CONFIG_GUIDE.md) - Tool configuration
- [Docker Sandbox Guide](DOCKER_SANDBOX_GUIDE.md) - Docker sandboxing

## Web Interfaces

- **Playground**: `http://localhost:8000/static/playground.html`
- **Router Admin**: `http://localhost:8000/static/router_admin.html`
- **Stream Test**: `http://localhost:8000/static/stream_test.html`

## Requirements

- Python 3.8+
- Ollama server running locally
- Optional: Redis (for persistent caching)
- Optional: boto3 (for AWS tool)

## Development

```bash
# Clone repository
git clone https://github.com/oguzhancoskun/blakport.git
cd blakport

# Install in development mode
pip install -e ".[dev]"

# Run tests
pytest tests/

# Run specific test
python tests/test_persona.py
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Acknowledgments

- Built with [FastAPI](https://fastapi.tiangolo.com/)
- Powered by [Ollama](https://ollama.ai/)

---

<div align="center">

Made with ❤️

[GitHub](https://github.com/oguzhancoskun/blakport) • [Issues](https://github.com/oguzhancoskun/blakport/issues) • [Documentation](DOCUMENTATION.md)

</div>
