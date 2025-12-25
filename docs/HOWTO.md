---
type: how-to
audience: developers, engineering-leads
goal: Complete specific tasks with CustomGPT RAG
prereqs:
  - Plugin installed
  - Environment variables configured
owner: CustomGPT.ai
last_verified: 2025-12-24
---

# How-To Guides

Task-oriented guides for common scenarios. Each guide assumes you have completed the [Quick Start](QUICKSTART.md).

---

## Table of Contents

1. [Set Up Your First Agent](#set-up-your-first-agent)
2. [Switch Between Agents](#switch-between-agents)
3. [Query Documentation Effectively](#query-documentation-effectively)
4. [Use RAG with Code Reviews](#use-rag-with-code-reviews)
5. [Troubleshoot Connection Issues](#troubleshoot-connection-issues)
6. [Store Credentials Securely](#store-credentials-securely)
7. [Create an Agent from a Sitemap](#create-an-agent-from-a-sitemap)
8. [Create an Agent from Uploaded Files](#create-an-agent-from-uploaded-files)
9. [Integrate with CI/CD](#integrate-with-cicd)
10. [Use Multiple Knowledge Bases per Project](#use-multiple-knowledge-bases-per-project)

---

## Set Up Your First Agent

**Goal:** Create a CustomGPT agent and connect it to Claude Code.

**Prerequisites:**
- CustomGPT.ai account
- Documentation to index (sitemap URL or files)

### Steps

1. **Log in to CustomGPT:**

   Go to [app.customgpt.ai](https://app.customgpt.ai)

2. **Create a new agent:**

   - Click **Create Agent**
   - Enter a name (e.g., "Engineering Docs")
   - Add a sitemap URL or upload files
   - Click **Create**

3. **Wait for indexing:**

   The status will show "Indexing" until complete. This takes 1-10 minutes depending on content size.

4. **Enable MCP Server:**

   - Click **Deploy** in the left sidebar
   - Click **MCP Server (Beta)**
   - Click **Generate MCP Token**
   - Copy the token

5. **Configure environment variables:**

   ```bash
   export CUSTOMGPT_API_KEY="your-api-key"
   export CUSTOMGPT_PROJECT_ID="12345"        # From the agent URL
   export CUSTOMGPT_MCP_TOKEN="your-token"    # From step 4
   ```

6. **Verify:**

   ```bash
   /customgpt:ask "What topics are covered?"
   ```

**Done.** Your agent is connected.

---

## Switch Between Agents

**Goal:** Change which knowledge base Claude Code queries.

**Prerequisites:**
- Multiple agents in your CustomGPT account
- API key configured

### Steps

1. **List available agents:**

   ```bash
   /customgpt:list-agents
   ```

   Output:
   ```
   | ID    | Name              | Pages | Status |
   |-------|-------------------|-------|--------|
   | 12345 | Engineering Docs  | 150   | Active |  <-- current
   | 12346 | Security Policies | 45    | Active |
   | 12347 | API Reference     | 200   | Active |
   ```

2. **Switch to a different agent:**

   By ID:
   ```bash
   /customgpt:use-agent 12346
   ```

   By name:
   ```bash
   /customgpt:use-agent "Security Policies"
   ```

3. **Update environment (if needed):**

   If switching requires a different MCP token, the command will guide you.

4. **Verify the switch:**

   ```bash
   /customgpt:ask "What topics are covered in this knowledge base?"
   ```

**Done.** You are now querying a different knowledge base.

---

## Query Documentation Effectively

**Goal:** Get accurate, useful answers from your knowledge base.

### Write Specific Queries

**Less effective:**
```
/customgpt:ask "authentication"
```

**More effective:**
```
/customgpt:ask "How do I implement JWT token refresh in our authentication service?"
```

### Include Context

**Less effective:**
```
/customgpt:ask "What is the limit?"
```

**More effective:**
```
/customgpt:ask "What is the API rate limit for the payment service in production?"
```

### Ask for Specific Sections

```
/customgpt:ask "What does the runbook say about database failover procedures?"
/customgpt:ask "According to our security guidelines, how should we handle PII?"
```

### Use Follow-Up Queries

First query:
```
/customgpt:ask "What is our error handling policy?"
```

Follow-up:
```
/customgpt:ask "Show me examples of error response formats from that policy"
```

### Query Types That Work Well

| Query Type | Example |
|------------|---------|
| Procedures | "What are the steps to deploy to production?" |
| Policies | "What is our data retention policy?" |
| Architecture | "How is the payment service structured?" |
| Configuration | "What are the required environment variables for the auth service?" |
| Troubleshooting | "According to the runbook, how do I debug Redis connection issues?" |

---

## Use RAG with Code Reviews

**Goal:** Compare code against your organization's documented standards.

### Steps

1. **Open the file for review:**

   Have Claude read the code:
   ```
   "Review the authentication logic in src/auth/token.py"
   ```

2. **Ask for guideline comparison:**

   ```
   "Does this implementation follow our error handling guidelines?"
   ```

   Claude will:
   - Query your documentation for error handling guidelines
   - Compare the code against those guidelines
   - Report any discrepancies

3. **Request specific checks:**

   ```
   "Check if this code follows our security requirements for handling user data"
   "Verify this matches our logging standards from the ops runbook"
   "Compare this API design to our REST API guidelines"
   ```

### Example Workflow

```
User: Review the error handling in src/api/handlers.py

Claude: [reads file]

User: Does this follow our error handling policy?

Claude: I'll check your documentation...

According to your Error Handling Guidelines (Section 3.1):
- Errors should include a correlation ID - This code is missing correlation IDs
- HTTP 5xx responses must be logged at ERROR level - Logging is at WARNING level
- Error responses must follow the standard format - Format matches the standard

Recommendations:
1. Add correlation ID to error responses
2. Change logging level from WARNING to ERROR for 5xx responses

Source: `error-handling-guidelines.md` (Section 3.1)
```

---

## Troubleshoot Connection Issues

**Goal:** Diagnose and fix configuration problems.

### Step 1: Run Diagnostics

```bash
/customgpt:troubleshoot
```

This checks:
- Environment variables are set
- API key is valid
- MCP token is valid
- Agent exists and is active

### Step 2: Common Issues and Fixes

**Issue: "CUSTOMGPT_API_KEY not set"**

```bash
export CUSTOMGPT_API_KEY="your-api-key"
```

Get your key at: Profile -> API Keys

**Issue: "401 Unauthorized" from REST API**

Your API key is invalid or expired:
1. Go to [app.customgpt.ai](https://app.customgpt.ai)
2. Profile -> API Keys
3. Generate a new key
4. Update your environment variable

**Issue: "Project not found"**

Your project ID is incorrect:
```bash
# List your agents to find the correct ID
curl -s -X GET "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" | jq '.data.data[] | {id, project_name}'
```

**Issue: "MCP connection failed"**

Your MCP token may be invalid:
1. Go to your agent in CustomGPT
2. Deploy -> MCP Server (Beta)
3. Click **Regenerate Token**
4. Update `CUSTOMGPT_MCP_TOKEN`

**Issue: "No results found"**

Your knowledge base may not have relevant content:
1. Check the agent has finished indexing (status: Active)
2. Verify content was indexed (Sources tab in CustomGPT)
3. Try a broader query
4. Query for topics you know exist in your docs

### Step 3: Manual Verification

Test REST API:
```bash
curl -s -X GET "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Accept: application/json" | head -100
```

Check environment:
```bash
echo "API_KEY: ${CUSTOMGPT_API_KEY:+SET}"
echo "PROJECT_ID: $CUSTOMGPT_PROJECT_ID"
echo "MCP_TOKEN: ${CUSTOMGPT_MCP_TOKEN:+SET}"
```

---

## Store Credentials Securely

**Goal:** Keep your tokens safe and persistent.

### Option 1: Shell Profile (Personal Machine)

Add to `~/.bashrc` or `~/.zshrc`:

```bash
# CustomGPT Configuration
export CUSTOMGPT_API_KEY="your-api-key"
export CUSTOMGPT_PROJECT_ID="12345"
export CUSTOMGPT_MCP_TOKEN="your-mcp-token"
```

Reload:
```bash
source ~/.bashrc
```

### Option 2: .env File (Project-Specific)

Create `.env` in your project:

```bash
CUSTOMGPT_API_KEY=your-api-key
CUSTOMGPT_PROJECT_ID=12345
CUSTOMGPT_MCP_TOKEN=your-mcp-token
```

Add to `.gitignore`:
```bash
echo ".env" >> .gitignore
```

Load with direnv or similar tool.

### Option 3: Secrets Manager (Production/CI)

AWS Secrets Manager:
```bash
export CUSTOMGPT_API_KEY=$(aws secretsmanager get-secret-value \
  --secret-id customgpt/api-key \
  --query SecretString --output text)
```

1Password CLI:
```bash
export CUSTOMGPT_API_KEY=$(op read "op://Private/CustomGPT/api-key")
```

### Security Best Practices

- Never commit tokens to version control
- Rotate tokens every 90 days
- Use different tokens for dev/staging/prod
- Revoke tokens when team members leave

---

## Create an Agent from a Sitemap

**Goal:** Index an entire documentation site.

### Steps

1. **Find your sitemap URL:**

   Common locations:
   - `https://docs.example.com/sitemap.xml`
   - `https://example.com/sitemap.xml`
   - `https://example.com/sitemap_index.xml`

2. **Create the agent via API:**

   ```bash
   curl -X POST "https://app.customgpt.ai/api/v1/projects" \
     -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
     -H "Content-Type: multipart/form-data" \
     -F "project_name=My Documentation" \
     -F "sitemap_path=https://docs.example.com/sitemap.xml"
   ```

3. **Note the project ID from the response:**

   ```json
   {
     "data": {
       "id": 12345,
       "project_name": "My Documentation",
       ...
     }
   }
   ```

4. **Wait for indexing:**

   Check status:
   ```bash
   curl -s -X GET "https://app.customgpt.ai/api/v1/projects/12345" \
     -H "Authorization: Bearer $CUSTOMGPT_API_KEY" | jq '.data.is_chat_active'
   ```

   `true` means indexing is complete.

5. **Enable MCP and get token:**

   Go to the agent -> Deploy -> MCP Server (Beta) -> Generate Token

**Done.** Your documentation site is now queryable.

---

## Create an Agent from Uploaded Files

**Goal:** Index PDFs, Markdown, or other files.

### Via Dashboard (Recommended)

1. Go to [app.customgpt.ai/projects](https://app.customgpt.ai/projects)
2. Click **Create Agent**
3. Choose **Upload Files**
4. Drag and drop your files (PDF, MD, TXT, DOCX supported)
5. Click **Create**

### Via API

```bash
curl -X POST "https://app.customgpt.ai/api/v1/projects" \
  -H "Authorization: Bearer $CUSTOMGPT_API_KEY" \
  -H "Content-Type: multipart/form-data" \
  -F "project_name=Uploaded Docs" \
  -F "file=@/path/to/document.pdf"
```

For multiple files, make multiple upload requests to the project's pages endpoint.

---

## Integrate with CI/CD

**Goal:** Use RAG queries in automated workflows.

### Use Case: Documentation Validation

Check that code changes match documented standards:

```yaml
# .github/workflows/docs-check.yml
name: Documentation Compliance Check

on: [pull_request]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Check code against guidelines
        env:
          CUSTOMGPT_API_KEY: ${{ secrets.CUSTOMGPT_API_KEY }}
          CUSTOMGPT_PROJECT_ID: ${{ secrets.CUSTOMGPT_PROJECT_ID }}
          CUSTOMGPT_MCP_TOKEN: ${{ secrets.CUSTOMGPT_MCP_TOKEN }}
        run: |
          # Query for relevant guidelines
          # Compare changed files against retrieved standards
          # Report any discrepancies
```

### Use Case: Automated Changelog Context

Include relevant documentation in release notes:

```bash
# Get context for recent changes
/customgpt:ask "What is the documented purpose of the payment service retry logic?"
```

### Use Case: Onboarding Automation

Generate onboarding materials from your documentation:

```bash
/customgpt:ask "Create a summary of the development environment setup process"
```

---

## Use Multiple Knowledge Bases per Project

**Goal:** Query different documentation sets for different purposes.

### Scenario

You have:
- **Engineering Docs** (ID: 12345) - Architecture, standards
- **Ops Runbook** (ID: 12346) - Incident response, debugging
- **Security Policies** (ID: 12347) - Compliance, security requirements

### Approach 1: Switch on Demand

```bash
# For architecture questions
/customgpt:use-agent 12345
/customgpt:ask "How is the authentication service designed?"

# For incident response
/customgpt:use-agent 12346
/customgpt:ask "What is the procedure for database failover?"

# For compliance questions
/customgpt:use-agent 12347
/customgpt:ask "What are our GDPR requirements?"
```

### Approach 2: Project-Specific Defaults

Set different defaults in different project directories using `.env`:

```bash
# In /projects/backend/.env
CUSTOMGPT_PROJECT_ID=12345  # Engineering Docs

# In /projects/ops/.env
CUSTOMGPT_PROJECT_ID=12346  # Ops Runbook
```

### Approach 3: Unified Knowledge Base

Combine all documentation into one agent:
1. Create a new agent
2. Add multiple sitemaps or upload all files
3. Use a single configuration everywhere

This simplifies setup but may reduce query precision.

---

## Next Steps

- [Reference](REFERENCE.md) - Full command and configuration details
- [Explanation](EXPLANATION.md) - How RAG works, architecture decisions
- [Quick Start](QUICKSTART.md) - Initial setup if you have not done it

---

Need help? Contact [hello@customgpt.ai](mailto:hello@customgpt.ai) or [open an issue](https://github.com/Poll-The-People/customgpt-claude-code/issues).
