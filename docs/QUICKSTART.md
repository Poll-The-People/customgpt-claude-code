---
type: tutorial
audience: developers
goal: Connect your CustomGPT.ai knowledge base to Claude Code
timebox: 2
owner: CustomGPT.ai
last_verified: 2025-12-25
---

# Quick Start: Connect Your Knowledge Base

**Goal:** Go from zero to querying your documentation in 2 minutes.

**What you'll need:**
- Claude Code installed
- A CustomGPT.ai account with at least one agent ([free trial](https://app.customgpt.ai))

---

## Step 1: Get Your MCP URL

1. Go to [app.customgpt.ai](https://app.customgpt.ai)
2. Open your agent (knowledge base)
3. Click **Deploy** in the left sidebar
4. Click **MCP Server (Beta)**
5. Copy the **SSE URL**

It looks like:
```
https://mcp.customgpt.ai/projects/12345/sse?token=abc123def456...
```

---

## Step 2: Run Setup

In Claude Code, run:

```
/customgpt:setup
```

When prompted, paste your MCP URL.

---

## Step 3: Restart Claude Code

Exit Claude Code and start it again. The MCP server connects on startup.

---

## Step 4: Try It

Ask a question about your knowledge base:

```
/customgpt:ask "What topics are covered in my knowledge base?"
```

**Expected output:**
```
Based on your documentation:

This knowledge base covers:
- Deployment procedures
- API authentication
- Database migrations
- Error handling

Sources:
- index.md (Introduction)
- deployment/overview.md
```

---

## You're Done!

Your CustomGPT.ai knowledge base is now connected to Claude Code.

**Next steps:**
- Ask natural questions - Claude will automatically query your docs when relevant
- Run `/customgpt:troubleshoot` if you have connection issues
- Check the [How-To Guides](HOWTO.md) for advanced usage

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| MCP server not connecting | Restart Claude Code |
| Token expired | Get a new MCP URL from app.customgpt.ai |
| No results | Make sure your agent has indexed content |

Need help? [docs.customgpt.ai](https://docs.customgpt.ai) or email hello@customgpt.ai
