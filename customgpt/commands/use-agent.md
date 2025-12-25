---
description: Switch to a different CustomGPT agent/knowledge base
---

# Switch CustomGPT Agent

You are helping the user switch to a different CustomGPT agent (knowledge base).

## Arguments

The requested agent (name or ID): **$ARGUMENTS**

## Execution Steps

### Step 1: List Available Agents

First, fetch the list of available agents using the REST API:

```bash
curl -s -X GET "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Accept: application/json" | jq '.data.data[] | {id, project_name, created_at}'
```

Display agents in a table:

| ID | Name | Created | Status |
|----|------|---------|--------|
| 12345 | Engineering Docs | 2024-12-01 | Active |
| 12346 | Security Policies | 2024-12-15 | Active |

### Step 2: Match the Requested Agent

If `$ARGUMENTS` is provided:
- Try to match by ID (exact numeric match)
- Try to match by name (case-insensitive partial match)

If no match found, show the available agents and ask user to clarify.

If `$ARGUMENTS` is empty, display the list and ask user to select.

### Step 3: Verify MCP Server Enabled

Check if the selected agent has MCP Server enabled. If not, guide the user:

1. Go to https://app.customgpt.ai/projects/{selectedId}
2. Click **Deploy** → **MCP Server (Beta)**
3. If no token exists, click **Generate MCP Token**

### Step 4: Update Environment Variable

Guide the user to update their environment:

```bash
export CUSTOMGPT_PROJECT_ID="<selected-agent-id>"
```

For a persistent change, add to `~/.bashrc` or `~/.zshrc`:
```bash
echo 'export CUSTOMGPT_PROJECT_ID="<selected-agent-id>"' >> ~/.bashrc
source ~/.bashrc
```

### Step 5: Verify Connection

Test the new agent connection:

Use the `mcp__customgpt__send_message` tool with a simple query like "What topics are covered in this knowledge base?"

### Step 6: Confirm Success

Once verified:

**Agent Switched Successfully!**

- **Active Agent**: [Agent Name] (ID: [Agent ID])
- **Status**: Connected

You can now use `/customgpt:ask <question>` to query this knowledge base.

## Notes

- Environment variable changes only persist in the current session unless added to shell profile
- Each agent is a separate knowledge base with its own indexed content
- The MCP token is shared across agents (per-user), but the project ID determines which agent responds
