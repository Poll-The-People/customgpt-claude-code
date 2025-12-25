# CustomGPT RAG for Claude Code

Query your organization's knowledge bases directly from Claude Code. Get answers from runbooks, specs, policies, and documentation with citations.

## Installation

```bash
/plugin install customgpt@Poll-The-People-customgpt-claude-code
```

## Setup

1. **Get credentials from [app.customgpt.ai](https://app.customgpt.ai):**
   - API Key: Profile -> API Keys
   - Project ID: From agent URL (`/projects/12345`)
   - MCP Token: Agent -> Deploy -> MCP Server (Beta)

2. **Set environment variables:**
   ```bash
   export CUSTOMGPT_API_KEY="your-api-key"
   export CUSTOMGPT_PROJECT_ID="12345"
   export CUSTOMGPT_MCP_TOKEN="your-mcp-token"
   ```

3. **Configure MCP server:**
   ```bash
   jq --arg url "https://mcp.customgpt.ai/projects/$CUSTOMGPT_PROJECT_ID/sse?token=$CUSTOMGPT_MCP_TOKEN" \
     '.mcpServers.customgpt = {"type": "sse", "url": $url}' \
     ~/.claude.json > ~/.claude.json.tmp && mv ~/.claude.json.tmp ~/.claude.json
   ```
   Then restart Claude Code.

4. **Test:**
   ```bash
   /customgpt:ask "What topics are covered in this knowledge base?"
   ```

## Commands

| Command | Description |
|---------|-------------|
| `/customgpt:setup` | Interactive setup wizard |
| `/customgpt:ask <question>` | Query the knowledge base |
| `/customgpt:list-agents` | Show all available agents |
| `/customgpt:use-agent <id>` | Switch to a different agent |
| `/customgpt:troubleshoot` | Diagnose configuration issues |

## Features

- **Instant RAG queries** - Ask questions, get cited answers
- **Automatic retrieval** - Claude fetches docs when relevant
- **Multi-agent support** - Switch between knowledge bases
- **Enterprise security** - SOC-2 Type 2 certified

## Example

```
/customgpt:ask "What is our rate limiting policy?"
```

Response:
```
According to your API documentation:

Rate limits are configured as follows:
- Standard tier: 100 requests per minute
- Enterprise tier: 1,000 requests per minute

Source: api-guidelines.md (Section 4.2)
```

## Requirements

- Claude Code with plugin support
- CustomGPT.ai account ([free trial](https://app.customgpt.ai))
- At least one agent with indexed content

## Documentation

- [Quick Start](https://github.com/Poll-The-People/customgpt-claude-code/blob/main/docs/QUICKSTART.md)
- [How-To Guides](https://github.com/Poll-The-People/customgpt-claude-code/blob/main/docs/HOWTO.md)
- [Reference](https://github.com/Poll-The-People/customgpt-claude-code/blob/main/docs/REFERENCE.md)
- [CustomGPT Docs](https://docs.customgpt.ai)

## Support

- **Issues:** [GitHub Issues](https://github.com/Poll-The-People/customgpt-claude-code/issues)
- **Email:** hello@customgpt.ai
- **Docs:** [docs.customgpt.ai](https://docs.customgpt.ai)

## License

MIT License

---

Built by [CustomGPT.ai](https://customgpt.ai) - The #1 RAG API for Enterprise
