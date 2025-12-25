---
description: Connect your CustomGPT.ai knowledge base to Claude Code
---

# CustomGPT.ai Setup

You are helping the user connect their CustomGPT.ai knowledge base to Claude Code. This is a simple 2-step process.

## Step 1: Get the MCP URL

Ask the user to paste their CustomGPT.ai MCP Server URL:

"Please paste your **CustomGPT.ai MCP Server URL**.

To get it:
1. Go to https://app.customgpt.ai
2. Open your agent (knowledge base)
3. Click **Deploy** in the left sidebar
4. Click **MCP Server (Beta)**
5. Copy the **SSE URL**

It looks like: `https://mcp.customgpt.ai/projects/12345/sse?token=abc123...`"

## Step 2: Configure the MCP Server

When the user provides the URL:

1. **Validate the URL format** - It must match:
   - Starts with `https://mcp.customgpt.ai/projects/`
   - Contains a numeric project ID
   - Has `sse?token=` with a token value

2. **Run this command** to add the MCP server:
   ```bash
   claude mcp add --transport sse customgpt "<THE_URL_USER_PROVIDED>"
   ```

3. **Tell the user to restart Claude Code** for the MCP server to connect.

## Step 3: Confirm Success

After the user restarts Claude Code:

"**Setup complete!**

To verify, run `/mcp` - you should see `customgpt` listed as connected with 28 tools.

Try asking: *What topics are covered in my knowledge base?*"

## Troubleshooting

If the URL is invalid:
- "That doesn't look like a valid CustomGPT.ai MCP URL. Please make sure you copied the SSE URL from Deploy → MCP Server."

If the command fails:
- "There was an issue adding the MCP server. Please try running `/customgpt:troubleshoot` for diagnostics."
