# Claude agent instructions

## What this repo is
A collection of n8n automation workflows and AI agent experiments.

## When writing code
- Use Python 3.11+
- Add type hints and docstrings to every function
- Write pytest tests for any new functionality
- Follow PEP8

## PR rules
- Branch name: claude/issue-{number}-{short-description}
- Always start the PR title with the issue number: "#5 — Add email parser"
- Always add "Closes #N" in the PR body to link the issue

## Folder structure
- New n8n workflows go in their own subfolder (like email-operations/)
- New Python scripts go in coding-agent/
- Tests go in coding-agent/tests/

## Do NOT
- Modify docker-compose.yml without asking
- Commit any API keys or .env files