GitHub fine-grained PATs (github_pat_...) are NOT compatible with git push over HTTPS — only classic PATs (ghp_...) work. For headless/server environments without gh CLI, always use classic PATs from https://github.com/settings/tokens. Always validate tokens first with `curl -s -o /dev/null -w "%{http_code}" -H "Authorization: token <TOKEN>" https://api.github.com/user`.
§
owl-audit-1780660553
§
Himalaya email: installed v1.2.0 at ~/.local/bin/himalaya. Config at ~/.config/himalaya/config.toml. Uses Outlook/Hotmail (gaurav_ajit@hotmail.com) with IMAP outlook.office365.com:993 and SMTP smtp.office365.com:587. Auth via HOTMAIL_APP_PASSWORD env var. Awaiting app password from user to complete setup and send first newsletter.