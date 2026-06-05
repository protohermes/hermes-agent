# Gmail/App Password Configuration Guide

## Prerequisites
1. **2-Factor Authentication MUST be enabled** on your Google Account
   - Go to: https://myaccount.google.com/security
   - Under "Signing in to Google" → "2-Step Verification" → Turn on

## Creating an App Password
1. Go to: https://myaccount.google.com/apppasswords
2. If prompted, sign in again
3. Select app: **Mail**
4. Select device: **Other** → Type a name (e.g., "Hermes Bot") → Generate
5. **Copy the 16-character password** (it shows in 4 blocks of 4 characters, but use it without spaces)

## Using with Himalaya
In your `~/.config/himalaya/config.toml`:

```toml
[accounts.personal]
email = "yourname@gmail.com"
display-name = "Hermes Bot"
default = true

[accounts.personal.backend]
type = "imap"
host = "imap.gmail.com"
port = 993
encryption.type = "tls"
login = "yourname@gmail.com"
auth.type = "password"
auth.cmd = "echo your-16-char-app-password-here"

[accounts.personal.message.send]
backend.type = "smtp"
backend.host = "smtp.gmail.com"
backend.port = 587
backend.encryption.type = "start-tls"
backend.login = "yourname@gmail.com"
backend.auth.type = "password"
backend.auth.cmd = "echo your-16-char-app-password-here"
```

## Important Notes
- **App passwords are 16 characters** (letters only, no spaces when using)
- **Do NOT use your regular Gmail password** - it will fail with 2FA enabled
- **If you get "Invalid credentials"**: 
  - Double-check you copied the password correctly (no extra spaces)
  - Verify 2FA is enabled on your account
  - Try generating a new app password
- **App passwords can be revoked** at any time from the same page
- **Test with**: `himalaya folder list` (should show your Gmail folders)

## Troubleshooting
- **Authentication failed**: Verify app password is correct and 2FA is enabled
- **IMAP connection refused**: Check network/firewall settings to imap.gmail.com:993
- **SMTP connection refused**: Check network/firewall settings to smtp.gmail.com:587
- **SSL/TLS errors**: Ensure your system has up-to-date CA certificates

## References
- Google App Passwords: https://support.google.com/accounts/answer/185833
- Himalaya Gmail Setup: https://github.com/pimalaya/himalaya#gmail