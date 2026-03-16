# Bitbucket Skill for Claude Code

A lightweight Bitbucket Cloud CLI skill for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Manage pull requests, pipelines, and code reviews directly from your terminal — no external dependencies required.

## Features

- **Pull Requests**: Create, list, view, merge, decline PRs
- **Comments**: Add general and inline code review comments
- **Pipelines**: View recent pipeline runs and statuses
- **Batch Code Review**: Add multiple inline comments at once
- **Auto-detection**: Workspace and repo are detected from `git remote`

## Installation

1. Copy the `bitbucket` folder to your Claude Code skills directory:

```bash
cp -r bitbucket ~/.claude/skills/bitbucket
```

2. Create config file at `~/.claude/bitbucket.config`:

```json
{
  "username": "your_bitbucket_username",
  "app_password": "your_bitbucket_app_password"
}
```

> `workspace` and `repo_slug` are optional — auto-detected from `git remote get-url origin`.

3. Make scripts executable:

```bash
chmod +x ~/.claude/skills/bitbucket/scripts/*.sh
```

## Authentication

Supports three auth methods (in priority order):

| Method | Config Key | Auth Type |
|--------|-----------|-----------|
| Repository Access Token | `access_token` | Bearer |
| App Password | `username` + `app_password` | Basic |
| API Token | `api_token` | Bearer |

### Getting an App Password

1. Go to **Bitbucket** > **Personal settings** > **App passwords**
2. Create with scopes: **Repositories** (Read/Write), **Pull Requests** (Read/Write)

### Getting a Repository Access Token

1. Go to **Repository settings** > **Access tokens** > **Create**

> **Note**: Atlassian API Tokens (used for JIRA/Confluence) do NOT work with Bitbucket Cloud API.

## Usage

Once installed, use the `/bitbucket` command in Claude Code:

```
/bitbucket create-pr     — Create a pull request
/bitbucket list-prs      — List open PRs
/bitbucket get-pr 123    — View PR details
/bitbucket merge-pr 123  — Merge a PR
/bitbucket pr-comments 123 — View PR comments
/bitbucket pipelines     — View recent pipelines
```

Or use natural language:

```
"Create a PR for the current branch"
"Show me open pull requests"
"Merge PR #42 with squash strategy"
```

## Alternative: bb-cli

The skill also documents [bb-cli](https://github.com/bb-cli/bb-cli), a PHP-based Bitbucket CLI tool, as an alternative backend. See `BB_CLI_CONFIG.md` for setup.

## Requirements

- Python 3.6+ (no external packages — uses stdlib only)
- `jq` (for shell scripts, optional)
- Git (for auto-detection of workspace/repo)

## License

MIT License. See [LICENSE](LICENSE) for details.
