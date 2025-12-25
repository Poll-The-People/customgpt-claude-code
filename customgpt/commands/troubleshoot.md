---
description: Diagnose CustomGPT.ai connection issues
---

# CustomGPT.ai Troubleshoot

Help the user diagnose and fix connection issues with their CustomGPT.ai knowledge base.

## Step 1: Check MCP Server Status

Run this command to check if the CustomGPT.ai MCP server is configured:

```bash
claude mcp list 2>/dev/null | grep -i customgpt || echo "NOT_CONFIGURED"
```

### If NOT_CONFIGURED

Tell the user:
"The CustomGPT.ai MCP server is not configured. Please run `/customgpt:setup` to connect your knowledge base."

### If configured but showing errors

Tell the user:
"The MCP server is configured but may not be connected. Common fixes:
1. **Restart Claude Code** - MCP servers connect on startup
2. **Check your URL** - Make sure the MCP token hasn't expired
3. **Regenerate token** - Go to app.customgpt.ai → Your Agent → Deploy → MCP Server → Generate new token"

## Step 2: Test Connection (if configured)

If the MCP server is configured, try a simple query:

"Let me test the connection by querying your knowledge base..."

Use the `query_customgpt_knowledge_base` MCP tool with a simple test message like "Hello, what topics are in this knowledge base?"

### If successful

"**Connection verified!** Your CustomGPT.ai knowledge base is working. You can now use `/customgpt:ask <question>` to query it."

### If failed

"**Connection failed.** Please try:
1. Run `/customgpt:setup` again with a fresh MCP URL
2. Make sure your CustomGPT.ai agent is active
3. Check https://status.customgpt.ai for service status"

## Quick Fixes Reference

| Issue | Solution |
|-------|----------|
| MCP server not found | Run `/customgpt:setup` |
| Connection timeout | Restart Claude Code |
| Token expired | Regenerate MCP token at app.customgpt.ai |
| 401 Unauthorized | MCP URL is invalid, run setup again |

## Still Having Issues?

Contact CustomGPT.ai support:
- Documentation: https://docs.customgpt.ai
- Email: hello@customgpt.ai
