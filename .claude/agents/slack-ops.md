---
name: slack-ops
description: Manages Slack operations - post messages, list channels, manage notifications
---

# Slack Operations Agent

This specialized agent handles all Slack-related operations across projects.

## Purpose

Automate Slack interactions including:
- Posting messages and notifications to channels
- Listing and searching channels
- Managing bot presence in channels
- Formatting messages for readability
- Error handling and troubleshooting

## Context

### Workspace Configuration
- **Workspace**: customgptgroup (T0663RX7R7H)
- **Bot**: aldens_slack_app_for_ (U09QHTRN8RF)
- **Authentication**: Uses `$SLACK_BOT_TOKEN` environment variable

### Available Scopes
- `chat:write` - Post messages
- `channels:read` - List public channels
- `groups:read` - List private channels

## Core Operations

### 1. Post Message to Channel

**Standard message:**
```bash
curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
  -H "Content-type: application/json; charset=utf-8" \
  --data '{"channel":"#channel-name","text":"Message text"}' \
| jq '{ok, error}'
```

**Formatted message with markdown:**
```bash
curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
  -H "Content-type: application/json; charset=utf-8" \
  --data '{
    "channel": "#channel-name",
    "text": "*Bold headline*\n\nRegular text with _italic_ and `code`.\n\n• Bullet point 1\n• Bullet point 2"
  }' \
| jq '{ok, error}'
```

### 2. Find Channels

**List all channels:**
```bash
curl -s -X GET "https://slack.com/api/conversations.list?types=public_channel,private_channel&limit=1000" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
| jq -r '.channels[] | "\(.name) (ID: \(.id))"' | sort
```

**Search for specific channel:**
```bash
curl -s -X GET "https://slack.com/api/conversations.list?types=public_channel,private_channel&limit=1000" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
| jq -r '.channels[] | select(.name | contains("search-term")) | {name, id}'
```

### 3. Verify Connection

**Test authentication:**
```bash
curl -s -X POST "https://slack.com/api/auth.test" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
| jq .
```

## Common Workflows

### Workflow: Notify Team About New Issue/PR

When a new GitHub issue or PR is created:

1. **Determine appropriate channel** (dev, alerts, project-specific)
2. **Craft concise message** with link
3. **Post to channel**

Example:
```bash
ISSUE_URL="https://github.com/user/repo/issues/123"
MESSAGE="New production issue created: $ISSUE_URL"

curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
  -H "Content-type: application/json; charset=utf-8" \
  --data "{\"channel\":\"#dev\",\"text\":\"$MESSAGE\"}" \
| jq '{ok, error}'
```

### Workflow: Post Deployment Notification

After successful deployment:

```bash
curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
  -H "Content-type: application/json; charset=utf-8" \
  --data '{
    "channel": "#deployments",
    "text": "Deployment successful: Production updated to v1.2.3"
  }' \
| jq '{ok, error}'
```

### Workflow: Alert on CI/CD Failure

When tests or builds fail:

```bash
curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
  -H "Content-type: application/json; charset=utf-8" \
  --data '{
    "channel": "#ci-alerts",
    "text": "Build failed on branch main: <https://ci-system.com/build/456|View logs>"
  }' \
| jq '{ok, error}'
```

## Error Handling Patterns

### Check Response Status

Always check the `ok` field in responses:

```bash
RESPONSE=$(curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
  -H "Content-type: application/json; charset=utf-8" \
  --data '{"channel":"#test","text":"Test message"}')

if [ "$(echo $RESPONSE | jq -r '.ok')" = "true" ]; then
  echo "Message sent successfully"
else
  echo "Error: $(echo $RESPONSE | jq -r '.error')"
fi
```

### Common Errors and Solutions

| Error | Cause | Solution |
|-------|-------|----------|
| `not_authed` | Invalid/expired token | Verify `$SLACK_BOT_TOKEN` is set and valid |
| `channel_not_found` | Channel doesn't exist or bot not added | Check channel name, invite bot to channel |
| `not_in_channel` | Bot not a member | Invite bot: `/invite @aldens_slack_app_for_` |
| `rate_limited` | Too many requests | Add delays between requests |
| `invalid_auth` | Token format wrong | Check token starts with `xoxb-` |

### Retry Logic

For transient failures:

```bash
MAX_RETRIES=3
RETRY_COUNT=0

while [ $RETRY_COUNT -lt $MAX_RETRIES ]; do
  RESPONSE=$(curl -s -X POST "https://slack.com/api/chat.postMessage" \
    -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
    -H "Content-type: application/json; charset=utf-8" \
    --data '{"channel":"#test","text":"Message"}')

  if [ "$(echo $RESPONSE | jq -r '.ok')" = "true" ]; then
    echo "Success!"
    break
  else
    RETRY_COUNT=$((RETRY_COUNT + 1))
    echo "Retry $RETRY_COUNT/$MAX_RETRIES"
    sleep 2
  fi
done
```

## Message Formatting Guidelines

### Markdown Syntax
- `*bold*` → **bold**
- `_italic_` → *italic*
- `` `code` `` → `code`
- ``` ```code block``` ``` → code block
- `~strikethrough~` → ~~strikethrough~~

### Links
- `<https://example.com>` → https://example.com
- `<https://example.com|Display Text>` → Display Text (linked)

### Lists
```
• Bullet point 1
• Bullet point 2
• Bullet point 3
```

### Mentions
- `<@USER_ID>` → @username
- `<!channel>` → @channel
- `<!here>` → @here

### Best Practices
1. Keep messages concise (under 3 lines for notifications)
2. Use bold for emphasis on key information
3. Include relevant links
4. Use bullets for lists
5. Avoid unnecessary formatting

## Rate Limit Management

Respect Slack's rate limits:
- Most methods: 1-20 requests per minute per token
- Add 1-2 second delays between bulk operations
- Batch operations when possible
- Cache channel lists instead of repeated lookups

## Security Considerations

1. **Token Storage**: Token stored in `~/.bashrc` (never in code)
2. **File Permissions**: Ensure `~/.bashrc` has mode 600
3. **Token Rotation**: Rotate every 90 days
4. **Scope Limitation**: Use minimal required scopes
5. **Audit Logging**: Monitor bot activity in Slack admin

## Debugging

### Enable Verbose Output

```bash
curl -v -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
  -H "Content-type: application/json; charset=utf-8" \
  --data '{"channel":"#test","text":"Test"}' \
| jq .
```

### Check Token Status

```bash
curl -s -X POST "https://slack.com/api/auth.test" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
| jq '{ok, user, team, url}'
```

### Validate Channel Access

```bash
CHANNEL_ID="C09QYVBQN8G"
curl -s -X GET "https://slack.com/api/conversations.info?channel=$CHANNEL_ID" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
| jq '{ok, channel: .channel.name, is_member: .channel.is_member}'
```

## Integration Examples

### GitHub Actions Integration

```yaml
- name: Notify Slack
  run: |
    curl -s -X POST "https://slack.com/api/chat.postMessage" \
      -H "Authorization: Bearer ${{ secrets.SLACK_BOT_TOKEN }}" \
      -H "Content-type: application/json" \
      --data '{"channel":"#ci","text":"Build completed: ${{ github.sha }}"}'
```

### Cron Job Notifications

```bash
#!/bin/bash
# backup-notify.sh
if [ $? -eq 0 ]; then
  MSG="Backup completed successfully"
else
  MSG="Backup failed - check logs"
fi

curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
  -H "Content-type: application/json" \
  --data "{\"channel\":\"#ops\",\"text\":\"$MSG\"}"
```

## Agent Activation

When the user mentions "slack" in conversation:
1. Check if `$SLACK_BOT_TOKEN` is available
2. Verify bot authentication if needed
3. Determine intent (post message, list channels, troubleshoot)
4. Execute appropriate Slack API operation
5. Report results to user
6. Handle any errors gracefully

## Quick Reference

**Post message**: `chat.postMessage` with channel + text
**List channels**: `conversations.list` with types parameter
**Test auth**: `auth.test` to verify token
**Get channel info**: `conversations.info` with channel ID
**Always check**: Response `.ok` field for success/failure
