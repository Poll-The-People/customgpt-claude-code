---
type: tutorial
audience: developers
goal: Execute your first RAG query from Claude Code
prereqs:
  - Claude Code installed
  - CustomGPT.ai account (free trial available)
timebox: 5
owner: CustomGPT.ai
last_verified: 2025-12-24
---

# Quick Start: Your First RAG Query

**Goal:** Go from zero to querying your organization's documentation in 5 minutes.

**You will:**
1. Install the CustomGPT plugin
2. Connect to your CustomGPT agent
3. Run your first RAG query

---

## Prerequisites

Before you begin:

- [ ] **Claude Code** installed and working
- [ ] **CustomGPT.ai account** - [Sign up for free trial](https://app.customgpt.ai) if you do not have one
- [ ] **At least one agent** with indexed content (we will create one if needed)

---

## Step 1: Install the Plugin

In Claude Code, run:

```bash
/plugin install customgpt@Poll-The-People-customgpt-claude-code
```

**Expected output:**
```
Plugin 'customgpt' installed successfully.
```

### Alternative: Local Installation

If you prefer to install from source:

```bash
git clone https://github.com/Poll-The-People/customgpt-claude-code.git
cd customgpt-claude-code
claude --plugin-dir ./customgpt
```

---

## Step 2: Get Your API Key

You need an API key to list and manage your CustomGPT agents.

1. Go to [app.customgpt.ai](https://app.customgpt.ai)
2. Log in to your account
3. Click your **profile icon** (top right)
4. Select **API Keys**
5. Click **Generate New Key** or copy an existing key

Set the environment variable:

```bash
export CUSTOMGPT_API_KEY="your-api-key-here"
```

**Tip:** Add this to your `~/.bashrc` or `~/.zshrc` for persistence.

---

## Step 3: Select or Create an Agent

An "agent" is a CustomGPT knowledge base containing your indexed documents.

### Option A: Use an Existing Agent

If you already have agents, list them:

```bash
/customgpt:list-agents
```

**Expected output:**
```
| ID    | Name              | Pages | Status |
|-------|-------------------|-------|--------|
| 12345 | Engineering Docs  | 150   | Active |
| 12346 | API Reference     | 45    | Active |

Current agent: Engineering Docs (ID: 12345)
```

Skip to Step 4 if your agent is listed.

### Option B: Create a New Agent

If you have no agents, create one from a sitemap or URL:

**From a sitemap:**
```bash
curl -X POST "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Content-Type: multipart/form-data" \
  -F "project_name=My Documentation" \
  -F "sitemap_path=https://docs.example.com/sitemap.xml"
```

**Or use the dashboard:**
1. Go to [app.customgpt.ai/projects](https://app.customgpt.ai/projects)
2. Click **Create Agent**
3. Enter a name and add your sitemap URL or upload files
4. Wait for indexing to complete (1-10 minutes depending on size)

Note the **Project ID** from the response or URL (e.g., `12345`).

---

## Step 4: Get Your MCP Token

The MCP token authenticates RAG queries.

1. Go to [app.customgpt.ai/projects](https://app.customgpt.ai/projects)
2. Click on your agent
3. Click **Deploy** in the left sidebar
4. Click **MCP Server (Beta)**
5. Click **Generate MCP Token**
6. Copy the token

Set the environment variables:

```bash
export CUSTOMGPT_PROJECT_ID="12345"           # Your agent ID
export CUSTOMGPT_MCP_TOKEN="your-mcp-token"   # The token you just copied
```

---

## Step 5: Run Your First Query

Now test the connection:

```bash
/customgpt:ask "What topics are covered in this knowledge base?"
```

**Expected output:**
```
Based on your documentation:

This knowledge base covers:
- Deployment procedures and runbooks
- API authentication and authorization
- Database schema and migrations
- Error handling guidelines

Sources:
- `index.md` (Introduction)
- `deployment/overview.md` (Deployment Guide)
```

---

## You Built It

You now have RAG-powered documentation search in Claude Code.

**Try these queries:**

```bash
/customgpt:ask "How do I deploy to production?"
/customgpt:ask "What is our error handling policy?"
/customgpt:ask "Where is the database schema documented?"
```

---

## Next Steps

| What | Link |
|------|------|
| Learn common tasks | [How-To Guides](HOWTO.md) |
| Full command reference | [Reference](REFERENCE.md) |
| Understand the architecture | [Explanation](EXPLANATION.md) |
| Switch between agents | `/customgpt:use-agent <id>` |
| Troubleshoot issues | `/customgpt:troubleshoot` |

---

## Troubleshooting

### "API key not configured"

```bash
echo $CUSTOMGPT_API_KEY
```

If empty, set it:
```bash
export CUSTOMGPT_API_KEY="your-api-key"
```

### "Project not found"

Verify your project ID:
```bash
echo $CUSTOMGPT_PROJECT_ID
```

Check it matches an agent in your account:
```bash
/customgpt:list-agents
```

### "Token invalid"

Regenerate your MCP token:
1. Go to your agent in CustomGPT
2. Deploy -> MCP Server (Beta)
3. Click **Regenerate Token**
4. Update your environment variable

### "No results found"

Your knowledge base may not have relevant content indexed. Check:
1. Agent has finished indexing (not "Indexing" status)
2. Your query relates to content in your docs
3. Try a broader query

For detailed diagnostics:
```bash
/customgpt:troubleshoot
```

---

## Summary

| Step | Action | Result |
|------|--------|--------|
| 1 | Install plugin | Plugin available in Claude Code |
| 2 | Set API key | Can list/manage agents |
| 3 | Select agent | Know which knowledge base to query |
| 4 | Set MCP token | Authenticated for RAG queries |
| 5 | Run query | Documentation answers in terminal |

Total time: ~5 minutes

---

Need help? Contact [hello@customgpt.ai](mailto:hello@customgpt.ai) or [open an issue](https://github.com/Poll-The-People/customgpt-claude-code/issues).
