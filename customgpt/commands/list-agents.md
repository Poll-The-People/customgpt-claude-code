---
description: List all available CustomGPT agents in your account
---

# List CustomGPT Agents

You are helping the user see all their available CustomGPT agents (knowledge bases).

## Execution

### Step 1: Fetch Agents from API

Use the Bash tool to call the CustomGPT REST API:

```bash
curl -s -X GET "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Accept: application/json"
```

### Step 2: Parse and Display Results

Extract and display the agents in a clear table format:

| ID | Name | Type | Pages | Status | Created |
|----|------|------|-------|--------|---------|
| 12345 | Engineering Docs | SITEMAP | 150 | Active | 2024-12-01 |
| 12346 | API Reference | URL | 45 | Active | 2024-12-15 |
| 12347 | Security Policies | FILE | 20 | Indexing | 2024-12-20 |

**Legend:**
- **Type**: SITEMAP (indexed from sitemap), URL (single page), FILE (uploaded document)
- **Pages**: Number of indexed content pages
- **Status**: Active (ready to query), Indexing (processing), Error

### Step 3: Indicate Active Agent

Check the current `CUSTOMGPT_PROJECT_ID` environment variable and mark the active agent:

```bash
echo "Current CUSTOMGPT_PROJECT_ID: $CUSTOMGPT_PROJECT_ID"
```

Add a marker (→) next to the currently active agent in the table.

### Step 4: Show Summary

**Total Agents**: [count]
**Active Agent**: [name] (ID: [id])

## Switching Agents

To switch to a different agent, use:
```
/customgpt:use-agent <agent-id-or-name>
```

## Creating New Agents

To create a new agent:
1. Visit https://app.customgpt.ai/projects
2. Click "Create Agent"
3. Add a sitemap URL or upload documents
4. Run `/customgpt:setup` to connect it

## Error Handling

### API Key Not Set
If `CUSTOMGPT_API_KEY` is not set:
"API key not configured. Run `/customgpt:setup` first, or set CUSTOMGPT_API_KEY environment variable."

### API Key Invalid
If the API returns 401:
"API key is invalid or expired. Get a new key at https://app.customgpt.ai → Profile → API Keys"

### No Agents Found
If the list is empty:
"No agents found in your account. Create one at https://app.customgpt.ai or use `/customgpt:setup` to create your first agent."

### Pagination
If there are more than 10 agents, the API returns paginated results. Fetch additional pages if needed:

```bash
curl -s -X GET "https://app.customgpt.ai/api/v1/projects?page=2" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Accept: application/json"
```
