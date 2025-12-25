---
description: Diagnose and fix CustomGPT RAG configuration issues
---

# CustomGPT Troubleshoot

You are diagnosing the CustomGPT RAG plugin configuration to help the user fix any issues.

## Diagnostic Checklist

Run through each check and report findings:

### 1. Environment Variables Check

Use Bash to check if required environment variables are set:

```bash
echo "=== CustomGPT Configuration Check ==="
echo ""
echo "CUSTOMGPT_API_KEY: ${CUSTOMGPT_API_KEY:+SET (hidden for security)}"
echo "CUSTOMGPT_API_KEY: ${CUSTOMGPT_API_KEY:-NOT SET}"
echo ""
echo "CUSTOMGPT_PROJECT_ID: ${CUSTOMGPT_PROJECT_ID:-NOT SET}"
echo ""
echo "CUSTOMGPT_MCP_TOKEN: ${CUSTOMGPT_MCP_TOKEN:+SET (hidden for security)}"
echo "CUSTOMGPT_MCP_TOKEN: ${CUSTOMGPT_MCP_TOKEN:-NOT SET}"
```

**Expected**: All three variables should be SET.

### 2. REST API Connectivity Test

Test the REST API connection:

```bash
curl -s -w "\nHTTP Status: %{http_code}\n" -X GET "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Accept: application/json" | tail -1
```

**Expected**: HTTP Status: 200

### 3. MCP Server Connectivity Test

Test the MCP connection by using the `mcp__customgpt__send_message` tool with message: "ping"

**Expected**: A valid response (even if it says "no relevant content found")

### 4. Agent Status Check

Verify the configured agent exists and is active:

```bash
curl -s -X GET "https://app.customgpt.ai/api/v1/projects/$CUSTOMGPT_PROJECT_ID" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Accept: application/json" | jq '{id: .data.id, name: .data.project_name, status: .data.is_shared}'
```

**Expected**: Agent details returned with valid ID

## Common Issues and Solutions

Present findings as a diagnostic report:

| Check | Status | Issue | Solution |
|-------|--------|-------|----------|
| API Key | ✓/✗ | [issue if any] | [solution] |
| Project ID | ✓/✗ | [issue if any] | [solution] |
| MCP Token | ✓/✗ | [issue if any] | [solution] |
| REST API | ✓/✗ | [issue if any] | [solution] |
| MCP Connection | ✓/✗ | [issue if any] | [solution] |
| Agent Status | ✓/✗ | [issue if any] | [solution] |

## Solutions Reference

### CUSTOMGPT_API_KEY Not Set
```bash
# Get your API key from https://app.customgpt.ai → Profile → API Keys
export CUSTOMGPT_API_KEY="your-api-key-here"
```

### CUSTOMGPT_PROJECT_ID Not Set
```bash
# Find your project ID:
# 1. Go to https://app.customgpt.ai/projects
# 2. Click on your agent
# 3. The ID is in the URL: /projects/12345
export CUSTOMGPT_PROJECT_ID="12345"
```

### CUSTOMGPT_MCP_TOKEN Not Set
```bash
# Get your MCP token:
# 1. Go to https://app.customgpt.ai/projects/<your-project-id>
# 2. Click Deploy → MCP Server (Beta)
# 3. Click "Generate MCP Token"
export CUSTOMGPT_MCP_TOKEN="your-mcp-token-here"
```

### API Returns 401 (Unauthorized)
- API key is invalid or expired
- Solution: Generate a new API key at Profile → API Keys

### API Returns 404 (Not Found)
- Project ID doesn't exist or you don't have access
- Solution: Verify the project ID at https://app.customgpt.ai/projects

### MCP Connection Timeout
- MCP Server may not be enabled for this agent
- Solution: Enable at Deploy → MCP Server (Beta)

### Empty Responses
- Knowledge base may not have indexed content
- Solution: Check the agent's Sources tab for indexed pages

## Quick Fix Command

If all else fails, run the setup wizard:
```
/customgpt:setup
```

This will guide you through a complete reconfiguration.

## Still Having Issues?

Contact CustomGPT support:
- Documentation: https://docs.customgpt.ai
- Email: hello@customgpt.ai
- MCP Reference: https://docs.customgpt.ai/reference/customgptai-mcp-support
