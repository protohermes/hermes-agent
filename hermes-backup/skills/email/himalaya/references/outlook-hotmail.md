## Outlook / Hotmail / Microsoft 365 Configuration

```toml
[accounts.outlook]
email = "you@hotmail.com"
display-name = "Your Name"
default = true

backend.type = "imap"
backend.host = "outlook.office365.com"
backend.port = 993
backend.encryption.type = "tls"
backend.login = "you@hotmail.com"
backend.auth.type = "password"
backend.auth.cmd = "echo ${HOTMAIL_APP_PASSWORD}"

message.send.backend.type = "smtp"
message.send.backend.host = "smtp.office365.com"
message.send.backend.port = 587
message.send.backend.encryption.type = "start-tls"
message.send.backend.login = "you@hotmail.com"
message.send.backend.auth.type = "password"
message.send.backend.auth.cmd = "echo ${HOTMAIL_APP_PASSWORD}"

folder.aliases.inbox = "INBOX"
folder.aliases.sent = "Sent"
folder.aliases.drafts = "Drafts"
folder.aliases.trash = "Trash"
```

**Generating an App Password:**
1. Sign in at https://account.live.com
2. Enable two-step verification if not already on
3. Go to https://account.live.com/proofs/AppPassword
4. Create a new app password — copy the 16-char code
5. Set `HOTMAIL_APP_PASSWORD` in `~/.hermes/.env`

**Note:** Always use `auth.cmd` with an env var rather than `auth.raw` to avoid storing passwords in plaintext config files.
