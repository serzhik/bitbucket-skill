---
name: bitbucket
description: >
  Interact with Bitbucket — create PRs, list PRs, view PR details, merge, decline, read comments, view pipelines,
  code review with inline comments, manage environments and variables. Use when user says "/bitbucket pr",
  "/bitbucket create-pr", "/bitbucket list-prs", "/bitbucket pipelines", or similar Bitbucket-related requests.
---

# Bitbucket Skill

Lightweight Bitbucket CLI for Claude Code. Uses a Python script that calls Bitbucket Cloud REST API 2.0 directly and returns **compact output** to minimize token usage.

## Script Location

```
~/.claude/skills/bitbucket/bitbucket_api.py
```

Config: `~/.claude/bitbucket.config`

### Config Setup

Supports two auth methods:

**Option 1: App Password (recommended)**
```json
{
  "username": "your_bitbucket_username",
  "app_password": "your_bitbucket_app_password",
  "workspace": "your-workspace",
  "repo_slug": "your-repo"
}
```
Create: Bitbucket → Personal settings → App passwords (scopes: Repositories Read/Write, Pull requests Read/Write).

**Option 2: Repository Access Token**
```json
{
  "access_token": "your_repository_access_token",
  "workspace": "your-workspace",
  "repo_slug": "your-repo"
}
```
Create: Repository settings → Access tokens → Create.

**Note**: `workspace` and `repo_slug` are optional — auto-detected from `git remote get-url origin`.
**Note**: Atlassian API Tokens (used for JIRA) do NOT work with Bitbucket Cloud API.

## Commands

All commands are run via Bash tool:

```bash
python3 ~/.claude/skills/bitbucket/bitbucket_api.py <command> [args]
```

### Create Pull Request

**Trigger**: `/bitbucket create-pr`, `/bitbucket pr`, "create PR", "open pull request"

```bash
python3 ~/.claude/skills/bitbucket/bitbucket_api.py create-pr "PROJ-123: Fix customer grid" --description "Summary of changes" --destination master
```

- `--description TEXT` — PR description (markdown)
- `--source BRANCH` — source branch (default: current branch)
- `--destination BRANCH` — target branch (default: master)
- `--no-close` — don't close source branch after merge
- Returns: PR number and URL

### List Pull Requests

**Trigger**: `/bitbucket list-prs`, `/bitbucket prs`

```bash
python3 ~/.claude/skills/bitbucket/bitbucket_api.py list-prs [STATE]
```

- STATE: `OPEN` (default), `MERGED`, `DECLINED`, `SUPERSEDED`

### View Pull Request

**Trigger**: `/bitbucket get-pr <ID>`, `/bitbucket pr <ID>`

```bash
python3 ~/.claude/skills/bitbucket/bitbucket_api.py get-pr 123
```

### Merge Pull Request

**Trigger**: `/bitbucket merge-pr <ID>`

```bash
python3 ~/.claude/skills/bitbucket/bitbucket_api.py merge-pr 123 --strategy squash
```

- `--strategy`: `merge_commit` (default), `squash`, `fast_forward`

### Decline Pull Request

**Trigger**: `/bitbucket decline-pr <ID>`

```bash
python3 ~/.claude/skills/bitbucket/bitbucket_api.py decline-pr 123
```

### PR Comments

**Trigger**: `/bitbucket pr-comments <ID>`

```bash
python3 ~/.claude/skills/bitbucket/bitbucket_api.py pr-comments 123
```

### Add Comment to PR

**Trigger**: `/bitbucket add-comment <ID> <text>`

```bash
python3 ~/.claude/skills/bitbucket/bitbucket_api.py add-comment 123 "LGTM! Ready to merge."
```

### Pipelines

**Trigger**: `/bitbucket pipelines`

```bash
python3 ~/.claude/skills/bitbucket/bitbucket_api.py pipelines [COUNT]
```

- COUNT: number of recent pipelines to show (default: 10)

## PR Title Format

PR title **must** follow the format: `PROJ-XXX: Task title`

Example: `PROJ-123: Fix customer grid loading issue`

## PR Description Formatting

Bitbucket PR descriptions use **Markdown** format (not ADF like JIRA).

### Co-Authored-By Line

The text `Co-Authored-By: Claude Opus 4.6 noreply@anthropic.com` **must be italic** in the PR description.

In Markdown, wrap it with `*`:
```
*Co-Authored-By: Claude Opus 4.6 noreply@anthropic.com*
```

### PR Description Template

```markdown
## Summary

- Change description here

## Test Plan

- [ ] Test step here

---

*Co-Authored-By: Claude Opus 4.6 noreply@anthropic.com*
```

## Code Review with Inline Comments

### Single Inline Comment

Use the `bb-comment.sh` script to add inline code review comments:

```bash
~/.claude/skills/bitbucket/scripts/bb-comment.sh <pr-id> <file-path> <line-number> "comment text"
```

Example:
```bash
~/.claude/skills/bitbucket/scripts/bb-comment.sh 42 app/code/Vendor/Module/Model/Example.php 45 "Use dependency injection here"
```

### Batch Code Review

Use `bb-review.sh` for batch review with multiple comments:

```bash
~/.claude/skills/bitbucket/scripts/bb-review.sh <pr-id> <comments-file>
```

Comments file format (TSV — tab separated):
```
file_path<TAB>line_number<TAB>comment_text
```

Example:
```bash
echo -e 'src/Model.php\t45\tUse DI here\nsrc/Controller.php\t120\tAdd try/catch' > /tmp/comments.tsv
~/.claude/skills/bitbucket/scripts/bb-review.sh 42 /tmp/comments.tsv
```

Scripts auto-detect credentials from `~/.bitbucket-rest-cli-config.json` or environment variables (`BB_WORKSPACE`, `BB_REPO`, `BB_USER`, `BB_APP_PASSWORD`).

## Alternative: bb-cli (PHP-based)

For users who prefer a PHP CLI tool, `bb-cli` is also available.

### Installation

```bash
curl -L https://github.com/bb-cli/bb-cli/releases/latest/download/bb -o /usr/local/bin/bb
chmod +x /usr/local/bin/bb
```

Auth: `bb auth` (follow prompts). Config: `~/.bitbucket-rest-cli-config.json`. See [BB_CLI_CONFIG.md](BB_CLI_CONFIG.md).

### bb-cli Commands

| Command | Description |
|---------|-------------|
| `bb pr list [branch]` | List PRs, optionally filter by destination branch |
| `bb pr create <source> <dest>` | Create PR from source to destination branch |
| `bb pr merge <pr-id>` | Merge PR |
| `bb pr approve <pr-id>` | Approve PR |
| `bb pr no-approve <pr-id>` | Remove approval |
| `bb pr request-changes <pr-id>` | Request changes (code review) |
| `bb pr no-request-changes <pr-id>` | Remove request-changes status |
| `bb pr decline <pr-id>` | Decline PR |
| `bb pr diff <pr-id>` | Show PR diff |
| `bb pr commits <pr-id>` | List PR commits |
| `bb pipeline run <branch>` | Run default pipeline for branch |
| `bb pipeline custom <branch> <name>` | Run custom pipeline |
| `bb pipeline get <pipeline-id>` | Get pipeline details |
| `bb pipeline latest` | Get latest pipeline info |
| `bb pipeline wait <pipeline-id>` | Wait for pipeline to complete |
| `bb env environments` | List all environments |
| `bb env variables <env-uuid>` | List environment variables |
| `bb env create-variable <env-uuid> <key> <value> <secured>` | Create variable |
| `bb env update-variable <env-uuid> <var-uuid> <key> <value> <secured>` | Update variable |
| `bb branch list` | List branches |

## Direct API Reference

For advanced use cases not covered by the Python script or bb-cli, see [references/api_reference.md](references/api_reference.md).

## Notes

- Default destination branch: **master**
- Auto-detects workspace/repo from git remote if not in config
- Output is already formatted as markdown — display directly to user
- No external Python dependencies required (uses stdlib `urllib`)
