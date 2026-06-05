# Outlook / Hotmail / Live.com Configuration

Himalaya works with Outlook/Hotmail accounts using Microsoft's Exchange/Outlook.com IMAP and SMTP servers.

## Server Settings

| Protocol | Server | Port | Encryption |
|----------|--------|------|------------|
| IMAP (read) | `outlook.office365.com` | 993 | TLS |
| SMTP (send) | `smtp.office365.com` | 587 | STARTTLS |

## Important: You Need an App Password

Microsoft accounts **require an app password** for IMAP/SMTP access. Your regular password will NOT work, especially if 2FA is enabled.

### How to Generate an App Password

1. Go to **https://account.live.com/proofs/AppPassword** (sign in with your Microsoft account)
2. Create a new app password — name it something like "Hermes Bot"
3. Copy the 16-character password immediately — it won't be shown again
4. Use this as your IMAP/SMTP password

**Note:** You may need to first enable two-step verification at https://account.live.com/proofs/Manage/additional before app passwords become available.

## Config Template

```toml
[accounts.personal]
email = "you@hotmail.com"
display-name = "Your Name"
default = true

# IMAP backend for reading emails
backend.type = "imap"
backend.host = "outlook.office365.com"
backend.port = 993
backend.encryption.type = "tls"
backend.login = "you@hotmail.com"
backend.auth.type = "password"
backend.auth.cmd = "pass show microsoft/app-password"

# SMTP backend for sending emails
message.send.backend.type = "smtp"
message.send.backend.host = "smtp.office365.com"
message.send.backend.port = 587
message.send.backend.encryption.type = "start-tls"
message.send.backend.login = "you@hotmail.com"
message.send.backend.auth.type = "password"
message.send.backend.auth.cmd = "pass show microsoft/app-password"

# Folder aliases (Outlook uses standard names, but be explicit)
folder.aliases.inbox = "INBOX"
folder.aliases.sent = "Sent"
folder.aliases.drafts = "Drafts"
folder.aliases.trash = "Trash"
```

## Alternative: Direct Password (Testing Only)

For testing without `pass`:

```toml
backend.auth.cmd = "echo YOUR_APP_PASSWORD_HERE"
```

Or using an environment variable:

```toml
backend.auth.cmd = "echo ${HOTMAIL_APP_PASSWORD}"
```

## Common Issues

- **"Login failed"**: Regular passwords don't work. Use an app password.
- **"Connection refused"**: Ensure IMAP is enabled in Outlook.com settings (Settings → Mail → Sync email → IMAP enabled)
- **STARTTLS errors on port 587**: Make sure `encryption.type = "start-tls"` not `"tls"` for SMTP
- **2FA enabled without app password**: You MUST use an app password. Regular passwords are rejected when 2FA is on.
- **Outlook.com vs Live.com vs Hotmail.com**: All use the same `outlook.office365.com` / `smtp.office365.com` servers regardless of email domain.

## Verification

```bash
# Test IMAP connection — should list inbox messages
himalaya envelope list

# Test SMTP — send a test email
cat << 'EOF' | himalaya template send
From: you@hotmail.com
To: recipient@gmail.com
Subject: Himalaya Test
This is a test from Himalaya email CLI.
EOF
```
