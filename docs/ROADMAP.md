# CustomGPT.ai Claude Code Plugin - Roadmap

## Current Status: v1.1.0

✅ Simplified NUX: paste MCP URL, done
✅ 3 commands: setup, ask, troubleshoot
✅ RAG skill for automatic retrieval
✅ Full documentation (Diátaxis)

---

## Next Steps

### Priority 1: Test the New Flow End-to-End

**Goal:** Verify the simplified setup works for a fresh user.

- [ ] Remove existing MCP config: `claude mcp remove customgpt`
- [ ] Fresh install: clone repo, run `claude --plugin-dir ./customgpt`
- [ ] Run `/customgpt:setup` and paste MCP URL
- [ ] Restart Claude Code
- [ ] Verify `/mcp` shows customgpt connected with 28 tools
- [ ] Test `/customgpt:ask "What topics are in my knowledge base?"`
- [ ] Test natural language RAG trigger

### Priority 2: Publish to GitHub Marketplace

**Goal:** Make plugin installable via `/plugin install`.

- [ ] Verify repo structure matches Claude Code plugin requirements
- [ ] Test: `/plugin marketplace add Poll-The-People/customgpt-claude-code`
- [ ] Test: `/plugin install customgpt`
- [ ] Update README with marketplace installation instructions
- [ ] Announce on CustomGPT.ai blog/social

### Priority 3: Multi-Agent Support (v1.2.0)

**Goal:** Allow users to switch between knowledge bases.

- [ ] Add `/customgpt:add-agent` command (paste another MCP URL)
- [ ] Add `/customgpt:switch-agent` command (switch between configured agents)
- [ ] Store multiple agent configs in `~/.claude.json`
- [ ] Update documentation

### Priority 4: Enhanced UX

**Goal:** Polish the user experience.

- [ ] Add `/customgpt:status` command (show current agent info)
- [ ] Better error messages with actionable fixes
- [ ] Add retry logic for transient failures
- [ ] Cache agent name from MCP response for display

### Priority 5: Official Anthropic Marketplace

**Goal:** Get listed in `claude-plugins-official`.

- [ ] Meet all marketplace requirements
- [ ] Create demo video
- [ ] Write security documentation
- [ ] Collect user testimonials
- [ ] Submit for review

---

## Known Issues

1. **Env var expansion bug in plugin .mcp.json** - Workaround: users add MCP via `claude mcp add` during setup
2. **Restart required after setup** - MCP servers connect on startup

---

## Out of Scope (for now)

- Agent creation from CLI (use app.customgpt.ai)
- File upload to agent (use app.customgpt.ai)
- Usage analytics/telemetry
- Conversation persistence across sessions

---

## Contact

- **Repo:** https://github.com/Poll-The-People/customgpt-claude-code
- **Issues:** https://github.com/Poll-The-People/customgpt-claude-code/issues
- **Email:** hello@customgpt.ai
