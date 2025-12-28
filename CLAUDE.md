# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the **CustomGPT RAG for Claude Code** plugin (v1.1.0) - a Claude Code marketplace plugin that integrates CustomGPT.ai's enterprise-grade RAG capabilities directly into Claude Code via the Model Context Protocol (MCP).

**Goal**: Deploy to the official Claude Code App Marketplace (`claude-plugins-official`).

## Architecture

### Plugin Structure
```
customgpt/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest (name, version, description)
├── commands/
│   ├── setup.md              # /customgpt:setup - Connect knowledge base
│   ├── ask.md                # /customgpt:ask <question> - Query RAG directly
│   └── troubleshoot.md       # /customgpt:troubleshoot - Debug configuration
├── skills/
│   └── rag-retrieval/
│       └── SKILL.md          # Autonomous RAG retrieval skill
└── README.md                  # Marketplace documentation
```

Root-level files:
- `.mcp.json` - MCP server configuration (at repo root, not in plugin dir)
- `docs/` - Diátaxis documentation (QUICKSTART, HOWTO, REFERENCE, EXPLANATION)

### MCP Integration

The plugin connects to CustomGPT's Hosted MCP Server via SSE:
- **Endpoint**: `https://mcp.customgpt.ai/projects/<PROJECT_ID>/sse?token=<TOKEN>`
- **Tools**: 28 MCP tools including `query_customgpt_knowledge_base`, knowledge management, analytics, and settings

## Plugin Development

### Test plugin locally
```bash
claude --plugin-dir ./customgpt
```

### End-to-end test flow
```bash
# 1. Remove existing config (fresh test)
claude mcp remove customgpt

# 2. Load plugin and run setup
claude --plugin-dir ./customgpt
/customgpt:setup   # Paste MCP URL when prompted

# 3. Restart Claude Code, then verify
/mcp               # Should show customgpt with 28 tools

# 4. Test queries
/customgpt:ask "What topics are in my knowledge base?"
```

### Available commands
```
/customgpt:setup              # Interactive setup wizard
/customgpt:ask "question"     # Direct RAG query
/customgpt:troubleshoot       # Debug configuration
```

## Known Issues

1. **Env var expansion bug in plugin .mcp.json** - Workaround: users add MCP via `claude mcp add` during setup (not via plugin's .mcp.json)
2. **Restart required after setup** - MCP servers connect on Claude Code startup

## Distribution

**Repository**: `Poll-The-People/customgpt-claude-code`

```bash
# GitHub Marketplace install
/plugin marketplace add Poll-The-People/customgpt-claude-code
/plugin install customgpt@Poll-The-People-customgpt-claude-code
```

## Reference Documentation

- CustomGPT API: https://docs.customgpt.ai/
- CustomGPT MCP Server: https://docs.customgpt.ai/mcp
- Claude Code Plugins: https://docs.anthropic.com/en/docs/claude-code/plugins
- Claude Code MCP: https://docs.anthropic.com/en/docs/claude-code/mcp
