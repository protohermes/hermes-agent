# Gmail Configuration for Himalaya

## Prerequisites
1. Enable 2-Factor Authentication on your Google Account
2. Generate an App Password for "Mail" service

## Configuration Template
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
auth.cmd = "echo YOUR_APP_PASSWORD_HERE"  # 16-character app password

[accounts.personal.message.send]
backend.type = "smtp"
backend.host = "smtp.gmail.com"
backend.port = 587
backend.encryption.type = "start-tls"
backend.login = "yourname@gmail.com"
backend.auth.type = "password"
auth.cmd = "echo YOUR_APP_PASSWORD_HERE"

[accounts.personal.folder.aliases]
inbox = "INBOX"
sent = "[Gmail]/Sent Mail"
drafts = "[Gmail]/Drafts"
trash = "[Gmail]/Trash"
```

## Important Notes
- **Folder aliases are REQUIRED for Gmail** - without them, `himalaya message send` will appear to succeed but actually fail silently, causing retries that send duplicate emails
- The app password is 16 characters, typically shown in 4-blocks (abcd efgh ijkl mnop) but should be used without spaces
- Store the app password securely - consider using `pass` or system keyring instead of plain text in config
- Test configuration with: `himalaya folder list` and `himalaya envelope list`

## Troubleshooting
If you get "Invalid credentials" errors:
1. Verify 2FA is enabled on your Google Account
2. Regenerate the app password at https://myaccount.google.com/apppasswords
3. Ensure you're using the exact 16-character password (no spaces)
4. Check that the app password was generated for "Mail" service