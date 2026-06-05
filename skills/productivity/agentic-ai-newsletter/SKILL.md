---
name: agentic-ai-newsletter
description: "Set up and manage automated agentic AI newsletters with Hermes"
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
tags: [newsletter, automation, cron, email, agentic-ai, research]
---

# Agentic AI Newsletter Automation

This skill provides a complete workflow for setting up automated daily newsletters covering the latest developments in agentic AI assistants, autonomous agents, and AI agent frameworks using Hermes Agent's cron job system and email capabilities.

## Overview

The workflow consists of:
1. **Research**: Use web_search to find latest agentic AI news from reputable sources
2. **Synthesis**: Extract key points and write a professional newsletter
3. **Delivery**: Send via email using configured SMTP/IMAP (Himalaya or Hermes gateway)
4. **Scheduling**: Automate with Hermes cron jobs for daily delivery

## Prerequisites

1. **Email Configuration**: Set up either:
   - **Himalaya CLI** with SMTP/IMAP credentials (see `himalaya` skill)
   - **Hermes Email Gateway** adapter configured in `config.yaml`
2. **Research Sources**: Access to web search for gathering latest news
3. **Time Commitment**: Initial setup ~15-20 minutes, then fully automated

## Setup Workflow

### 1. Configure Email Delivery

Choose one of these methods:

**Option A: Himalaya CLI (Recommended for flexibility)**
```bash
# Install Himalaya
curl -sSL https://raw.githubusercontent.com/pimalaya/himalaya/master/install.sh | PREFIX=$HOME/.local sh

# Configure for Gmail (requires App Password with 2FA enabled)
# See references/gmail-app-password.md for detailed steps

# Test configuration
$HOME/.local/bin/himalaya folder list
```

**Option B: Hermes Email Gateway**
```bash
# Configure in Hermes
hermes config set platforms.email.enabled true
hermes config set platforms.email.smtp.host smtp.gmail.com
hermes config set platforms.email.smtp.port 587
hermes config set platforms.email.smtp.encryption start-tls
hermes config set platforms.email.smtp.login youremail@gmail.com
# Set password via hermes auth add
```

### 2. Create the Newsletter Skill

Save this as `newsletter_research.skill` or use inline in cron job:

```
You are Hermes Agent. Research the latest news about agentic AI assistants, autonomous agents, and AI agent frameworks from the past 24 hours. Use web_search to find 5-8 relevant articles from reputable sources (tech news sites, company blogs, research publications). Extract key points and write a professional newsletter summarizing developments, trends, and insights suitable for a business audience. The newsletter should have:
1) A clear subject line with today's date
2) Brief intro
3) Top 3-5 stories with summaries
4) Framework watch section
5) Trend to watch
6) By the numbers if relevant
7) Closing note
Keep it concise but informative (~400-600 words).
Then send the newsletter via email to [RECIPIENTS] using the configured email account.
```

### 3. Create the Cron Job

```bash
# Replace [TIME] with your preferred schedule (e.g., "0 9 * * *" for 9am UTC)
# Replace [RECIPIENTS] with comma-separated email list
# Replace [SKILL_NAME] if you saved the skill above

hermes cron create "[TIME]" "You are Hermes Agent. Research the latest news about agentic AI assistants, autonomous agents, and AI agent frameworks from the past 24 hours. Use web_search to find 5-8 relevant articles from reputable sources (tech news sites, company blogs, research publications). Extract key points and write a professional newsletter summarizing developments, trends, and insights suitable for a business audience. The newsletter should have: 1) A clear subject line with today's date, 2) Brief intro, 3) Top 3-5 stories with summaries, 4) Framework watch section, 5) Trend to watch, 6) By the numbers if relevant, 7) Closing note. Keep it concise but informative (~400-600 words). Then send the newsletter via email to [RECIPIENTS] using the configured email account." --name "daily-agentic-newsletter" --deliver "origin"
```

## Example Configurations

### Daily 9am UTC Delivery to Multiple Recipients
```bash
hermes cron create "0 9 * * *" "You are Hermes Agent. Research the latest news about agentic AI assistants, autonomous agents, and AI agent frameworks from the past 24 hours. Use web_search to find 5-8 relevant articles from reputable sources (tech news sites, company blogs, research publications). Extract key points and write a professional newsletter summarizing developments, trends, and insights suitable for a business audience. The newsletter should have: 1) A clear subject line with today's date, 2) Brief intro, 3) Top 3-5 stories with summaries, 4) Framework watch section, 5) Trend to watch, 6) By the numbers if relevant, 7) Closing note. Keep it concise but informative (~400-600 words). Then send the newsletter via email to gaurav_ajit@hotmail.com,roseandrojude@gmail.com using the configured email account." --name "daily-agentic-newsletter" --deliver "origin"
```

### Weekday Only Delivery (Mon-Fri at 8:30am EST)
```bash
hermes cron create "30 8 * * 1-5" "..." --name "weekday-agentic-newsletter"
```

## Verification & Testing

### Test the Newsletter Generation
```bash
# Run immediately to test
hermes cron run [JOB-ID]

# Check logs if needed
hermes cron list  # See last run status and time
```

### Manual Trigger for Immediate Delivery
```bash
# Find job ID
hermes cron list

# Run specific job
hermes cron run [JOB-ID]
```

## Customization Options

### Adjust Research Focus
Modify the prompt to focus on:
- Specific frameworks (LangGraph, AutoGen/AG2, CrewAI)
- Industry verticals (healthcare, finance, manufacturing)
- Technical aspects (memory systems, tool use, planning algorithms)
- Regulatory/compliance developments

### Change Delivery Format
- **Email**: As configured above
- **Telegram**: Change `--deliver` to `telegram` or specific chat ID
- **Multiple platforms**: Use `deliver: origin,telegram,email` in advanced configs
- **File output**: Use `--no-agent=True` with a script that saves to file

### Adjust Frequency
- Twice daily: `"0 9 * * *"` and `"0 17 * * *"`
- Weekly: `"0 9 * * 1"` (Mondays at 9am)
- Every 6 hours: `"0 */6 * * *"`

## Troubleshooting

### Common Issues & Fixes

**Issue**: Newsletter not sending
- **Fix**: Check cron job status with `hermes cron list`
- **Fix**: Verify last run time and status
- **Fix**: Run manually with `hermes cron run [ID]` to see error output

**Issue**: Authentication failures (Email)
- **Fix For Himalaya**: Verify app password is correct (16 chars, no spaces)
- **Fix For Hermes**: Check `hermes auth list` and ensure credentials exist
- **Fix**: Test with `hermes config check` or manual send test

**Issue**: No research content
- **Fix**: Verify web_search tool is enabled (`hermes tools list`)
- **Fix**: Test search manually: `hermes chat -q "agentic AI news June 2026" --tool web`
- **Fix**: Check internet connectivity and DNS resolution

**Issue**: Formatting problems in email
- **Fix**: Use plain text format (as in examples) for reliable delivery
- **Fix**: Avoid complex HTML or markdown that may not render well
- **Fix**: Test with shorter content first to isolate issue

## Best Practices

### 1. Security
- Never hardcode passwords in scripts or cron prompts
- Use Hermes credential system or app passwords
- Regularly rotate app passwords (every 3-6 months)
- Use least-privilege credentials (email send-only if possible)

### 2. Reliability
- Test the cron job manually before relying on automation
- Monitor logs periodically for failures
- Consider setting up backup notification (e.g., Telegram alert if email fails)

### 3. Content Quality
- Vary sources to avoid bias (tech news, company blogs, research, industry reports)
- Include both breakthrough developments and practical adoption trends
- Keep tone professional but accessible to business audience
- Always include date and clear subject line for archiving

### 4. Maintenance
- Review and update sources quarterly
- Adjust frequency based on recipient feedback
- Archive old newsletters for reference/trend analysis
- Update skill and cron job as Hermes features evolve

## References

- **Himalaya Setup**: See `himalaya` skill for detailed email configuration
- **Himalaya + Gmail Setup Guide**: `references/gmail-himalaya-setup.md` (step-by-step from today's session)
- **Hermes Cron Docs**: `hermes cron --help` and `/opt/data/.hermes/skills/autonomous-ai-agents/hermes-agent/SKILL.md`
- **Agentic AI Sources**: 
  - TechCrunch, VentureBeat, The Information for breaking news
  - Company blogs: Microsoft, Google, NVIDIA, OpenAI, Anthropic
  - Research: arXiv.cs.AI, arXiv.cs.LL, Hugging Face papers
  - Industry: Gartner, Forrester, McKinsey reports on AI adoption
- **Framework Tracking**:
  - GitHub: Star history, issue activity, release frequency
  - PyPI/NPM: Download counts, version release dates
  - Community: Discord/Slack activity, Stack Overflow tags

## Extending This Skill

To adapt this pattern for other newsletter types:

1. **Change Research Focus**: Modify the web search query and extraction criteria
2. **Adjust Output Format**: Change newsletter structure or length requirements
3. **Different Delivery**: Modify email template or add additional channels
4. **Add Processing**: Include translation, summarization, or formatting steps
5. **Enhanced Research**: Add multiple search passes or source verification

The core pattern remains: Research → Synthesize → Deliver → Schedule.