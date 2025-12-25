# CustomGPT RAG for Claude Code

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

## Quick Start (2 minutes)

### 1. Install the Plugin

```bash
/plugin install customgpt@Poll-The-People-customgpt-claude-code
```

### 2. Set Your Credentials

Get these from [app.customgpt.ai](https://app.customgpt.ai):

```bash
export CUSTOMGPT_API_KEY="your-api-key"        # Profile -> API Keys
export CUSTOMGPT_PROJECT_ID="12345"            # From agent URL
export CUSTOMGPT_MCP_TOKEN="your-mcp-token"    # Deploy -> MCP Server
```

### 3. Query Your Knowledge Base

```bash
/customgpt:ask "What is our deployment process for production?"
```

**That's it.** You now have RAG-powered documentation search in Claude Code.

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
> Access requires explicit user consent and audit logging.
>
> **Source:** `data-handling-guidelines.pdf` (Page 12)

### Automatic Retrieval

Claude autonomously queries your docs when you ask about:
- Company policies and procedures
- Internal documentation or runbooks
- Security and compliance requirements
- Technical specifications and architecture

Just ask naturally:
```
"Update this code to follow our error handling guidelines"
```

Claude will fetch the relevant documentation and apply it.

### Citation Support

Every answer includes:
- Source document name
- Page or section reference
- Relevant excerpt

No more "I think I read this somewhere."

### Multi-Agent Support

Switch between knowledge bases on the fly:

```bash
/customgpt:list-agents          # See all your agents
/customgpt:use-agent 67890      # Switch to a different one
```

---

## Commands

| Command | Description |
|---------|-------------|
| `/customgpt:setup` | Interactive setup wizard |
| `/customgpt:ask <question>` | Query the knowledge base |
| `/customgpt:list-agents` | Show all available agents |
| `/customgpt:use-agent <id>` | Switch to a different agent |
| `/customgpt:troubleshoot` | Diagnose configuration issues |

---

## Use Cases

### Code Reviews

> "Does this authentication flow match our security guidelines?"

Claude fetches your security documentation and compares it against the code.

### Debugging

> "According to our runbook, what should I check if Redis connection fails?"

Get step-by-step procedures from your ops documentation.

### Onboarding

> "What's our standard project structure for Python services?"

New team members get consistent answers from your actual standards.

### Compliance

> "What are our GDPR requirements for user data deletion?"

Cite specific policies when implementing compliance features.

---

## Installation Options

### From the Marketplace (Recommended)

```bash
/plugin install customgpt@Poll-The-People-customgpt-claude-code
```

### Add Marketplace First

```bash
/plugin marketplace add Poll-The-People/customgpt-claude-code
/plugin install customgpt
```

### Local Development

```bash
git clone https://github.com/Poll-The-People/customgpt-claude-code.git
claude --plugin-dir ./customgpt-claude-code/customgpt
```

---

## Documentation

| Document | Description |
|----------|-------------|
| [Quick Start](docs/QUICKSTART.md) | Tutorial: Zero to first query in 5 minutes |
| [How-To Guides](docs/HOWTO.md) | Task-oriented guides for common scenarios |
| [Reference](docs/REFERENCE.md) | Complete command and configuration reference |
| [Explanation](docs/EXPLANATION.md) | How RAG works, architecture, and design decisions |

---

## Requirements

- **Claude Code** with plugin support
- **CustomGPT.ai account** ([free trial available](https://app.customgpt.ai))
- At least one agent with indexed content

---

## Security

- **SOC-2 Type 2 Certified** - CustomGPT.ai infrastructure
- **TLS Encryption** - All traffic encrypted in transit
- **No Data Sharing** - Your docs stay in your CustomGPT account
- **Token-Based Auth** - Revocable MCP tokens

See [Security](docs/EXPLANATION.md#security-model) for details.

---

## Support

- **Documentation:** [docs.customgpt.ai](https://docs.customgpt.ai)
- **MCP Reference:** [CustomGPT MCP Support](https://docs.customgpt.ai/reference/customgptai-mcp-support)
- **Issues:** [GitHub Issues](https://github.com/Poll-The-People/customgpt-claude-code/issues)
- **Email:** hello@customgpt.ai

---

## License

MIT License - see [LICENSE](LICENSE) for details.

---

Built by [CustomGPT.ai](https://customgpt.ai) - The #1 RAG API for Enterprise
