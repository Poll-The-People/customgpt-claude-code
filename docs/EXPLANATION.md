---
type: explanation
audience: developers, architects, engineering-leads
goal: Understand how RAG works, why design decisions were made, and how the system fits together
owner: CustomGPT.ai
last_verified: 2025-12-24
---

# Explanation

This document explains the concepts, architecture, and design decisions behind CustomGPT RAG for Claude Code. It answers "why" questions and provides mental models for understanding the system.

---

## Table of Contents

1. [What is RAG?](#what-is-rag)
2. [Why RAG for Code Development?](#why-rag-for-code-development)
3. [Why MCP Over REST?](#why-mcp-over-rest)
4. [Architecture Overview](#architecture-overview)
5. [Data Flow](#data-flow)
6. [Security Model](#security-model)
7. [Trade-offs and Alternatives](#trade-offs-and-alternatives)
8. [When RAG Helps (and When It Does Not)](#when-rag-helps-and-when-it-does-not)

---

## What is RAG?

**RAG** stands for **Retrieval-Augmented Generation**. It is a technique that combines information retrieval with language model generation.

### The Problem with Pure LLMs

Large language models (LLMs) have two limitations for organizational documentation:

1. **Knowledge cutoff:** LLMs only know information from their training data. Your internal runbook from last month is not in there.

2. **Hallucination:** When asked about something they do not know, LLMs may generate plausible-sounding but incorrect answers.

### How RAG Solves This

RAG addresses both problems by adding a retrieval step:

```
User Query
    |
    v
+-------------------+
| 1. RETRIEVE       |  Search your indexed documents
|    Find relevant  |  for content matching the query
|    passages       |
+-------------------+
    |
    v
+-------------------+
| 2. AUGMENT        |  Add retrieved passages to
|    Context +      |  the LLM's context window
|    retrieved docs |
+-------------------+
    |
    v
+-------------------+
| 3. GENERATE       |  LLM generates answer
|    Grounded       |  grounded in actual
|    answer         |  documentation
+-------------------+
    |
    v
Answer with Citations
```

### Why This Matters

- **Accuracy:** Answers come from your actual documentation, not model imagination.
- **Traceability:** Every answer includes citations so you can verify.
- **Freshness:** Re-index your docs and answers update immediately.
- **Security:** Your content stays in your CustomGPT account.

---

## Why RAG for Code Development?

Developers spend significant time context-switching between code and documentation.

### The Context-Switch Problem

Typical workflow without RAG:

1. You are debugging an authentication issue
2. You need to check the token refresh policy
3. Alt-Tab to browser
4. Search Confluence/Notion/GitHub
5. Scroll through search results
6. Find the document (maybe the wrong version)
7. Read it, extract what you need
8. Switch back to code
9. Try to remember what you just read

**Result:** Broken flow, lost context, wasted time.

### The RAG Solution

Workflow with RAG:

1. You are debugging an authentication issue
2. Ask: "What is our token refresh policy?"
3. Get answer with citation in your terminal
4. Continue coding

**Result:** Maintained flow, immediate answer, traceable source.

### Use Cases That Benefit Most

| Use Case | Why RAG Helps |
|----------|---------------|
| **Policy compliance** | Get exact policy text with citation for audits |
| **Incident response** | Immediate access to runbook procedures |
| **Code review** | Check code against documented standards |
| **Onboarding** | New devs get consistent, authoritative answers |
| **Architecture decisions** | Reference documented patterns and rationale |

---

## Why MCP Over REST?

This plugin uses MCP (Model Context Protocol) instead of a traditional REST API. Here is why.

### What is MCP?

MCP is a protocol for connecting AI models to external tools and data sources. It provides:

- Standardized tool definitions
- Streaming connections via SSE
- Native integration with Claude

### Comparison

| Aspect | REST API | MCP |
|--------|----------|-----|
| Integration | Custom code needed | Native to Claude |
| Connection | Request/response | Persistent SSE stream |
| Tool discovery | Manual | Automatic |
| Context management | Manual | Built-in |
| Response streaming | Polling required | Real-time |

### Why MCP is Better for RAG

1. **Native integration:** Claude understands MCP tools natively. No wrapper code needed.

2. **Streaming:** SSE allows real-time response streaming for long answers.

3. **Persistent connection:** Reduces latency for repeated queries.

4. **Automatic invocation:** Claude can decide when to invoke RAG based on the skill definition, without explicit user commands.

5. **Standard protocol:** As MCP adoption grows, the plugin works without modification.

### Trade-off

The REST API is still available and used for management operations (listing agents, creating projects). MCP is specifically for the RAG query path where latency and integration matter most.

---

## Architecture Overview

### Components

```
+------------------+     +----------------------+     +-------------------+
|   Claude Code    |     |   CustomGPT MCP      |     |   CustomGPT       |
|   (Terminal)     |<--->|   Server             |<--->|   RAG Engine      |
|                  |     |   (SSE)              |     |                   |
+------------------+     +----------------------+     +-------------------+
        |                                                      |
        |                                                      v
        v                                             +-------------------+
+------------------+                                  |   Your Indexed    |
|   Environment    |                                  |   Knowledge Base  |
|   Variables      |                                  |   (Documents)     |
+------------------+                                  +-------------------+
```

### Plugin Structure

```
customgpt/
├── .claude-plugin/
│   └── plugin.json         # Plugin manifest for marketplace
├── .mcp.json               # MCP server configuration
├── commands/
│   ├── setup.md            # /customgpt:setup logic
│   ├── ask.md              # /customgpt:ask logic
│   ├── list-agents.md      # /customgpt:list-agents logic
│   ├── use-agent.md        # /customgpt:use-agent logic
│   └── troubleshoot.md     # /customgpt:troubleshoot logic
├── skills/
│   └── rag-retrieval/
│       └── SKILL.md        # Autonomous RAG skill definition
└── README.md               # Plugin documentation
```

### Responsibility Separation

| Component | Responsibility |
|-----------|----------------|
| **Claude Code** | User interface, plugin loading, command dispatch |
| **Plugin commands** | Guide Claude's behavior for explicit commands |
| **RAG skill** | Define when Claude should autonomously use RAG |
| **MCP configuration** | Define SSE connection to CustomGPT |
| **CustomGPT MCP Server** | Handle SSE, authenticate, route to RAG engine |
| **CustomGPT RAG Engine** | Retrieve, rank, generate, cite |
| **Your Knowledge Base** | Store indexed documentation |

---

## Data Flow

### Query Flow

When you run `/customgpt:ask "What is our rate limiting policy?"`:

```
1. User types command in Claude Code
   |
2. Claude Code loads ask.md command definition
   |
3. Claude invokes mcp__customgpt__send_message tool
   |
4. MCP client connects to SSE endpoint:
   https://mcp.customgpt.ai/projects/{PROJECT_ID}/sse?token={MCP_TOKEN}
   |
5. Query is sent to CustomGPT RAG engine
   |
6. RAG engine:
   a. Searches vector index for relevant chunks
   b. Ranks results by relevance
   c. Passes top chunks to LLM
   d. Generates answer grounded in chunks
   e. Attaches citations
   |
7. Response streams back via SSE
   |
8. Claude formats response with citations
   |
9. User sees answer in terminal
```

### Indexing Flow (Background)

When you add content to a CustomGPT agent:

```
1. Provide sitemap URL or upload files
   |
2. Crawler fetches content (for sitemaps)
   |
3. Content is chunked into passages
   |
4. Chunks are embedded (converted to vectors)
   |
5. Vectors are stored in search index
   |
6. Metadata (source, page, section) is preserved
   |
7. Agent status becomes "Active"
```

---

## Security Model

### Authentication Layers

1. **API Key (REST):** Used for management operations (list/create agents).
   - Scoped to your account
   - Revocable
   - Required for all REST API calls

2. **MCP Token (SSE):** Used for RAG queries.
   - Scoped to specific agent
   - Revocable
   - Required for MCP connections

### Data Protection

| Aspect | Protection |
|--------|------------|
| **In Transit** | TLS 1.3 encryption for all connections |
| **At Rest** | AES-256 encryption in CustomGPT infrastructure |
| **Access Control** | Token-based; only you can query your agents |
| **Audit** | All queries logged in CustomGPT dashboard |
| **Compliance** | SOC-2 Type 2 certified infrastructure |

### What CustomGPT Can See

CustomGPT processes:
- Your indexed documents (for RAG)
- Your queries (to generate answers)
- Response analytics (for quality improvement)

CustomGPT does NOT:
- Share your data with other customers
- Train models on your private data
- Store queries beyond operational needs

### Token Security Best Practices

```bash
# DO: Store in environment or secrets manager
export CUSTOMGPT_MCP_TOKEN=$(op read "op://Private/CustomGPT/mcp-token")

# DO: Add .env to .gitignore
echo ".env" >> .gitignore

# DO NOT: Commit tokens
# DO NOT: Share tokens in screenshots or logs
# DO NOT: Use same token across environments
```

### Token Rotation

Rotate tokens periodically:

1. Go to agent -> Deploy -> MCP Server
2. Click **Regenerate Token**
3. Update environment variables
4. Old token is immediately invalidated

---

## Trade-offs and Alternatives

### Trade-off: MCP vs. Direct LLM

**This plugin approach:**
```
User -> Claude Code -> MCP -> CustomGPT RAG -> Response
```

**Alternative: Direct LLM with documents in context:**
```
User -> Claude Code -> (paste docs) -> Response
```

| Factor | Plugin (MCP) | Direct LLM |
|--------|--------------|------------|
| Context window | Unlimited (indexed) | Limited (100k tokens) |
| Freshness | Always current | Manual refresh |
| Citation quality | Automatic, precise | Varies |
| Setup effort | Higher | Lower |
| Ongoing effort | None | Manual paste |
| Accuracy for large docs | Higher | Degrades with size |

**When to use direct LLM:** Small, stable documents that fit in context.

**When to use this plugin:** Large, changing documentation sets.

### Trade-off: Hosted vs. Self-Hosted RAG

**This plugin (hosted CustomGPT):**
- Zero infrastructure to manage
- SOC-2 compliance included
- Subscription cost

**Self-hosted RAG (e.g., LangChain + Pinecone):**
- Full control
- Complex setup and maintenance
- Variable cost (compute + storage)

**Recommendation:** Start with hosted. Move to self-hosted only if you have specific compliance or customization requirements.

### Trade-off: Single Agent vs. Multiple Agents

**Single unified agent:**
- Simpler configuration
- One knowledge base to query
- May return less precise results for diverse content

**Multiple specialized agents:**
- More precise results per domain
- Requires agent switching
- More MCP tokens to manage

**Recommendation:** Start with one agent containing your most-used docs. Split when query precision degrades.

---

## When RAG Helps (and When It Does Not)

### RAG is Most Helpful For

| Scenario | Why |
|----------|-----|
| **Policies and procedures** | Exact text matters; citations needed |
| **Runbooks and playbooks** | Step-by-step procedures with nuance |
| **API documentation** | Specific endpoints, parameters, examples |
| **Architecture decisions** | Rationale and context from ADRs |
| **Compliance requirements** | Auditable sources required |
| **Onboarding materials** | Consistent answers for new team members |

### RAG is Less Helpful For

| Scenario | Why |
|----------|-----|
| **General programming questions** | Public knowledge; Claude already knows |
| **Real-time data** | RAG indexes are point-in-time snapshots |
| **Highly dynamic content** | Re-indexing delay |
| **Content not in your docs** | Cannot retrieve what is not indexed |
| **Creative tasks** | RAG grounds answers; limits creativity |

### Mental Model: When to Invoke RAG

Ask yourself: "Is the answer in our internal documentation?"

- **Yes** -> Use RAG
- **No, it is public knowledge** -> Let Claude answer directly
- **No, it does not exist** -> Write the docs, then index them

---

## Summary

CustomGPT RAG for Claude Code brings enterprise documentation into your terminal by:

1. **Indexing** your documentation into a searchable vector store
2. **Retrieving** relevant passages for each query
3. **Generating** answers grounded in your actual content
4. **Citing** sources for traceability

The MCP integration provides native Claude support, streaming responses, and autonomous invocation when appropriate.

Security is handled through token-based authentication, TLS encryption, and SOC-2 certified infrastructure.

---

## Related Documentation

- [Quick Start](QUICKSTART.md) - Get started in 5 minutes
- [How-To Guides](HOWTO.md) - Task-oriented guides
- [Reference](REFERENCE.md) - Complete command and configuration reference
- [CustomGPT Docs](https://docs.customgpt.ai) - Full platform documentation
