# Bitbucket CLI Configuration

## Config File Location

`~/.bitbucket-rest-cli-config.json`

## Config Structure

```json
{
  "auth": {
    "username": "your-email@example.com",
    "appPassword": "your-bitbucket-api-token"
  }
}
```

## Getting Bitbucket API Token

> **Note:** App Passwords are deprecated (disabled Sept 2025, removed June 2026). Use API Tokens instead.

### Steps:

1. Go to: **Bitbucket** → **Settings** → **Atlassian account settings** → **Security**
2. Click **"Create and manage API tokens"** → **"Create API token with scopes"**
3. Select **"Bitbucket"** as the app
4. Grant permissions:
   - Repositories: Read, Write
   - Pull Requests: Read, Write
   - Pipelines: Read, Write
5. Copy the token and use it as `appPassword` in config

## Important Notes

- **Username**: Use your email address (e.g., `user@example.com`)
- **Atlassian API Token** (starts with `ATATT3x...`): Only for JIRA/Confluence, NOT for Bitbucket
- **Bitbucket API Token**: Created via steps above, different format

## Environment Variables (for scripts)

Scripts can also use environment variables:

| Variable | Description |
|----------|-------------|
| `BB_WORKSPACE` | Bitbucket workspace (e.g., `my-team`) |
| `BB_REPO` | Repository slug (e.g., `my-app`) |
| `BB_USER` | Username (email) |
| `BB_APP_PASSWORD` | API token |

## Troubleshooting

### "Authorization error"
- Verify you're using Bitbucket API Token (not Atlassian API Token)
- Check username is your email address

### "Token is invalid, expired, or not supported"
- You're using Atlassian API Token (`ATATT3x...`) instead of Bitbucket API Token
- Create a new token via Bitbucket settings as described above

### "Config not found"
- Ensure `~/.bitbucket-rest-cli-config.json` exists
- Run `bb auth` to create it interactively

### Rate Limiting
- Bitbucket API has rate limits
- Add delays between requests in batch operations
