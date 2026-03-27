# claude-mailcow

A Claude Code skill for managing [Mailcow](https://mailcow.email/) mail servers via the Mailcow API.

## What it does

This skill lets Claude Code interact with your Mailcow instance to:

- Manage **mailboxes** (create, edit, delete, list)
- Manage **domains** (create, edit, delete, list)
- Manage **aliases** (create, edit, delete, list)
- Generate and manage **DKIM** keys
- Configure **anti-spam** policies
- View and manage **quarantine**
- Inspect and flush the **mail queue**
- View **logs** (SMTP, IMAP, Rspamd, autodiscover, etc.)
- Check **container status** and system health
- Manage **Fail2Ban**, **rate limits**, **TLS policies**, **routing**, and more

## Installation

### Option 1: Add as a git submodule

```bash
cd your-project
git submodule add https://github.com/paul1404/claude-mailcow.git .claude/skills/mailcow
```

### Option 2: Copy the skill files

1. Copy `.claude/skills/mailcow.md` into your project's `.claude/skills/` directory
2. Copy the `references/` directory alongside it (or adjust the path in the skill file)

### Option 3: Clone and use directly

```bash
git clone https://github.com/paul1404/claude-mailcow.git
cd claude-mailcow
claude  # start Claude Code in this directory
```

## Usage

Once the skill is installed, just ask Claude Code to do Mailcow tasks in natural language:

- "List all mailboxes"
- "Create a new mailbox info@example.com"
- "Add an alias support@example.com -> admin@example.com"
- "Generate DKIM keys for example.com"
- "Check the mail queue"
- "Show me the last 50 SMTP log entries"
- "What's the container status?"

Claude will ask you for your `MAILCOW_HOST` and `MAILCOW_API_KEY` if not already provided.

## Requirements

- A running Mailcow instance with API access enabled
- An API key from Mailcow UI (**Access > API**)
  - Read-only key for viewing data
  - Read-write key for creating/editing/deleting resources

## File structure

```
.claude/skills/mailcow.md   # The skill definition
references/endpoints.md      # Complete API endpoint reference
```

## Security

- API keys are never hardcoded or logged
- Use IP-restricted API keys where possible
- Destructive actions (delete, flush) always require confirmation
