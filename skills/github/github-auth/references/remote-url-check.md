# Verifying GitHub Remote URL

When encountering "Invalid username or token" errors or pushing to the wrong account, always verify your remote URL first.

## Why This Matters
The username in your remote URL determines which GitHub account you're authenticating as, not your local `git config user.name/user.email`.

## How to Check
```bash
git remote -v
```

Example output showing correct protohermes setup:
```
origin	https://protohermes:ghp_Yl...lVaC@github.com/protohermes/hermes-agent.git (fetch)
origin	https://protohermes:ghp_Yl...lVaC@github.com/protohermes/hermes-agent.git (push)
```

Example showing incorrect personal account setup:
```
origin	https://Gauravajit:github_pat_...@github.com/Gauravajit/HermesBot.git (fetch)
origin	https://Gauravajit:github_pat_...@github.com/Gauravajit/HermesBot.git (push)
```

## Fixing Wrong Remote URL
```bash
# To correct to protohermes account
git remote set-url origin https://protohermes:<YOUR_CLASSIC_PAT>@github.com/protohermes/hermes-agent.git

# Verify the change
git remote -v
```

## Best Practice
Always check `git remote -v` before troubleshooting authentication issues - it's quick and often reveals account mismatch problems immediately.