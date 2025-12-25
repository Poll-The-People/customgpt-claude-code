# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the **CustomGPT RAG for Claude Code** plugin project - a Claude Code marketplace plugin that integrates CustomGPT.ai's enterprise-grade RAG (Retrieval-Augmented Generation) capabilities directly into Claude Code via the Model Context Protocol (MCP).

**Goal**: Deploy to the official Claude Code App Marketplace (`claude-plugins-official`) to bring CustomGPT's industry-leading RAG to the Claude Code developer experience.

## Architecture

### Plugin Structure (Implemented)
```
customgpt/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest (name, version, description)
├── commands/
│   ├── setup.md              # /customgpt:setup - Full wizard (create OR connect agent)
│   ├── ask.md                # /customgpt:ask <question> - Query RAG directly
│   ├── use-agent.md          # /customgpt:use-agent - Switch active agent
│   ├── list-agents.md        # /customgpt:list-agents - Show all agents
│   └── troubleshoot.md       # /customgpt:troubleshoot - Debug configuration
├── skills/
│   └── rag-retrieval/
│       └── SKILL.md          # Autonomous RAG retrieval skill
├── .mcp.json                  # MCP server configuration for CustomGPT Hosted MCP
└── README.md                  # Marketplace documentation
```

### MCP Integration

The plugin connects to CustomGPT's Hosted MCP Server via SSE:
- **Endpoint**: `https://mcp.customgpt.ai/projects/<PROJECT_ID>/sse?token=<TOKEN>`
- **Primary Tool**: `send_message` - RAG search with citations
- **Advanced Tools** (plan-dependent): `upload_file`, `list_sources`

### CustomGPT API

The CustomGPT API (`https://app.customgpt.ai/api/v1/`) provides:
- **Agents** (projects): Knowledge bases built from sitemaps, files, or URLs
- **Conversations**: Chat sessions with RAG-powered responses
- **Pages**: Individual content sources within an agent
- **Sources**: Manage agent knowledge base content

Key endpoints for this plugin:
- `POST /api/v1/projects/{projectId}/conversations/{sessionId}/messages` - Send RAG query
- `POST /api/v1/projects/{projectId}/chat/completions` - OpenAI-compatible chat format
- `GET /api/v1/projects` - List available agents

## Environment Variables

```bash
CUSTOMGPT_API_KEY     # CustomGPT API bearer token (for REST API)
CUSTOMGPT_MCP_TOKEN   # MCP-specific token (for Hosted MCP Server)
CUSTOMGPT_PROJECT_ID  # Default agent/project ID
```

## Key Technical Decisions

1. **MCP over REST API**: Use CustomGPT's Hosted MCP Server for native Claude Code tool integration rather than wrapping REST calls
2. **Read-only MVP**: Start with `send_message` only; `upload_file` requires explicit opt-in
3. **Bearer token auth**: MCP token treated as secret, never persisted in repo
4. **Per-agent routing**: Support multiple CustomGPT agents (security-policies, platform-architecture, etc.)

## Plugin Development

### Test plugin locally
```bash
claude --plugin-dir ./customgpt
```

### Test commands
```
/customgpt:setup              # Interactive setup wizard
/customgpt:ask "question"     # Direct RAG query
/customgpt:list-agents        # Show all available agents
/customgpt:use-agent <id>     # Switch to different agent
/customgpt:troubleshoot       # Debug configuration
```

### Plugin manifest format (.claude-plugin/plugin.json)
```json
{
  "name": "customgpt",
  "description": "Enterprise RAG for Claude Code via CustomGPT Hosted MCP",
  "version": "1.0.0",
  "author": { "name": "CustomGPT.ai" },
  "keywords": ["rag", "knowledge-base", "docs", "enterprise", "mcp"]
}
```

### MCP config format (.mcp.json)
```json
{
  "mcpServers": {
    "customgpt": {
      "url": "https://mcp.customgpt.ai/projects/${CUSTOMGPT_PROJECT_ID}/sse?token=${CUSTOMGPT_MCP_TOKEN}",
      "transport": "sse"
    }
  }
}
```

## Distribution

**Target Repository**: `Poll-The-People/customgpt-claude-code`

### Track A: GitHub Marketplace (Immediate)
```bash
/plugin marketplace add Poll-The-People/customgpt-claude-code
/plugin install customgpt@Poll-The-People-customgpt-claude-code
```

### Track B: Official Anthropic Marketplace (Goal)
Submit to `claude-plugins-official` for zero-friction discovery via `/plugin → Discover`

## Reference Documentation

- CustomGPT API: https://docs.customgpt.ai/
- CustomGPT MCP Server: https://docs.customgpt.ai/mcp
- Claude Code Plugins: https://docs.anthropic.com/en/docs/claude-code/plugins
- Claude Code MCP: https://docs.anthropic.com/en/docs/claude-code/mcp
