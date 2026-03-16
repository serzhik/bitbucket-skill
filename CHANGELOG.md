# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.0.2] - 2026-03-16

### Added

- `CHANGELOG.md` for tracking project changes

### Removed

- `BB_CLI_CONFIG.md` — removed unused bb-cli (PHP) configuration docs
- bb-cli alternative section from `skill.md` and `README.md` — the skill uses only the Python API client

## [0.0.1] - 2026-03-16

### Added

- `skill.md` — skill entry point with command definitions, triggers, and config setup
- `bitbucket_api.py` — Python CLI client for Bitbucket Cloud REST API 2.0 (stdlib only, no external dependencies)
  - Pull request management: create, list, view, merge, decline
  - PR comments: add general and inline comments
  - Pipeline listing with status, duration, and trigger info
  - Auto-detection of workspace/repo from `git remote`
  - Flexible auth: App Password, Repository Access Token, API Token
- `scripts/bb-comment.sh` — shell script for adding single inline PR comments
- `scripts/bb-review.sh` — shell script for batch code review with multiple inline comments via TSV file
- `references/api_reference.md` — direct Bitbucket REST API reference for advanced use cases
- `README.md` — installation, authentication, and usage documentation
- `.gitignore` to exclude IDE files (`.idea/`) and credential config files (`*.config`)
- `LICENSE` — MIT License (Serhii Koval, Zghraia Software)
