# Gmail + Himalaya Setup (Working Session)

This file documents the successful Gmail + Himalaya configuration from the session where the agentic AI newsletter was set up.

## Working Configuration

```toml
[accounts.personal]
email = "dev.gauravajit@gmail.com"
display-name = "Hermes Bot"
default = true

[accounts.personal.backend]
type = "imap"
host = "imap.gmail.com"
port = 993
encryption.type = "tls"
login = "dev.gauravajit@gmail.com"
auth.type = "password"
auth.cmd = "echo kviahxpxxhbbyblg"

[accounts.personal.message.send]
  backend.type = "smtp"
  backend.host = "smtp.gmail.com"
  backend.port = 587
  backend.encryption.type = "start-tls"
  backend.login = "dev.gauravajit@gmail.com"
  backend.auth.type = "password"
  backend.auth.cmd = "echo kviahxpxxhbbyblg"

[accounts.personal.folder.aliases]
inbox = "INBOX"
sent = "[Gmail]/Sent Mail"
drafts = "[Gmail]/Drafts"
trash = "[Gmail]/Trash"
```

## Key Points for Gmail

1. **App Password Required**: Must use an App Password (not regular password) with 2FA enabled
2. **IMAP Settings**: 
   - Host: `imap.gmail.com`
   - Port: `993`
   - Encryption: `tls` (SSL/TLS)
3. **SMTP Settings**:
   - Host: `smtp.gmail.com`  
   - Port: `587`
   - Encryption: `start-tls` (STARTTLS)
4. **Folder Aliases**: Critical for Gmail - use `[Gmail]/Sent Mail` etc. format
5. **Auth Command**: Simple `echo` of the App Password works for automation

## Verification Commands That Worked

```bash
# Test IMAP connection
himalaya folder list

# Test sending via template
cat <<EOF | himalaya template send
From: dev.gauravajit@gmail.com
To: test@example.com
Subject: Test
Body
This is a test.
EOF

# For multiple recipients, use comma-separated list in To: header
```

## Troubleshooting Notes

- **Authentication Failed**: Usually means wrong App Password or 2FA not enabled
- **Folder Errors**: Missing `[Gmail]/` prefix in aliases causes sent mail to go to wrong place
- **Port Confusion**: Gmail uses 993 for IMAP, 587 for SMTP with STARTTLS (not 465 for SSL)
- **App Password Format**: 16 characters, often displayed in 4-4-4-4 groups but entered without spaces

## Security Note
The App Password `kviahxpxxhbbyblg` used in this session should be revoked after use and regenerated for future sessions.