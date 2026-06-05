# Gmail + Himalaya Setup for Agentic AI Newsletter

Based on successful configuration from session on 2026-06-05.

## Prerequisites
- Gmail account with 2-Factor Authentication enabled
- App password generated for Mail access
- Himalaya CLI installed

## Step-by-Step Configuration

### 1. Install Himalaya CLI
```bash
curl -sSL https://raw.githubusercontent.com/pimalaya/himalaya/master/install.sh | PREFIX=$HOME/.local sh
# Verify installation
$HOME/.local/bin/himalaya --version
```

### 2. Create Gmail App Password
1. Go to https://myaccount.google.com/apppasswords
2. Select app: Mail
3. Select device: Other → Type "Hermes Bot" → Generate
4. Copy the 16-character password (e.g., `kviahxpxxhbbyblg`)

### 3. Configure Himalaya
Create `~/.config/himalaya/config.toml`:

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

### 4. Test Configuration
```bash
# Test IMAP connection
$HOME/.local/bin/himalaya folder list

# Test sending an email
$HOME/.local/bin/himalaya template send <<'EOF'
From: dev.gauravajit@gmail.com
To: test@example.com
Subject: Test from Himalaya

This is a test email to verify Himalaya configuration works.
EOF
```

### 5. Use in Hermes Cron Job
The working cron job command from today's session:

```bash
/opt/hermes/bin/hermes cron create "0 9 * * *" "You are Hermes Agent. Research the latest news about agentic AI assistants, autonomous agents, and AI agent frameworks from the past 24 hours. Use web_search to find 5-8 relevant articles from reputable sources (tech news sites, company blogs, research publications). Extract key points and write a professional newsletter summarizing developments, trends, and insights suitable for a business audience. The newsletter should have: 1) A clear subject line with today's date, 2) Brief intro, 3) Top 3-5 stories with summaries, 4) Framework watch section, 5) Trend to watch, 6) By the numbers if relevant, 7) Closing note. Keep it concise but informative (~400-600 words). Then send the newsletter via email to gaurav_ajit@hotmail.com,roseandrojude@gmail.com using the configured Himalaya account." --name "daily-agentic-newsletter" --deliver "origin"
```

## Troubleshooting Notes from Session

### Issue: "Invalid credentials" with Himalaya
**Root Cause**: App password had accidental spaces when copied
**Solution**: Ensure the 16-character password has NO spaces when used in auth.cmd
**Verification**: Test with `himalaya folder list` before using in cron jobs

### Issue: Message not sending via `himalaya message write`
**Root Cause**: `message write` opens $EDITOR and requires PTY mode for automation
**Solution**: Use `himalaya template send` with piped input or heredoc for reliable automation
**Working Pattern**: 
```bash
cat << 'EOF' | himalaya template send
From: sender@example.com
To: recipient@example.com
Subject: Test

Message body here
EOF
```

### Issue: Unicode variation selectors causing security blocks
**Root Cause**: Emoji variation selectors triggered Hermes security scan
**Solution**: Use standard emojis without variation selectors or avoid emojis in automated emails
**Alternative**: Use text-based markers like [NEWS] or 📰 instead of complex emojis

## Verification Commands

```bash
# Check cron job status
/opt/hermes/bin/hermes cron list

# Run newsletter job immediately (for testing)
/opt/hermes/bin/hermes cron run 05946f150d4d

# View last run output
# (Check gateway logs or use hermes cron log <ID> if available)
```

## Maintenance Tips
1. **App Password Rotation**: Generate new app password every 3-6 months
2. **Test Quarterly**: Run manual test to ensure configuration still works
3. **Monitor Logs**: Check `~/.hermes/logs/gateway.log` for email-related errors
4. **Backup Config**: Keep a copy of your working config.toml