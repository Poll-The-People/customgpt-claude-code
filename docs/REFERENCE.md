---
type: reference
audience: developers
goal: Complete specification of all commands, configuration, and APIs
owner: CustomGPT.ai
last_verified: 2025-12-24
---

# Reference

Complete reference for CustomGPT RAG for Claude Code. This document covers all commands, configuration options, environment variables, and API details.

---

## Table of Contents

1. [Commands](#commands)
2. [Environment Variables](#environment-variables)
3. [MCP Configuration](#mcp-configuration)
4. [Plugin Manifest](#plugin-manifest)
5. [REST API Endpoints](#rest-api-endpoints)
6. [Error Codes](#error-codes)
7. [Skill Configuration](#skill-configuration)

---

## Commands

### /customgpt:setup

**Description:** Interactive setup wizard for configuring the CustomGPT RAG integration.

**Syntax:**
```
/customgpt:setup
```

**Arguments:** None

**Behavior:**
1. Checks for existing API key configuration
2. Lists available agents if API key is set
3. Guides through agent creation if none exist
4. Helps generate MCP token
5. Verifies connection

**Example:**
```
/customgpt:setup

> Do you have a CustomGPT.ai account? If not, sign up at https://app.customgpt.ai
> [User confirms]
> Checking for existing agents...
> Found 2 agents:
>   - Engineering Docs (ID: 12345)
>   - API Reference (ID: 12346)
> Which agent would you like to use?
```

---

### /customgpt:ask

**Description:** Query the knowledge base directly with a question.

**Syntax:**
```
/customgpt:ask <question>
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| question | Yes | The question to ask the knowledge base |

**Returns:**
- Answer generated from indexed content
- Source citations with document names and references
- Excerpt from relevant source material

**Example:**
```
/customgpt:ask "What is our rate limiting policy for the API?"

> According to your API documentation:
>
> Rate limits are configured as follows:
> - Standard tier: 100 requests per minute
> - Enterprise tier: 1,000 requests per minute
> - Exceeding limits returns HTTP 429 with a Retry-After header
>
> Source: api-guidelines.md (Section 4.2 - Rate Limiting)
```

**Error Handling:**
| Error | Meaning | Resolution |
|-------|---------|------------|
| "Token invalid" | MCP token expired or incorrect | Regenerate at Deploy -> MCP Server |
| "No results found" | Knowledge base lacks relevant content | Verify indexing complete; try broader query |
| "Connection timeout" | MCP server unreachable | Check MCP Server enabled; retry |

---

### /customgpt:list-agents

**Description:** Display all CustomGPT agents available in your account.

**Syntax:**
```
/customgpt:list-agents
```

**Arguments:** None

**Returns:**
- Table of agents with ID, name, page count, and status
- Indicator for currently active agent

**Example:**
```
/customgpt:list-agents

> | ID    | Name              | Type    | Pages | Status   |
> |-------|-------------------|---------|-------|----------|
> | 12345 | Engineering Docs  | SITEMAP | 150   | Active   | <-- current
> | 12346 | API Reference     | URL     | 45    | Active   |
> | 12347 | Security Policies | FILE    | 20    | Indexing |
>
> Total Agents: 3
> Active Agent: Engineering Docs (ID: 12345)
```

**Agent Types:**
| Type | Description |
|------|-------------|
| SITEMAP | Indexed from a sitemap URL |
| URL | Indexed from a single page |
| FILE | Indexed from uploaded files |

**Agent Statuses:**
| Status | Description |
|--------|-------------|
| Active | Ready to query |
| Indexing | Still processing content |
| Error | Indexing failed |

---

### /customgpt:use-agent

**Description:** Switch to a different CustomGPT agent (knowledge base).

**Syntax:**
```
/customgpt:use-agent <identifier>
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| identifier | Yes | Agent ID (numeric) or name (string) |

**Behavior:**
1. Looks up agent by ID or name (case-insensitive)
2. Verifies MCP Server is enabled for the agent
3. Updates CUSTOMGPT_PROJECT_ID
4. Tests connection

**Example:**
```
/customgpt:use-agent 12346

> Agent Switched Successfully!
> Active Agent: API Reference (ID: 12346)
> Status: Connected
```

```
/customgpt:use-agent "Security Policies"

> Agent Switched Successfully!
> Active Agent: Security Policies (ID: 12347)
> Status: Connected
```

**Notes:**
- Environment variable changes persist only in current session
- Add to shell profile for persistence
- May require new MCP token if switching accounts

---

### /customgpt:troubleshoot

**Description:** Diagnose and report configuration issues.

**Syntax:**
```
/customgpt:troubleshoot
```

**Arguments:** None

**Checks Performed:**
1. CUSTOMGPT_API_KEY is set
2. CUSTOMGPT_PROJECT_ID is set
3. CUSTOMGPT_MCP_TOKEN is set
4. REST API connectivity (HTTP 200)
5. MCP connection (valid response)
6. Agent exists and is active

**Example Output:**
```
/customgpt:troubleshoot

> === CustomGPT Diagnostic Report ===
>
> | Check          | Status | Details                    |
> |----------------|--------|----------------------------|
> | API Key        | PASS   | Set (hidden)               |
> | Project ID     | PASS   | 12345                      |
> | MCP Token      | PASS   | Set (hidden)               |
> | REST API       | PASS   | HTTP 200                   |
> | MCP Connection | PASS   | Response received          |
> | Agent Status   | PASS   | Active, 150 pages indexed  |
>
> All checks passed. Configuration is valid.
```

---

## Environment Variables

### CUSTOMGPT_API_KEY

**Required:** Yes

**Description:** REST API key for managing agents and account operations.

**Source:** CustomGPT Dashboard -> Profile -> API Keys

**Format:** String, typically starts with alphanumeric characters

**Example:**
```bash
export CUSTOMGPT_API_KEY="abc123def456ghi789"
```

**Used For:**
- Listing agents (`/customgpt:list-agents`)
- Creating agents
- Checking agent status

---

### CUSTOMGPT_PROJECT_ID

**Required:** Yes

**Description:** Numeric identifier for the active CustomGPT agent.

**Source:**
- Agent URL: `app.customgpt.ai/projects/12345`
- API response when creating/listing agents

**Format:** Numeric string

**Example:**
```bash
export CUSTOMGPT_PROJECT_ID="12345"
```

**Used For:**
- MCP connection URL construction
- Agent-specific operations

---

### CUSTOMGPT_MCP_TOKEN

**Required:** Yes

**Description:** Bearer token for authenticating MCP SSE connections.

**Source:** CustomGPT Dashboard -> Agent -> Deploy -> MCP Server (Beta)

**Format:** String, opaque token

**Example:**
```bash
export CUSTOMGPT_MCP_TOKEN="mcp_xxxxxxxxxxxxxxxxxxxxxx"
```

**Used For:**
- Authenticating RAG queries
- MCP SSE connection

**Security Notes:**
- Treat as a secret
- Do not commit to version control
- Rotate periodically
- Revoke if compromised

---

## MCP Configuration

### .mcp.json

The plugin includes an MCP configuration file that defines the SSE server connection.

**Location:** `customgpt/.mcp.json`

**Contents:**
```json
{
  "customgpt": {
    "type": "sse",
    "url": "https://mcp.customgpt.ai/projects/${CUSTOMGPT_PROJECT_ID}/sse?token=${CUSTOMGPT_MCP_TOKEN}"
  }
}
```

> **Note:** Plugin `.mcp.json` files use a flat server format (no `"mcpServers"` wrapper), unlike project-level `.mcp.json` files.

**Fields:**

| Field | Description |
|-------|-------------|
| type | Connection type. Always "sse" for CustomGPT |
| url | SSE endpoint with variable substitution |

**Variable Substitution:**
- `${CUSTOMGPT_PROJECT_ID}` - Replaced with environment variable
- `${CUSTOMGPT_MCP_TOKEN}` - Replaced with environment variable

---

### MCP Tool: send_message

The MCP server exposes a single tool for RAG queries.

**Tool Name:** `mcp__customgpt__send_message`

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| prompt | string | Yes | The query to send to the RAG engine |

**Response:**
- Generated answer based on indexed content
- Citations with source references
- Confidence indicators

**Example Invocation:**
```
mcp__customgpt__send_message(prompt: "What is our deployment process?")
```

**Response Format:**
```json
{
  "answer": "According to the deployment guide...",
  "citations": [
    {
      "source": "deployment-guide.md",
      "section": "Production Deployment",
      "excerpt": "..."
    }
  ]
}
```

---

## Plugin Manifest

### plugin.json

**Location:** `customgpt/.claude-plugin/plugin.json`

**Contents:**
```json
{
  "name": "customgpt",
  "description": "Enterprise RAG for Claude Code via CustomGPT Hosted MCP Server. Query your organization's knowledge bases (runbooks, specs, policies, docs) with industry-leading accuracy and citation support.",
  "version": "1.0.0",
  "author": {
    "name": "CustomGPT.ai",
    "url": "https://customgpt.ai"
  },
  "repository": "https://github.com/Poll-The-People/customgpt-claude-code",
  "homepage": "https://customgpt.ai",
  "keywords": [
    "rag",
    "knowledge-base",
    "enterprise",
    "mcp",
    "documentation",
    "search",
    "retrieval",
    "ai"
  ],
  "license": "MIT"
}
```

**Fields:**

| Field | Description |
|-------|-------------|
| name | Plugin identifier (must match directory name) |
| description | Short description for marketplace listing |
| version | Semantic version |
| author.name | Author display name |
| author.url | Author website |
| repository | GitHub repository URL |
| homepage | Product homepage |
| keywords | Search terms for discovery |
| license | License identifier |

---

## REST API Endpoints

These endpoints are used by the plugin for management operations.

### Base URL

```
https://app.customgpt.ai/api/v1
```

### Authentication

All requests require the Authorization header:
```
Authorization: Bearer $CUSTOMGPT_API_KEY
```

---

### List Projects

**Endpoint:** `GET /projects`

**Description:** List all agents in the account.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | integer | 1 | Page number |
| per_page | integer | 10 | Results per page |

**Response:**
```json
{
  "status": "success",
  "data": {
    "data": [
      {
        "id": 12345,
        "project_name": "Engineering Docs",
        "created_at": "2024-12-01T10:00:00Z",
        "updated_at": "2024-12-15T14:30:00Z",
        "is_chat_active": true,
        "pages_found": 150
      }
    ],
    "current_page": 1,
    "last_page": 1,
    "per_page": 10,
    "total": 1
  }
}
```

**Example:**
```bash
curl -X GET "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Accept: application/json"
```

---

### Get Project

**Endpoint:** `GET /projects/{id}`

**Description:** Get details for a specific agent.

**Path Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| id | integer | Project ID |

**Response:**
```json
{
  "status": "success",
  "data": {
    "id": 12345,
    "project_name": "Engineering Docs",
    "sitemap_path": "https://docs.example.com/sitemap.xml",
    "created_at": "2024-12-01T10:00:00Z",
    "is_chat_active": true,
    "pages_found": 150,
    "pages_crawled": 150,
    "pages_indexed": 150
  }
}
```

**Example:**
```bash
curl -X GET "https://app.customgpt.ai/api/v1/projects/12345" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Accept: application/json"
```

---

### Create Project

**Endpoint:** `POST /projects`

**Description:** Create a new agent.

**Request Body (multipart/form-data):**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| project_name | string | Yes | Display name for the agent |
| sitemap_path | string | No | URL to sitemap for indexing |
| file | file | No | File to upload and index |

**Response:**
```json
{
  "status": "success",
  "data": {
    "id": 12348,
    "project_name": "New Docs",
    "created_at": "2024-12-20T09:00:00Z"
  }
}
```

**Example (sitemap):**
```bash
curl -X POST "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -F "project_name=My Documentation" \
  -F "sitemap_path=https://docs.example.com/sitemap.xml"
```

**Example (file upload):**
```bash
curl -X POST "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -F "project_name=Uploaded Docs" \
  -F "file=@/path/to/document.pdf"
```

---

## Error Codes

### HTTP Status Codes

| Code | Meaning | Resolution |
|------|---------|------------|
| 200 | Success | Request completed successfully |
| 400 | Bad Request | Check request parameters |
| 401 | Unauthorized | API key invalid or missing |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource does not exist |
| 429 | Rate Limited | Slow down; retry after delay |
| 500 | Server Error | Retry; contact support if persistent |

### MCP Errors

| Error | Meaning | Resolution |
|-------|---------|------------|
| "Authentication failed" | Invalid MCP token | Regenerate token at Deploy -> MCP Server |
| "Project not found" | Invalid project ID | Verify CUSTOMGPT_PROJECT_ID |
| "MCP not enabled" | MCP Server not activated | Enable at Deploy -> MCP Server |
| "Connection timeout" | SSE connection failed | Check network; retry |
| "No relevant content" | Query matched nothing | Broaden query; check indexing |

### REST API Errors

| Error | Meaning | Resolution |
|-------|---------|------------|
| "Unauthenticated" | Missing or invalid API key | Set CUSTOMGPT_API_KEY |
| "Project not found" | ID does not exist | Use /customgpt:list-agents to find valid IDs |
| "Rate limit exceeded" | Too many requests | Wait and retry |
| "Validation failed" | Invalid parameters | Check request format |

---

## Skill Configuration

### RAG Retrieval Skill

The plugin includes an autonomous skill that triggers RAG queries when appropriate.

**Location:** `customgpt/skills/rag-retrieval/SKILL.md`

**Trigger Conditions:**
- User asks about company policies or procedures
- User references internal documentation
- User asks about internal systems or APIs
- User mentions "our documentation" or "company docs"
- User asks about security, compliance, or regulations
- User needs technical specifications or architecture details

**Non-Trigger Conditions:**
- General programming questions
- Public framework/library documentation
- Local file operations
- Math or logic problems
- Creative writing or brainstorming

**Invocation:**
The skill is invoked automatically by Claude when appropriate context is detected. It uses the `mcp__customgpt__send_message` tool internally.

**Response Integration:**
- Answers include citations from source documents
- Gaps in knowledge base are acknowledged
- General knowledge is offered as fallback with caveats

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2025-12-24 | Initial release |

---

## Related Documentation

- [Quick Start](QUICKSTART.md) - Get started in 5 minutes
- [How-To Guides](HOWTO.md) - Task-oriented guides
- [Explanation](EXPLANATION.md) - Architecture and design decisions
- [CustomGPT API Docs](https://docs.customgpt.ai) - Full API reference
- [MCP Reference](https://docs.customgpt.ai/reference/customgptai-mcp-support) - MCP-specific documentation
