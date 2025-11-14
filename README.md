# BlakPort

<div align="center">

**Fast, lightweight FastAPI wrapper for exposing Ollama server to your local network**

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104+-green.svg)](https://fastapi.tiangolo.com/)

</div>

## Overview

BlakPort is a lightweight API gateway that exposes your local Ollama server to devices on your local network. It provides a simple REST API wrapper around Ollama, making it easy to access your LLM models from any device on your LAN.

## Features

- ✅ REST API wrapper for Ollama
- ✅ Streaming and non-streaming text generation
- ✅ Optional API key authentication
- ✅ Configuration management (file, env vars, CLI)
- ✅ Background daemon mode with process management
- ✅ Modern CLI with colorful output

## Installation

### From PyPI

```bash
pip install blakport
```

### From Source

```bash
git clone https://github.com/yourusername/blakport.git
cd blakport
pip install -e .
```

## Quick Start

### 1. Install and Initialize

```bash
pip install blakport
blakport init  # Optional: configure Ollama host/port
```

### 2. Start the Server

```bash
# Start (shows logs, then moves to background)
blakport start

# Start in background immediately
blakport start --background
```

### 3. Test the API

```bash
# Health check
curl http://localhost:8000/api/health

# Generate text
curl -X POST http://localhost:8000/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3",
    "prompt": "Why is the sky blue?",
    "stream": false
  }' | jq '.response'
```

## CLI Commands

### `blakport start`

Start the BlakPort server.

```bash
blakport start                    # Foreground (auto-moves to background)
blakport start --background       # Background immediately
blakport start --host 192.168.1.100 --port 11435  # Override Ollama config
```

### `blakport stop`

Stop the running server.

```bash
blakport stop
```

### `blakport status`

Show server status.

```bash
blakport status
```

### `blakport log`

View server logs.

```bash
blakport log              # Last 50 lines
blakport log --lines 100  # Last 100 lines
blakport log --follow     # Follow logs (tail -f)
```

### `blakport init` / `blakport configure`

Initialize or update configuration.

```bash
blakport init                          # Interactive setup
blakport init --host 127.0.0.1 --port 11434
blakport configure --host 192.168.1.100
```

## Configuration

Configuration priority (highest to lowest):
1. CLI arguments (`--host`, `--port`)
2. Environment variables (`OLLAMA_HOST`, `OLLAMA_PORT`)
3. Config file (`~/.config/blakport/config.toml`)
4. Defaults (host=127.0.0.1, port=11434)

### Config File

Located at `~/.config/blakport/config.toml`:

```toml
[ollama]
host = "127.0.0.1"
port = 11434
provider = "ollama"
```

### Environment Variables

```bash
export OLLAMA_HOST=127.0.0.1
export OLLAMA_PORT=11434
export HOST=0.0.0.0          # BlakPort listen address
export PORT=8000             # BlakPort server port
export API_KEY=your-key      # Optional: API key authentication
```

## API Reference

### Base URL

Default: `http://localhost:8000` (local) or `http://<your-ip>:8000` (LAN)

### Endpoints

#### `GET /api/health`

Health check endpoint.

```bash
curl http://localhost:8000/api/health
```

**Response:**
```json
{
  "status": "ok",
  "ollama_available": true,
  "ollama_url": "http://127.0.0.1:11434"
}
```

#### `GET /api/tags`

List available Ollama models.

```bash
curl http://localhost:8000/api/tags
```

#### `POST /api/generate`

Generate text using an Ollama model.

**Request:**
```json
{
  "model": "llama3",
  "prompt": "Why is the sky blue?",
  "stream": false,
  "options": {
    "temperature": 0.7,
    "top_p": 0.9
  }
}
```

**Response:**
```json
{
  "model": "llama3",
  "response": "The sky appears blue because of a phenomenon called Rayleigh scattering...",
  "done": true
}
```

**Streaming Mode:**
Set `"stream": true` to receive NDJSON stream.

## Examples

### Basic Text Generation

```bash
curl -s -X POST http://localhost:8000/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3",
    "prompt": "Why is the sky blue?",
    "stream": false
  }' | jq '.response'
```

### With API Key Authentication

```bash
# Start server with API key
export API_KEY=my-secret-key
blakport start

# Make authenticated request
curl -X POST http://localhost:8000/api/generate \
  -H "Content-Type: application/json" \
  -H "X-API-Key: my-secret-key" \
  -d '{"model": "llama3", "prompt": "Hello!", "stream": false}'
```

### Python Client

```python
import requests

response = requests.post(
    "http://localhost:8000/api/generate",
    json={
        "model": "llama3",
        "prompt": "Explain quantum computing",
        "stream": False
    }
)
print(response.json()["response"])
```

### LAN Access

```bash
# Find your LAN IP
ipconfig getifaddr en0  # macOS
hostname -I | awk '{print $1}'  # Linux

# Access from another device
curl http://192.168.0.24:8000/api/health
```

## Security

Enable API key authentication:

```bash
export API_KEY=your-secret-key
blakport start
```

All API requests (except `/` and `/api/health`) will require the `X-API-Key` header:

```bash
curl -H "X-API-Key: your-secret-key" http://localhost:8000/api/tags
```

## Troubleshooting

**Port already in use:**
```bash
PORT=8001 blakport start
```

**Cannot connect to Ollama:**
```bash
# Verify Ollama is running
curl http://127.0.0.1:11434/api/tags

# Update Ollama host/port
blakport configure --host 192.168.1.100 --port 11435
```

**Check logs:**
```bash
blakport log --follow
```

## License

MIT License - see [LICENSE](LICENSE) file for details.

---

<div align="center">

Made with ❤️ for the local LLM community

</div>
