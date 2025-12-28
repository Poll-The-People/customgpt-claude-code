# CustomGPT.ai RAG for Claude Code

**Enterprise-grade RAG directly in your terminal.** Query your organization's knowledge bases, runbooks, specs, and documentation without leaving Claude Code.

[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-blue)](https://github.com/Poll-The-People/customgpt-claude-code)
[![SOC-2 Type 2](https://img.shields.io/badge/SOC--2-Type%202%20Certified-green)](https://customgpt.ai/security)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## The Problem

You are debugging a production issue. You need to know:
- What does the runbook say about database failover?
- What is our rate limiting policy?
- How is the auth service supposed to handle token refresh?

**Without this plugin:** Alt-Tab to browser. Search Confluence. Scroll through Notion. Find the wrong version. Lose context.

**With this plugin:** Ask directly in your terminal. Get the answer with citations. Keep your flow.

---

## Installation

### From GitHub (Recommended)

```
/plugin marketplace add Poll-The-People/customgpt-claude-code
/plugin install customgpt
```

### Local Development

```bash
claude --plugin-dir ./customgpt
```

---

## Quick Start (30 seconds)

### 1. Get Your MCP URL

From [app.customgpt.ai](https://app.customgpt.ai):
- Open your agent → **Deploy** → **MCP Server - BETA** → **SSE** tab → Copy the URL

### 2. Run Setup

```
/customgpt:setup
```

Paste your URL when prompted.

### 3. Restart Claude Code

The MCP server connects on startup.

### 4. Query Your Docs

```
/customgpt:ask "What is our deployment process?"
```

**That's it.** RAG-powered documentation search in Claude Code.

---

## Features

### Instant RAG Queries

Query your documentation directly:

```
/customgpt:ask "How do we handle PII data?"
```

Response:
> According to the Data Handling Guidelines (Section 4.2):
>
> PII must be encrypted at rest using AES-256 and in transit using TLS 1.3.
>
> **Source:** `data-handling-guidelines.pdf` (Page 12)

### Automatic Retrieval

Claude autonomously queries your docs when you ask about:
- Company policies and procedures
- Internal documentation or runbooks
- Technical specifications

Just ask naturally:
```
"Update this code to follow our error handling guidelines"
```

Claude will fetch the relevant documentation and apply it.

### Citation Support

Every answer includes:
- Source document name
- Section reference
- Relevant excerpt

No more "I think I read this somewhere."

---

## Commands

| Command | Description |
|---------|-------------|
| `/customgpt:setup` | Connect your knowledge base |
| `/customgpt:ask <question>` | Query your docs |
| `/customgpt:troubleshoot` | Check connection status |

---

## Use Cases

### Code Reviews

> "Does this authentication flow match our security guidelines?"

Claude fetches your security documentation and compares it against the code.

### Debugging

> "According to our runbook, what should I check if Redis connection fails?"

Get step-by-step procedures from your ops documentation.

### Compliance

> "What are our GDPR requirements for user data deletion?"

Cite specific policies when implementing compliance features.

---

## Documentation

| Document | Description |
|----------|-------------|
| [Quick Start](docs/QUICKSTART.md) | 2-minute setup guide |
| [How-To Guides](docs/HOWTO.md) | Task-oriented guides |
| [Reference](docs/REFERENCE.md) | Command and configuration reference |
| [Explanation](docs/EXPLANATION.md) | How RAG works, architecture |

---

## Requirements

- **Claude Code** with plugin support
- **CustomGPT.ai account** ([free trial available](https://app.customgpt.ai))
- At least one agent with indexed content

---

## Security

- **SOC-2 Type 2 Certified** - CustomGPT.ai infrastructure
- **TLS Encryption** - All traffic encrypted in transit
- **No Data Sharing** - Your docs stay in your CustomGPT.ai account
- **Token-Based Auth** - Revocable MCP tokens

---

## Support

- **Documentation:** [docs.customgpt.ai](https://docs.customgpt.ai)
- **Issues:** [GitHub Issues](https://github.com/Poll-The-People/customgpt-claude-code/issues)
- **Email:** hello@customgpt.ai

---

## License

MIT License - see [LICENSE](LICENSE) for details.

---

Built by [CustomGPT.ai](https://customgpt.ai) - The #1 RAG API for Enterprise
