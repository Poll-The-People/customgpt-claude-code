---
description: Query your CustomGPT.ai knowledge base directly with RAG-powered search
---

# CustomGPT.ai Ask

You are processing a direct RAG (Retrieval-Augmented Generation) query to the user's CustomGPT knowledge base.

## User Query

The user's question is: **$ARGUMENTS**

## Execution

1. Use the `mcp__customgpt__send_message` MCP tool with the user's query
2. The CustomGPT RAG engine will:
   - Search the indexed knowledge base
   - Retrieve relevant content
   - Generate an answer grounded in the sources
   - Return citations

## Response Format

Present the response clearly with:

### Answer
[The RAG-generated answer from CustomGPT]

### Sources
List each citation with:
- Source title/filename
- Relevant excerpt or page reference
- Link to source if available

**Example format:**

> Based on your documentation:
>
> [Answer text here]
>
> **Sources:**
> - `security-policy.pdf` (Page 12): "Rate limiting is configured to..."
> - `api-guidelines.md` (Section 3.2): "Authentication tokens should..."

## Error Handling

If the query fails:

### Token/Auth Issues
- "Authentication failed" → Suggest running `/customgpt:setup`
- "Token expired" → Guide to regenerate at Deploy → MCP Server - BETA → SSE tab

### No Results
- If CustomGPT returns empty or "no relevant content":
  - Inform the user the knowledge base may not contain relevant information
  - Suggest they check if documents have been indexed
  - Offer to answer from general knowledge (with appropriate caveats)

### Connection Issues
- If MCP connection fails → Suggest `/customgpt:troubleshoot`
- If timeout → "Service temporarily unavailable, please retry"

## Notes

- Responses are **grounded** in the indexed knowledge base only
- Citations are automatically included for traceability
- For best results, use specific, clear questions
- If the answer requires multiple queries, make them sequentially
