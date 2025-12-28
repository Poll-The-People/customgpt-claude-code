---
type: reference
audience: developers
goal: Complete specification of all commands and configuration
owner: CustomGPT.ai
last_verified: 2025-12-25
---

# Reference

Complete reference for CustomGPT.ai RAG for Claude Code.

---

## Table of Contents

1. [Commands](#commands)
2. [MCP Configuration](#mcp-configuration)
3. [Plugin Manifest](#plugin-manifest)
4. [MCP Tools](#mcp-tools)
5. [Error Codes](#error-codes)

---

## Commands

### /customgpt:setup

**Description:** Connect your CustomGPT.ai knowledge base to Claude Code.

**Syntax:**
```
/customgpt:setup
```

**Arguments:** None

**Behavior:**
1. Asks for your MCP Server URL
2. Validates the URL format
3. Adds the MCP server to Claude Code
4. Instructs you to restart Claude Code

**Example:**
```
/customgpt:setup

> Please paste your CustomGPT.ai MCP Server URL.
>
> To get it:
> 1. Go to https://app.customgpt.ai
> 2. Open your agent
> 3. Click Deploy → MCP Server - BETA
> 4. Click the SSE tab
> 5. Copy the full URL

[User pastes URL]

> ✅ MCP server configured. Please restart Claude Code.
```

---

### /customgpt:ask

**Description:** Query the knowledge base directly with a question.

**Syntax:**
```
/customgpt:ask <question>
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| question | Yes | The question to ask |

**Returns:**
- Answer generated from indexed content
- Source citations with document names

**Example:**
```
/customgpt:ask "What is our rate limiting policy?"

> According to your API documentation:
>
> Rate limits are:
> - Standard: 100 requests per minute
> - Enterprise: 1,000 requests per minute
>
> Source: api-guidelines.md (Section 4.2)
```

---

### /customgpt:troubleshoot

**Description:** Diagnose connection issues.

**Syntax:**
```
/customgpt:troubleshoot
```

**Arguments:** None

**Behavior:**
1. Checks if MCP server is configured
2. Tests the connection
3. Provides resolution steps if issues found

---

## MCP Configuration

The CustomGPT.ai MCP server is configured using the `claude mcp add` command.

**Adding the server:**
```bash
claude mcp add --transport sse customgpt "https://mcp.customgpt.ai/projects/12345/sse?token=abc..."
```

**Removing the server:**
```bash
claude mcp remove customgpt
```

**Listing configured servers:**
```bash
claude mcp list
```

**Configuration location:** `~/.claude.json`

---

## Plugin Manifest

**Location:** `customgpt/.claude-plugin/plugin.json`

```json
{
  "name": "customgpt",
  "description": "Enterprise RAG for Claude Code via CustomGPT.ai MCP Server",
  "version": "1.0.0",
  "author": {
    "name": "CustomGPT.ai",
    "url": "https://customgpt.ai"
  },
  "repository": "https://github.com/Poll-The-People/customgpt-claude-code",
  "license": "MIT"
}
```

---

## MCP Tools

When connected, the CustomGPT.ai MCP server provides 28 tools:

### Core Query Tools

| Tool | Description |
|------|-------------|
| `query_customgpt_knowledge_base` | Query the knowledge base |
| `get_customgpt_conversations` | List conversations |
| `get_customgpt_conversation` | Get conversation details |

### Document Tools

| Tool | Description |
|------|-------------|
| `get_customgpt_knowledge_documents` | List indexed documents |
| `get_customgpt_knowledge_sources` | List knowledge sources |

### Management Tools

| Tool | Description |
|------|-------------|
| `delete_customgpt_conversation` | Delete a conversation |

For complete tool documentation, see [docs.customgpt.ai](https://docs.customgpt.ai).

---

## Error Codes

| Error | Cause | Solution |
|-------|-------|----------|
| `401 Unauthorized` | Invalid or expired MCP token | Get new URL from app.customgpt.ai |
| `404 Not Found` | Project doesn't exist | Verify project ID in URL |
| `Connection timeout` | MCP server unreachable | Restart Claude Code |
| `No results` | No matching content | Verify agent has indexed content |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.1.0 | 2025-12-25 | Simplified setup (paste URL), removed env vars |
| 1.0.0 | 2025-12-24 | Initial release |

---

## Related Documentation

- [Quick Start](QUICKSTART.md) - 2-minute setup guide
- [How-To Guides](HOWTO.md) - Task-oriented guides
- [CustomGPT.ai Docs](https://docs.customgpt.ai) - Full API reference
