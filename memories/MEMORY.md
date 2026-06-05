GitHub fine-grained PATs (format: github_pat_...) are NOT compatible with standard git push over HTTPS. Only classic PATs work with git credential helper. For headless/server environments without gh CLI, always generate a classic PAT at https://github.com/settings/tokens.
§
owl-audit-1780660553