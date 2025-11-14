# BlakPort

<div align="center">

**Fast, lightweight FastAPI wrapper for exposing Ollama server to your local network**

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104+-green.svg)](https://fastapi.tiangolo.com/)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

[Features](#features) • [Installation](#installation) • [Quick Start](#quick-start) • [Configuration](#configuration) • [API Reference](#api-reference) • [Examples](#examples)

</div>

---

## Overview

BlakPort is a lightweight API gateway that exposes your local Ollama server to devices on your local network. It provides a simple REST API wrapper around Ollama, making it easy to access your LLM models from any device on your LAN without exposing Ollama directly.

### Why BlakPort?

- 🚀 **Easy Setup** - Install and run in seconds
- 🔒 **Optional Security** - API key authentication support
- 🌐 **LAN Access** - Access your models from any device on your network
- ⚙️ **Flexible Configuration** - Config file, environment variables, or CLI overrides
- 🎨 **Modern CLI** - Beautiful, colorful command-line interface
- 📦 **Production Ready** - Background daemon mode with process management

## Features

- ✅ REST API wrapper for Ollama
- ✅ Streaming and non-streaming text generation
- ✅ Health check and model listing endpoints
- ✅ Optional API key authentication
- ✅ Configuration management (file, env vars, CLI)
- ✅ Background daemon mode
- ✅ Process management (start, stop, status, logs)
- ✅ Colorful CLI with modern styling
- ✅ Cross-platform support (macOS, Linux, Windows)

## Installation

### From PyPI (Recommended)

```bash
pip install blakport
```

### From Source

```bash
# Clone the repository
git clone https://github.com/yourusername/blakport.git
cd blakport

# Install in development mode
pip install -e .

# Or install dependencies manually
pip install -r requirements.txt
```

### Requirements

- Python 3.8 or higher
- Ollama server running locally or on your network

## Quick Start

### 1. Install BlakPort

```bash
pip install blakport
```

### 2. Initialize Configuration (Optional)

```bash
# Interactive setup
blakport init

# Or with CLI arguments
blakport init --host 127.0.0.1 --port 11434
```

### 3. Start the Server

```bash
# Start in foreground (shows logs, then moves to background)
blakport start

# Start in background immediately
blakport start --background

# Start with custom Ollama host/port
blakport start --host 192.168.1.100 --port 11435
```

### 4. Check Status

```bash
blakport status
```

### 5. Test the API

```bash
# Health check
curl http://localhost:8000/api/health

# List available models
curl http://localhost:8000/api/tags

# Generate text
curl -X POST http://localhost:8000/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3",
    "prompt": "Why is the sky blue?",
    "stream": false
  }'
```

## CLI Commands

BlakPort provides a modern CLI with colorful output and intuitive commands:

### `blakport start`

Start the BlakPort server.

```bash
# Start in foreground (shows startup logs, then moves to background)
blakport start

# Start in background immediately
blakport start --background

# Override Ollama configuration
blakport start --host 192.168.1.100 --port 11435

# Options:
#   --background, -b    Start server in background
#   --host TEXT         Ollama server host (overrides config/env)
#   --port INTEGER      Ollama server port (overrides config/env)
#   --provider TEXT     Provider name (default: ollama)
```

**Example Output:**
```
🚀 Starting BlakPort server...
  Ollama: http://127.0.0.1:11434
  Auth: disabled

⏳ Waiting for server to start...
✓ Server is ready on http://localhost:8000
📦 Moving to background...

✓ BlakPort is running in background
  PID: 10494
  Logs: ~/.blakport/blakport.log
  Status: blakport status
  Stop: blakport stop
```

### `blakport stop`

Stop the running BlakPort server.

```bash
blakport stop
```

**Example Output:**
```
🛑 Stopping BlakPort server...
✓ BlakPort stopped (PID: 10494)
```

### `blakport status`

Show the current status of the BlakPort server.

```bash
blakport status
```

**Example Output:**
```
Status: Running
  PID: 10494
  URL: http://localhost:8000
  Host: 0.0.0.0
  Port: 8000
  Logs: /Users/blak/.blakport/blakport.log
```

### `blakport log`

View server logs.

```bash
# Show last 50 lines
blakport log

# Show last 100 lines
blakport log --lines 100

# Follow logs (like tail -f)
blakport log --follow

# Options:
#   --follow, -f        Follow log file (like tail -f)
#   --lines, -n INT     Number of lines to show (default: 50)
```

**Example Output:**
```
📋 Last 50 lines from /Users/blak/.blakport/blakport.log

  INFO:     Started server process [10494]
  INFO:     Waiting for application startup.
  INFO:     Application startup complete.
  INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
```

### `blakport init`

Initialize or update the BlakPort configuration file.

```bash
# Interactive mode
blakport init

# With CLI arguments
blakport init --host 127.0.0.1 --port 11434 --provider ollama
```

### `blakport configure`

Configure BlakPort settings interactively or via CLI args.

```bash
# Interactive mode (prompts for missing values)
blakport configure

# Update specific settings
blakport configure --host 192.168.1.100 --port 11435
```

## Configuration

BlakPort supports multiple configuration methods with priority order:

**Priority (highest to lowest):**
1. CLI arguments (`--host`, `--port`)
2. Environment variables (`OLLAMA_HOST`, `OLLAMA_PORT`)
3. Config file (`~/.config/blakport/config.toml`)
4. Defaults (host=127.0.0.1, port=11434)

### Configuration File

The configuration file is located at `~/.config/blakport/config.toml` (or `%APPDATA%\blakport\config.toml` on Windows).

**Example `config.toml`:**
```toml
[ollama]
host = "127.0.0.1"
port = 11434
provider = "ollama"
```

### Environment Variables

```bash
# Ollama server configuration
export OLLAMA_HOST=127.0.0.1
export OLLAMA_PORT=11434

# BlakPort server configuration
export HOST=0.0.0.0          # Listen address (0.0.0.0 = all interfaces)
export PORT=8000             # BlakPort server port

# Optional: API key for authentication
export API_KEY=your-secret-key-here

# Optional: Logging level
export LOG_LEVEL=INFO
```

### CLI Overrides

You can override any configuration via CLI arguments:

```bash
blakport start --host 192.168.1.100 --port 11435
```

## API Reference

### Base URL

By default, BlakPort runs on `http://0.0.0.0:8000`. Access it via:
- `http://localhost:8000` (local)
- `http://<your-ip>:8000` (LAN access)

### Endpoints

#### `GET /`

Root endpoint with API information.

**Response:**
```json
{
  "service": "BlakPort",
  "version": "1.0.0",
  "ollama_url": "http://127.0.0.1:11434",
  "endpoints": {
    "health": "/api/health",
    "list_models": "/api/tags",
    "generate": "/api/generate"
  },
  "authentication": "disabled"
}
```

#### `GET /api/health`

Health check endpoint. Returns server status and Ollama availability.

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

**Headers (if API key is enabled):**
```
X-API-Key: your-secret-key
```

**Response:**
```json
{
  "models": [
    {
      "name": "llama3",
      "modified_at": "2024-01-01T00:00:00Z",
      "size": 4765380224,
      "digest": "sha256:..."
    }
  ]
}
```

#### `POST /api/generate`

Generate text using an Ollama model. Supports streaming and non-streaming modes.

**Headers:**
```
Content-Type: application/json
X-API-Key: your-secret-key  # Required if API key is enabled
```

**Request Body:**
```json
{
  "model": "llama3",
  "prompt": "Why is the sky blue?",
  "stream": false,
  "options": {
    "temperature": 0.7,
    "top_p": 0.9,
    "top_k": 40
  }
}
```

**Response (non-streaming):**
```json
{
  "model": "llama3",
  "created_at": "2024-01-01T00:00:00Z",
  "response": "The sky appears blue because of a phenomenon called Rayleigh scattering...",
  "done": true,
  "context": [1, 2, 3],
  "total_duration": 1234567890,
  "load_duration": 123456,
  "prompt_eval_count": 10,
  "prompt_eval_duration": 123456,
  "eval_count": 100,
  "eval_duration": 1234567890
}
```

**Response (streaming):**
Returns NDJSON (newline-delimited JSON) stream:
```
{"model":"llama3","created_at":"...","response":"The","done":false}
{"model":"llama3","created_at":"...","response":" sky","done":false}
{"model":"llama3","created_at":"...","response":" appears","done":false}
...
{"model":"llama3","created_at":"...","response":"","done":true}
```

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

**Output:**
```
The sky appears blue because of a phenomenon called Rayleigh scattering, named after the British physicist Lord Rayleigh. Here's what happens:

1. **Sunlight**: When sunlight enters Earth's atmosphere, it contains all the colors of the visible spectrum...
```

### Streaming Generation

```bash
curl -X POST http://localhost:8000/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3",
    "prompt": "Write a short poem about AI",
    "stream": true
  }'
```

### With API Key Authentication

```bash
# Start server with API key
export API_KEY=my-secret-key-123
blakport start

# Make authenticated request
curl -X POST http://localhost:8000/api/generate \
  -H "Content-Type: application/json" \
  -H "X-API-Key: my-secret-key-123" \
  -d '{
    "model": "llama3",
    "prompt": "Hello, world!",
    "stream": false
  }'
```

### LAN Access

```bash
# Find your LAN IP (macOS)
ipconfig getifaddr en0

# Find your LAN IP (Linux)
hostname -I | awk '{print $1}'

# Access from another device on the network
curl http://192.168.0.24:8000/api/health
```

### Python Client Example

```python
import requests

# Health check
response = requests.get("http://localhost:8000/api/health")
print(response.json())

# Generate text
response = requests.post(
    "http://localhost:8000/api/generate",
    json={
        "model": "llama3",
        "prompt": "Explain quantum computing in simple terms",
        "stream": False
    }
)
print(response.json()["response"])
```

### JavaScript/Node.js Example

```javascript
const response = await fetch('http://localhost:8000/api/generate', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    model: 'llama3',
    prompt: 'Write a haiku about programming',
    stream: false
  })
});

const data = await response.json();
console.log(data.response);
```

## Security

### API Key Authentication

Enable API key authentication by setting the `API_KEY` environment variable:

```bash
export API_KEY=your-secret-key-here
blakport start
```

All API requests (except `/` and `/api/health`) will require the `X-API-Key` header:

```bash
curl -H "X-API-Key: your-secret-key-here" \
  http://localhost:8000/api/tags
```

### Network Security

- BlakPort listens on `0.0.0.0` by default, making it accessible on your LAN
- For production use, consider:
  - Using a firewall to restrict access
  - Running behind a reverse proxy (nginx, Caddy)
  - Using VPN for remote access
  - Enabling API key authentication

## Running as a Service

### systemd (Linux)

Create `/etc/systemd/system/blakport.service`:

```ini
[Unit]
Description=BlakPort Ollama API Gateway
After=network.target

[Service]
Type=simple
User=your-username
WorkingDirectory=/home/your-username
Environment="OLLAMA_HOST=127.0.0.1"
Environment="OLLAMA_PORT=11434"
Environment="PORT=8000"
Environment="API_KEY=your-secret-key"
ExecStart=/usr/local/bin/blakport start --background
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable blakport
sudo systemctl start blakport
sudo systemctl status blakport
```

### launchd (macOS)

Create `~/Library/LaunchAgents/com.blakport.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.blakport</string>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/local/bin/blakport</string>
        <string>start</string>
        <string>--background</string>
    </array>
    <key>EnvironmentVariables</key>
    <dict>
        <key>OLLAMA_HOST</key>
        <string>127.0.0.1</string>
        <key>OLLAMA_PORT</key>
        <string>11434</string>
        <key>PORT</key>
        <string>8000</string>
        <key>API_KEY</key>
        <string>your-secret-key</string>
    </dict>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
    <key>StandardOutPath</key>
    <string>/Users/your-username/.blakport/blakport.log</string>
    <key>StandardErrorPath</key>
    <string>/Users/your-username/.blakport/blakport.log</string>
</dict>
</plist>
```

Load the service:

```bash
launchctl load ~/Library/LaunchAgents/com.blakport.plist
launchctl start com.blakport
```

## Troubleshooting

### Port Already in Use

```bash
# Check what's using port 8000
lsof -i :8000  # macOS/Linux
netstat -ano | findstr :8000  # Windows

# Use a different port
PORT=8001 blakport start
```

### Cannot Connect to Ollama

```bash
# Verify Ollama is running
curl http://127.0.0.1:11434/api/tags

# Check Ollama configuration
blakport status

# Update Ollama host/port if needed
blakport configure --host 192.168.1.100 --port 11435
```

### Connection Refused from LAN

1. **Verify BlakPort is listening on all interfaces:**
   ```bash
   blakport status
   # Should show Host: 0.0.0.0 (not 127.0.0.1)
   ```

2. **Check firewall settings:**
   ```bash
   # macOS
   sudo pfctl -sr | grep 8000
   
   # Linux
   sudo ufw status
   sudo firewall-cmd --list-all
   ```

3. **Ensure devices are on the same network**

4. **Check BlakPort logs:**
   ```bash
   blakport log --follow
   ```

### Server Won't Start

```bash
# Check logs
blakport log

# Check if process is already running
blakport status

# Stop any existing processes
blakport stop

# Try starting again
blakport start
```

### Permission Errors

```bash
# Ensure you have write permissions for config/log directories
ls -la ~/.blakport/
ls -la ~/.config/blakport/

# Fix permissions if needed
chmod 755 ~/.blakport
chmod 755 ~/.config/blakport
```

## Development

### Setup Development Environment

```bash
# Clone repository
git clone https://github.com/yourusername/blakport.git
cd blakport

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install in development mode
pip install -e ".[dev]"

# Install development dependencies
pip install -r requirements.txt
```

### Running Tests

```bash
# Run tests (when test suite is added)
pytest

# Run with coverage
pytest --cov=main --cov-report=html
```

### Code Style

```bash
# Format code
black main.py

# Lint code
ruff check main.py
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Guidelines

- Follow PEP 8 style guide
- Add type hints where appropriate
- Write docstrings for functions and classes
- Update README.md for new features
- Test your changes before submitting

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Acknowledgments

- Built with [FastAPI](https://fastapi.tiangolo.com/)
- Inspired by the need for easy LAN access to Ollama
- Thanks to all contributors and users

---

<div align="center">

Made with ❤️ for the local LLM community

[Report Bug](https://github.com/yourusername/blakport/issues) • [Request Feature](https://github.com/yourusername/blakport/issues) • [Documentation](https://github.com/yourusername/blakport#readme)

</div>
