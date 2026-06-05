---
name: himalaya
description: "Himalaya CLI: IMAP/SMTP email from terminal."
version: 1.1.0
author: community
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Email, IMAP, SMTP, CLI, Communication]
    homepage: https://github.com/pimalaya/himalaya
prerequisites:
  commands: [himalaya]
---

# Himalaya Email CLI

Himalaya is a CLI email client that lets you manage emails from the terminal using IMAP, SMTP, Notmuch, or Sendmail backends.

This skill is separate from the Hermes Email gateway adapter. The gateway
adapter lets people email the agent and uses Hermes' built-in IMAP/SMTP
adapter; this skill lets the agent operate a mailbox from terminal tools and
requires the external `himalaya` CLI.

## References\\\\n\\\\n- `references/configuration.md` (config file setup + IMAP/SMTP authentication)\\\\n- `references/message-composition.md` (MML syntax for composing emails)\\\\n- `references/outlook-hotmail.md` (Outlook/Hotmail/Microsoft 365 config template)\\\\n- `references/gmail.md` (Gmail/App Password configuration guide)\\\\n- `references/gmail-app-password.md` (Detailed Gmail App Password setup guide)\\\\n- `references/gmail-himalaya-setup.md` (Step-by-step Gmail + Himalaya setup from working session)\\\\n- `references/gmail-himalaya-setup.md` (Step-by-step Gmail + Himalaya setup from working session)\\\\n- `references/gmail-himalaya-setup.md` (Step-by-step Gmail + Himalaya setup from working session)

## Prerequisites

1. Himalaya CLI installed (`himalaya --version` to verify)
2. A configuration file at `~/.config/himalaya/config.toml`
3. IMAP/SMTP credentials configured (password stored securely)
4. **For Gmail**: 2-Factor Authentication enabled and App Password generated (see `references/gmail-app-password.md`)

### Installation

> **Security note:** The following installation method downloads and executes a script directly. 
> For maximum security, download the script first, inspect its contents, then run it.
> Alternatively, use the Homebrew or cargo methods below which involve less risk.

```bash
# Pre-built binary (Linux/macOS — recommended)
# Option 1: Download and inspect first (most secure)
curl -sSL https://raw.githubusercontent.com/pimalaya/himalaya/master/install.sh -o himalaya_install.sh
# Review himalaya_install.sh contents, then:
PREFIX=~/.local sh himalaya_install.sh

# Option 2: Direct execution (less secure, not recommended for production)
# curl -sSL https://raw.githubusercontent.com/pimalaya/himalaya/master/install.sh | PREFIX=~/.local sh

# macOS via Homebrew
brew install himalaya

# Or via cargo (any platform with Rust)
cargo install himalaya --locked
```

## Gmail-Specific Configuration

For Gmail/Google Workspace accounts, use these specific settings:

```toml
[accounts.personal]
email = "yourname@gmail.com"
display-name = "Your Name"
default = true

[accounts.personal.backend]
type = "imap"
host = "imap.gmail.com"
port = 993
encryption.type = "tls"
login = "yourname@gmail.com"
auth.type = "password"
auth.cmd = "echo YOUR_APP_PASSWORD_HERE"  # Replace with actual app password

[accounts.personal.message.send]
  backend.type = "smtp"
  backend.host = "smtp.gmail.com"
  backend.port = 587
  backend.encryption.type = "start-tls"
  backend.login = "yourname@gmail.com"
  backend.auth.type = "password"
  backend.auth.cmd = "echo YOUR_APP_PASSWORD_HERE"  # Replace with actual app password

[accounts.personal.folder.aliases]
inbox = "INBOX"
sent = "[Gmail]/Sent Mail"
drafts = "[Gmail]/Drafts"
trash = "[Gmail]/Trash"
```

> **Critical Notes for Gmail:**
> - **App Password Required**: Must use an App Password (not regular password) with 2-Factor Authentication enabled
> - **Folder Aliases**: Gmail requires the `[Gmail]/` prefix for special folders (Sent Mail, Drafts, Trash)
> - **IMAP Port**: 993 with TLS encryption
> - **SMTP Port**: 587 with STARTTLS encryption
> - **Security**: Never commit your App Password to version control - use environment variables or secure secret management in production

> **Heads up on the alias syntax.** Pre-v1.2.0 docs used a
> `[accounts.NAME.folder.alias]` sub-section (singular `alias`).
> v1.2.0 silently ignores that form — TOML parses fine, but the
> alias resolver never reads it, so every lookup falls through to
> the canonical name. On Gmail this means save-to-Sent fails *after*
> SMTP delivery succeeds, and `himalaya message send` exits non-zero.
> Any caller (agent, script, user) that retries on that exit code
> will re-run the entire send — including SMTP — producing duplicate
> emails to recipients. Always use `folder.aliases.X` (plural, dotted
> keys, directly under `[accounts.NAME]`).

## Hermes Integration Notes

- **Reading, listing, searching, moving, deleting** all work directly through the terminal tool
- **Composing/replying/forwarding** — piped input (`cat << EOF | himalaya template send`) is recommended for reliability. Interactive `$EDITOR` mode works with `pty=true` + background + process tool, but requires knowing the editor and its commands
- Use `--output json` for structured output that's easier to parse programmatically
- The `himalaya account configure` wizard requires interactive input — use PTY mode: `terminal(command="himalaya account configure", pty=true)`

## Common Operations

### List Folders

```bash
himalaya folder list
```

### List Emails

List emails in INBOX (default):

```bash
himalaya envelope list
```

List emails in a specific folder:

```bash
himalaya envelope list --folder "Sent"
```

List with pagination:

```bash
himalaya envelope list --page 1 --page-size 20
```

### Search Emails

```bash
himalaya envelope list from john@example.com subject meeting
```

### Read an Email

Read email by ID (shows plain text):

```bash
himalaya message read 42
```

Export raw MIME:

```bash
himalaya message export 42 --full
```

### Reply to an Email

To reply non-interactively from Hermes, read the original message, compose a reply, and pipe it:

```bash
# Get the reply template, edit it, and send
himalaya template reply 42 | sed 's/^$/\nYour reply text here\n/' | himalaya template send
```

Or build the reply manually:

```bash
cat << 'EOF' | himalaya template send
From: you@example.com
To: sender@example.com
Subject: Re: Original Subject
In-Reply-To: <original-message-id>

Your reply here.
EOF
```

Reply-all (interactive — needs $EDITOR, use template approach above instead):

```bash
himalaya message reply 42 --all
```

### Forward an Email

```bash
# Get forward template and pipe with modifications
himalaya template forward 42 | sed 's/^To:.*/To: newrecipient@example.com/' | himalaya template send
```

### Write a New Email

**Non-interactive (use this from Hermes)** — pipe the full email via stdin to `template send`:

```bash
cat << 'EOF' | himalaya template send
From: you@example.com
To: recipient@example.com
Subject: Test Message

Hello from Himalaya!
EOF
```

**For multiple recipients**, separate with commas in the To: header:

```bash
cat << 'EOF' | himalaya template send
From: you@example.com
To: recipient1@example.com, recipient2@example.com
Subject: Test Message

Hello from Himalaya!
EOF
```

**Do NOT use** `himalaya message write` for non-interactive sending as it opens `$EDITOR` and requires PTY mode. The `template send` approach with stdin piping is more reliable for automation.

> **Pro tip from production use**: When automating email sending (like in cron jobs), always use the `template send` approach with heredoc or file redirection. This avoids editor dependencies and works reliably in non-interactive environments. For example:
> ```bash
> himalaya template send < /path/to/email.txt
> ```
> or
> ```bash
> cat /path/to/email.txt | himalaya template send
> ```

Note: `himalaya message write` without piped input opens `$EDITOR`. This works with `pty=true` + background mode, but piping to `template send` is simpler and more reliable for automated workflows.

### Move/Copy Emails

Move to folder:

```bash
himalaya message move 42 "Archive"
```

Copy to folder:

```bash
himalaya message copy 42 "Important"
```

### Delete an Email

```bash
himalaya message delete 42
```

### Manage Flags

Add flag:

```bash
himalaya flag add 42 --flag seen
```

Remove flag:

```bash
himalaya flag remove 42 --flag seen
```

## Multiple Accounts

List accounts:

```bash
himalaya account list
```

Use a specific account:

```bash
himalaya --account work envelope list
```

## Attachments

Save attachments from a message:

```bash
himalaya attachment download 42
```

Save to specific directory:

```bash
himalaya attachment download 42 --dir ~/Downloads
```

## Output Formats

Most commands support `--output` for structured output:

```bash
himalaya envelope list --output json
himalaya envelope list --output plain
```

## Debugging

Enable debug logging:

```bash
RUST_LOG=debug himalaya envelope list
```

Full trace with backtrace:

```bash
RUST_LOG=trace RUST_BACKTRACE=1 himalaya envelope list
```

## Tips

- Use `himalaya --help` or `himalaya <command> --help` for detailed usage.
- Message IDs are relative to the current folder; re-list after folder changes.
- For composing rich emails with attachments, use MML syntax (see `references/message-composition.md`).
- Store passwords securely using `pass`, system keyring, or a command that outputs the password.
