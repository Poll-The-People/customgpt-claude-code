# CustomGPT.ai RAG for Claude Code

Query your knowledge base directly from Claude Code. Get answers from your runbooks, specs, policies, and documentation with citations.

## Setup (30 seconds)

1. **Get your MCP URL** from [app.customgpt.ai](https://app.customgpt.ai):
   - Open your agent
   - Click **Deploy** → **MCP Server (Beta)**
   - Copy the **SSE URL**

2. **Run setup** in Claude Code:
   ```
   /customgpt:setup
   ```
   Paste your URL when prompted.

3. **Restart Claude Code** for the MCP server to connect.

4. **Try it:**
   ```
   /customgpt:ask "What topics are covered in my knowledge base?"
   ```

## Commands

| Command | Description |
|---------|-------------|
| `/customgpt:setup` | Connect your knowledge base |
| `/customgpt:ask <question>` | Query your docs |
| `/customgpt:troubleshoot` | Check connection status |

## Features

- **Instant RAG queries** - Ask questions, get cited answers
- **Automatic retrieval** - Claude fetches docs when relevant
- **28 MCP tools** - Full CustomGPT.ai API access
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

## Need Help?

- [CustomGPT.ai Docs](https://docs.customgpt.ai)
- [GitHub Issues](https://github.com/Poll-The-People/customgpt-claude-code/issues)
- Email: hello@customgpt.ai

## License

MIT License - Built by [CustomGPT.ai](https://customgpt.ai)
