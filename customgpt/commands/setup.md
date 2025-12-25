---
description: Configure CustomGPT RAG integration - connect existing agents or create new ones
---

# CustomGPT Setup Wizard

You are helping the user configure the CustomGPT RAG plugin for Claude Code. This setup supports both new users (who need to create an agent) and existing users (who want to connect their agents).

## Step 1: Check Prerequisites

First, verify the user has:
1. A CustomGPT.ai account at https://app.customgpt.ai (free trial available)
2. An API key for REST operations

Ask the user: "Do you have a CustomGPT.ai account? If not, you can sign up at https://app.customgpt.ai"

## Step 2: Get API Key

Guide the user to get their REST API key:
1. Log in to https://app.customgpt.ai
2. Click on their profile icon → API Keys
3. Generate or copy an existing API key

Have them set the environment variable:
```bash
export CUSTOMGPT_API_KEY="<their-api-key>"
```

## Step 3: Check for Existing Agents

Use the Bash tool to list existing agents:

```bash
curl -s -X GET "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Accept: application/json" | jq '.data.data[] | {id, project_name, created_at}'
```

### Path A: User Has Existing Agents

If agents exist, display them in a table format:

| ID | Name | Created |
|----|------|---------|
| ... | ... | ... |

Ask: "Which agent would you like to use? Enter the ID or name."

Once selected, proceed to Step 4.

### Path B: User Needs to Create an Agent

If no agents exist, guide the user through creation:

**Option 1: Create from Sitemap**
```bash
curl -s -X POST "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Content-Type: multipart/form-data" \
  -F "project_name=My Knowledge Base" \
  -F "sitemap_path=https://example.com/sitemap.xml"
```

**Option 2: Create from URL**
Ask the user for a documentation URL or sitemap URL to index.

After creation, note the returned `id` field - this is the Project ID.

## Step 4: Enable MCP Server and Get Token

Guide the user to enable the MCP Server for their agent:

1. Go to https://app.customgpt.ai/projects/{projectId}
2. Click **Deploy** in the left sidebar
3. Click **MCP Server (Beta)**
4. Click **Generate MCP Token**
5. Copy the generated token

Have them set the environment variables:
```bash
export CUSTOMGPT_PROJECT_ID="<project-id>"
export CUSTOMGPT_MCP_TOKEN="<mcp-token>"
```

## Step 5: Configure MCP Server

Due to a known issue with environment variable expansion in plugin configs, we need to add the MCP server to the user's global Claude config.

Run this command to add the CustomGPT MCP server to `~/.claude.json`:

```bash
# Read existing config, add MCP server, write back
CLAUDE_CONFIG=~/.claude.json
PROJECT_ID="$CUSTOMGPT_PROJECT_ID"
MCP_TOKEN="$CUSTOMGPT_MCP_TOKEN"

# Check if file exists and has mcpServers
if [ -f "$CLAUDE_CONFIG" ]; then
  # Add or update the customgpt MCP server
  jq --arg url "https://mcp.customgpt.ai/projects/$PROJECT_ID/sse?token=$MCP_TOKEN" \
    '.mcpServers.customgpt = {"type": "sse", "url": $url}' \
    "$CLAUDE_CONFIG" > "$CLAUDE_CONFIG.tmp" && mv "$CLAUDE_CONFIG.tmp" "$CLAUDE_CONFIG"
  echo "✅ Added CustomGPT MCP server to ~/.claude.json"
else
  # Create new config with MCP server
  echo "{\"mcpServers\":{\"customgpt\":{\"type\":\"sse\",\"url\":\"https://mcp.customgpt.ai/projects/$PROJECT_ID/sse?token=$MCP_TOKEN\"}}}" | jq . > "$CLAUDE_CONFIG"
  echo "✅ Created ~/.claude.json with CustomGPT MCP server"
fi
```

**Important:** After running this, the user must restart Claude Code for the MCP server to connect.

## Step 6: Verify Configuration

Test the connection by making a simple query:

Use the `mcp__customgpt__send_message` tool or `query_customgpt_knowledge_base` tool with a test message.

If successful, display the response and confirm setup is complete.

## Step 7: Confirm Success

Once verified, inform the user:

**Setup Complete!**

You can now:
- Use `/customgpt:ask <question>` for direct queries
- Claude will automatically use RAG when you ask about documented topics
- Use `/customgpt:list-agents` to see all your agents
- Use `/customgpt:use-agent <id>` to switch agents

## Troubleshooting

If any step fails:
- **API key invalid**: Regenerate at Profile → API Keys
- **No agents found**: Create one using a sitemap or file upload
- **MCP token issues**: Regenerate at Deploy → MCP Server
- **Connection failed**: Run `/customgpt:troubleshoot` for diagnostics
