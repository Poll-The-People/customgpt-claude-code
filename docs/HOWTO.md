---
type: how-to
audience: developers
goal: Complete specific tasks with CustomGPT.ai RAG
prereqs:
  - Claude Code installed
  - CustomGPT.ai account
owner: CustomGPT.ai
last_verified: 2025-12-25
---

# How-To Guides

Task-oriented guides for common scenarios. Each guide assumes you have completed the [Quick Start](QUICKSTART.md).

---

## Table of Contents

1. [Query Documentation Effectively](#query-documentation-effectively)
2. [Use RAG with Code Reviews](#use-rag-with-code-reviews)
3. [Troubleshoot Connection Issues](#troubleshoot-connection-issues)
4. [Update Your MCP Token](#update-your-mcp-token)
5. [Create an Agent from a Sitemap](#create-an-agent-from-a-sitemap)

---

## Query Documentation Effectively

**Goal:** Get the best answers from your knowledge base.

### Tips for Better Queries

1. **Be specific:**
   ```
   /customgpt:ask "What is the authentication flow for the REST API?"
   ```
   Not: "How does auth work?"

2. **Reference document types:**
   ```
   /customgpt:ask "According to the deployment runbook, what are the rollback steps?"
   ```

3. **Ask follow-up questions:**
   After getting an answer, ask for more detail on specific points.

### Natural Language Queries

You don't need to use `/customgpt:ask` for every query. Just ask Claude naturally:

```
What does our documentation say about rate limiting?
```

The RAG skill will automatically query your knowledge base when relevant.

---

## Use RAG with Code Reviews

**Goal:** Use your documentation during code reviews.

### Steps

1. **Open the PR or diff**

2. **Ask about relevant policies:**
   ```
   Does this code follow our error handling guidelines?
   ```

3. **Check for compliance:**
   ```
   According to our security docs, is this authentication implementation correct?
   ```

4. **Reference specific sections:**
   ```
   What does section 3.2 of our API design guide say about pagination?
   ```

---

## Troubleshoot Connection Issues

**Goal:** Fix common connection problems.

### Quick Diagnosis

Run:
```
/customgpt:troubleshoot
```

### Common Issues

| Symptom | Solution |
|---------|----------|
| "MCP server not configured" | Run `/customgpt:setup` |
| "Connection timeout" | Restart Claude Code |
| "401 Unauthorized" | Get new MCP URL from app.customgpt.ai |
| No results returned | Verify agent has indexed content |

### Manual Check

1. Run `/mcp` to see connected servers
2. Look for `customgpt` in the list
3. Check the status shows "connected"

---

## Update Your MCP Token

**Goal:** Regenerate your MCP token if it expires.

### Steps

1. Go to [app.customgpt.ai](https://app.customgpt.ai)
2. Open your agent
3. Click **Deploy** → **MCP Server - BETA**
4. Click the **SSE** tab
5. Click **Generate New Token**
6. Copy the full URL

7. Remove the old configuration:
   ```bash
   claude mcp remove customgpt
   ```

8. Run setup again:
   ```
   /customgpt:setup
   ```
   Paste the new URL.

9. Restart Claude Code

---

## Create an Agent from a Sitemap

**Goal:** Index your documentation from a sitemap.

### Steps

1. Go to [app.customgpt.ai](https://app.customgpt.ai)

2. Click **Create Agent**

3. Enter a name (e.g., "Engineering Docs")

4. Select **Sitemap** as the source type

5. Enter your sitemap URL:
   ```
   https://docs.example.com/sitemap.xml
   ```

6. Click **Create**

7. Wait for indexing (1-10 minutes depending on size)

8. Once complete, go to **Deploy** → **MCP Server - BETA** → **SSE** tab

9. Copy the URL and run `/customgpt:setup` in Claude Code

---

## Related Documentation

- [Quick Start](QUICKSTART.md) - Initial setup
- [Reference](REFERENCE.md) - Command details
- [CustomGPT.ai Docs](https://docs.customgpt.ai) - Full documentation
