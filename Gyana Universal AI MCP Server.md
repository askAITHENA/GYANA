# Gyana Universal AI MCP Server

A unified WebSocket-based MCP (Model Context Protocol) server that enables access to multiple AI providers (OpenAI, Anthropic, Gemini) through a single endpoint with secure access, usage tracking, and standardized request/response formatting.

## 🚀 Quick Start

**Endpoint:** `wss://api.askaithena.com/mcpserver/ai`

**WebSocket Connection:**
```bash
websocat wss://api.askaithena.com/mcpserver/ai
```

## 📋 Table of Contents

- [Claude Desktop Integration](#-claude-desktop-integration)
- [Direct WebSocket Integration](#-direct-websocket-integration)
- [Available AI Providers](#-available-ai-providers)
- [Supported API Methods](#-supported-api-methods)
- [Authentication & Usage](#-authentication--usage)
- [Integration Examples](#-integration-examples)
- [Error Handling](#-error-handling)
- [Pricing](#-pricing)

## 🖥️ Claude Desktop Integration

### macOS Setup

1. **Install websocat:**
```bash
brew install websocat
```

2. **Configure Claude Desktop:**

Edit `~/Library/Application Support/Claude/claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "gyana-universal-ai": {
      "command": "websocat",
      "args": ["wss://api.askaithena.com/mcpserver/ai"]
    }
  }
}
```

3. **Restart Claude Desktop** and look for Gyana tools in new conversations

### Windows Setup

1. Download websocat from the [releases page](https://github.com/vi/websocat/releases)
2. Configure Claude Desktop config file with the same JSON configuration
3. Restart Claude Desktop

## 🔌 Direct WebSocket Integration

### Connection Details

- **Protocol:** WebSocket Secure (WSS)
- **URL:** `wss://api.askaithena.com/mcpserver/ai`
- **Format:** JSON-RPC 2.0

### Python

```python
import asyncio
import websockets
import json

async def connect_gyana():
    uri = "wss://api.askaithena.com/mcpserver/ai"
    
    async with websockets.connect(uri) as websocket:
        # Initialize connection
        init_message = {
            "jsonrpc": "2.0",
            "method": "initialize",
            "params": {
                "protocolVersion": "2024-11-05",
                "capabilities": {},
                "clientInfo": {
                    "name": "your-client",
                    "version": "1.0.0"
                }
            },
            "id": 1
        }
        
        await websocket.send(json.dumps(init_message))
        response = await websocket.recv()
        print(json.loads(response))

asyncio.run(connect_gyana())
```

### JavaScript/Node.js

```javascript
const WebSocket = require('ws');

const ws = new WebSocket('wss://api.askaithena.com/mcpserver/ai');

ws.on('open', () => {
    ws.send(JSON.stringify({
        jsonrpc: "2.0",
        method: "initialize",
        params: {
            protocolVersion: "2024-11-05",
            capabilities: {},
            clientInfo: {
                name: "my-app",
                version: "1.0.0"
            }
        },
        id: 1
    }));
});

ws.on('message', (data) => {
    const response = JSON.parse(data);
    console.log('Received:', response);
});
```

### Browser

```javascript
const ws = new WebSocket('wss://api.askaithena.com/mcpserver/ai');

ws.onopen = () => {
    ws.send(JSON.stringify({
        jsonrpc: "2.0",
        method: "initialize",
        params: {
            protocolVersion: "2024-11-05",
            capabilities: {},
            clientInfo: {
                name: "browser-client",
                version: "1.0.0"
            }
        },
        id: 1
    }));
};

ws.onmessage = (event) => {
    const response = JSON.parse(event.data);
    console.log('Received:', response);
};
```

## 🤖 Available AI Providers

### OpenAI
- `gpt-4o` - Latest GPT-4 optimized model
- `gpt-4` - Standard GPT-4
- `gpt-4-turbo` - Faster GPT-4 variant
- `gpt-3.5-turbo` - Cost-effective option

### Anthropic
- `claude-3-5-sonnet-20241022` - Latest Claude Sonnet
- `claude-3-opus-20240229` - Most capable Claude model
- `claude-3-haiku-20240307` - Fastest Claude model

### Gemini
- `gemini-pro` - Standard Gemini model
- `gemini-pro-vision` - Gemini with vision capabilities

## 🛠️ Supported API Methods

### `simple_chat`
Basic single message chat with any supported AI provider

**Parameters:**
- `access_key` (required) - Your Universal AI access key
- `message` (required) - The message to send
- `provider` (optional) - AI provider: "openai", "anthropic", or "gemini"
- `model` (optional) - Specific model to use
- `temperature` (optional) - Response creativity (0-2)
- `max_tokens` (optional) - Maximum response length

### `chat_with_ai`
Multi-turn conversation with message history

**Parameters:**
- `access_key` (required) - Your Universal AI access key
- `messages` (required) - Array of conversation messages with role and content
- `provider` (optional) - AI provider to use
- `model` (optional) - Specific model to use
- `temperature` (optional) - Response creativity (0-2)
- `max_tokens` (optional) - Maximum response length

### `check_usage`
Monitor your API usage statistics and remaining calls

**Parameters:**
- `access_key` (required) - Your Universal AI access key

### `list_models`
View all available models across all providers

**Parameters:**
- None required

## 🔐 Authentication & Usage

### Access Keys
All API calls require a valid Universal AI access key (format: `uai_[key]`)

### Usage Limits
- **Free Tier:** 20 API calls per day
- **Paid Tier:** 1,000 API calls per day
- **Enterprise:** Custom limits with dedicated support

### Usage Tracking
Each successful or failed API call counts toward your daily limit. Use the `check_usage` tool to monitor remaining calls.

## 📝 Integration Examples

### Basic Chat
```python
message = {
    "jsonrpc": "2.0",
    "method": "tools/call",
    "params": {
        "name": "simple_chat",
        "arguments": {
            "access_key": "uai_your_key_here",
            "message": "Hello, how can you help me?",
            "provider": "openai",
            "model": "gpt-4o"
        }
    },
    "id": 1
}
```

### Multi-turn Conversation
```python
conversation_message = {
    "jsonrpc": "2.0",
    "method": "tools/call",
    "params": {
        "name": "chat_with_ai",
        "arguments": {
            "access_key": "uai_your_key_here",
            "messages": [
                {"role": "user", "content": "What is machine learning?"},
                {"role": "assistant", "content": "Machine learning is..."},
                {"role": "user", "content": "Can you give me an example?"}
            ],
            "provider": "anthropic",
            "model": "claude-3-5-sonnet-20241022"
        }
    },
    "id": 2
}
```

### Usage Monitoring
```python
usage_check = {
    "jsonrpc": "2.0",
    "method": "tools/call",
    "params": {
        "name": "check_usage",
        "arguments": {
            "access_key": "uai_your_key_here"
        }
    },
    "id": 3
}
```

### List Available Models
```python
list_models_request = {
    "jsonrpc": "2.0",
    "method": "tools/call",
    "params": {
        "name": "list_models",
        "arguments": {}
    },
    "id": 4
}
```

## ✨ Key Benefits

✅ Single endpoint for multiple AI providers  
✅ Built-in usage tracking and rate limiting  
✅ Standardized request/response format across all providers  
✅ Authentication and user management built-in  
✅ No need to manage multiple API keys - just one access key for all providers  
✅ WebSocket-based for real-time communication  
✅ MCP protocol compliance for seamless integration with MCP clients  
✅ Provider-agnostic - switch between AI providers without code changes

## ⚠️ Error Handling

The server provides detailed error responses for:
- Invalid access keys
- Usage limit exceeded
- Provider-specific errors
- Invalid model selection
- Malformed requests
- Network connectivity issues
- Rate limit violations

### Example Error Response
```json
{
    "jsonrpc": "2.0",
    "error": {
        "code": -32001,
        "message": "Usage limit exceeded",
        "data": {
            "limit": 20,
            "used": 20,
            "reset_time": "2025-09-26T00:00:00Z"
        }
    },
    "id": 1
}
```

## 🚀 Production Deployment

The Gyana Universal AI MCP Server runs on AWS EC2 infrastructure with:
- SSL/TLS encryption via nginx reverse proxy
- Domain: api.askaithena.com
- WebSocket endpoint: /mcpserver/ai
- High availability and monitoring
- Rate limiting and DDoS protection
- Automatic failover and load balancing

## 🔒 Security & Compliance

- End-to-end encryption for all communications
- API key authentication and authorization
- Request validation and sanitization
- Usage auditing and logging
- Regular security audits and updates

## 🎯 Getting Started

1. Sign up for a Gyana Universal AI account
2. Generate your access key from the dashboard
3. Configure your preferred integration method (MCP client, Python, JavaScript, or browser)
4. Start making API calls to any supported AI provider
5. Monitor your usage through the `check_usage` API method

## 💰 Pricing

### Free Tier
- 20 API calls per day
- Access to all AI providers
- Community support
- Basic usage analytics

### Paid Subscription
Coming soon!

### Enterprise - Custom Pricing
- Custom usage limits
- Dedicated support team
- SLA guarantees
- Volume discounts
- Dedicated account manager

Contact founder@askaithena.com for Enterprise pricing

## 📞 Contact

- **Website:** https://www.askaithena.com/univeralaimcpserver
- **Support:** founder@askaithena.com
- **Sales:** founder@askaithena.com

## 📚 Resources

Coming soon!
- Integration examples and documentation available
- Enterprise support for high-volume applications

---

**By askAITHENA** (brand owned by Consulting Nirmana 53462120A registered in Singapore)

*Universal AI access through a single MCP-compatible WebSocket endpoint*

© 2025 Gyana. All rights reserved.
