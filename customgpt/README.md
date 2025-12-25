# CustomGPT RAG for Claude Code

Enterprise-grade RAG (Retrieval-Augmented Generation) for Claude Code, powered by [CustomGPT.ai](https://customgpt.ai)'s Hosted MCP Server.

**Query your organization's knowledge bases directly from Claude Code** - runbooks, specs, policies, documentation - with industry-leading accuracy and citation support.

## Features

- **Instant RAG queries**: `/customgpt:ask <question>` for direct knowledge base search
- **Automatic retrieval**: Claude autonomously queries your docs when relevant
- **Citation support**: Every answer includes source references for traceability
- **Multi-agent support**: Switch between different knowledge bases easily
- **Enterprise security**: SOC-2 Type 2 certified infrastructure

## Prerequisites

- A CustomGPT.ai account ([free trial available](https://app.customgpt.ai))
- At least one agent with indexed content (or create one during setup)
- MCP Server access (available on all plans)

## Installation

From the Claude Code marketplace:
```bash
/plugin install customgpt@Poll-The-People-customgpt-claude-code
```

Or add the marketplace first:
```bash
/plugin marketplace add Poll-The-People/customgpt-claude-code
/plugin install customgpt
```

For local development:
```bash
claude --plugin-dir ./customgpt
```

## Quick Start

### 1. Run Setup

```bash
/customgpt:setup
```

This interactive wizard will:
- Check for existing CustomGPT agents
- Help you create a new agent if needed
- Configure your MCP connection

### 2. Set Environment Variables

After setup, ensure these are set:

```bash
export CUSTOMGPT_API_KEY="your-api-key"        # From Profile → API Keys
export CUSTOMGPT_PROJECT_ID="your-project-id"  # From agent URL
export CUSTOMGPT_MCP_TOKEN="your-mcp-token"    # From Deploy → MCP Server
```

### 3. Test the Connection

```bash
/customgpt:ask "What topics are covered in this knowledge base?"
```

## Commands

| Command | Description |
|---------|-------------|
| `/customgpt:setup` | Interactive setup wizard - create or connect agents |
| `/customgpt:ask <question>` | Query the knowledge base directly |
| `/customgpt:list-agents` | Show all available agents in your account |
| `/customgpt:use-agent <id>` | Switch to a different agent/knowledge base |
| `/customgpt:troubleshoot` | Diagnose configuration issues |

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `CUSTOMGPT_API_KEY` | Yes | REST API key for listing/creating agents |
| `CUSTOMGPT_PROJECT_ID` | Yes | Numeric ID of the active agent |
| `CUSTOMGPT_MCP_TOKEN` | Yes | MCP bearer token for RAG queries |

### Getting Your Credentials

1. **API Key**: https://app.customgpt.ai → Profile → API Keys
2. **Project ID**: Found in agent URL: `app.customgpt.ai/projects/12345`
3. **MCP Token**: Agent → Deploy → MCP Server (Beta) → Generate Token

## How It Works

This plugin connects Claude Code to CustomGPT's Hosted MCP Server via Server-Sent Events (SSE).

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│   Claude Code   │────▶│  CustomGPT MCP   │────▶│ Your Knowledge  │
│   (your IDE)    │◀────│     Server       │◀────│      Base       │
└─────────────────┘     └──────────────────┘     └─────────────────┘
```

When you query:
1. Your question is sent to the CustomGPT RAG engine
2. Relevant content is retrieved from your indexed knowledge base
3. An answer is generated with citations
4. Results are returned to Claude Code

### Automatic RAG

Claude will automatically invoke RAG when you ask about:
- Company policies, procedures, or guidelines
- Internal documentation or runbooks
- Security and compliance requirements
- Technical specifications or architecture decisions

## Creating Knowledge Bases

### From a Sitemap
```bash
curl -X POST "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -F "project_name=My Docs" \
  -F "sitemap_path=https://docs.example.com/sitemap.xml"
```

### From the Dashboard
1. Visit https://app.customgpt.ai/projects
2. Click "Create Agent"
3. Add a sitemap URL or upload documents
4. Wait for indexing to complete
5. Enable MCP Server in Deploy settings

## Security

- **Tokens are secrets** - Never commit to version control
- **TLS encryption** - All traffic is encrypted
- **No data sharing** - Your data is accessed only via secure endpoints
- **SOC-2 certified** - CustomGPT.ai is SOC-2 Type 2 certified

### Best Practices

```bash
# Store tokens in .env file (add to .gitignore)
echo "CUSTOMGPT_API_KEY=..." >> .env
echo "CUSTOMGPT_MCP_TOKEN=..." >> .env

# Or use a secrets manager
export CUSTOMGPT_API_KEY=$(aws secretsmanager get-secret-value ...)
```

## Troubleshooting

Run the diagnostic command:
```bash
/customgpt:troubleshoot
```

### Common Issues

| Issue | Solution |
|-------|----------|
| "Token invalid" | Regenerate at Deploy → MCP Server |
| "Project not found" | Verify CUSTOMGPT_PROJECT_ID matches your agent |
| "No results found" | Check that your agent has indexed content |
| "Connection timeout" | Ensure MCP Server is enabled for your agent |

## Examples

### Query Internal Documentation
```
/customgpt:ask "How do I deploy to production?"
```

### Ask About Policies
```
/customgpt:ask "What's our data retention policy?"
```

### Get Architecture Details
```
/customgpt:ask "How is the authentication service structured?"
```

### Automatic Context (via Skill)
Just ask naturally - Claude will fetch relevant docs:
```
"Update this code to follow our error handling guidelines"
```

## Support

- **Documentation**: https://docs.customgpt.ai
- **MCP Reference**: https://docs.customgpt.ai/reference/customgptai-mcp-support
- **Email**: hello@customgpt.ai
- **Issues**: https://github.com/Poll-The-People/customgpt-claude-code/issues

## License

MIT License - see [LICENSE](LICENSE) for details.

---

Built with ❤️ by [CustomGPT.ai](https://customgpt.ai) - The #1 RAG API
