# Hermes Agent — Backup

This repo is a backup of my Hermes Agent configuration, skills, and memories.

## Structure

```
skills/          — Custom and overridden skills
memories/        — Persistent memory (MEMORY.md, USER.md)
config/          — Sanitized config files (API keys redacted)
SOUL.md          — Agent personality/system prompt
scripts/         — Utility scripts
docs/            — Documentation
```

## Sensitive files (NOT in this repo)

The following are excluded via `.gitignore`:
- `.env` — API keys (OpenRouter, Telegram, Email)
- `auth.json` — Authentication tokens
- `state.db` — Session database
- `gateway.*` — Runtime files

## Restore

To restore on a fresh Hermes install:
1. Clone this repo into the Hermes home directory
2. Copy skills to `~/.hermes/skills/`
3. Copy memories to `~/.hermes/memories/`
4. Copy SOUL.md to `~/.hermes/SOUL.md`
5. Re-create `.env` with your API keys
6. Copy config files and re-add API keys

## Last backed up

2026-06-05
