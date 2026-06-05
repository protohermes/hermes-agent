---
name: structured-reporting
description: "Structured multi-section audit/review/status reports with evidence: bold headers, bullets (never tables), ✅/⚠️/❌ markers, and a final summary block."
version: 1.0.0
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [reporting, audit, review, status, structured-output, formatting]
    related_skills: [dogfood, requesting-code-review, codebase-inspection]
---

# Structured Reporting

## When to Use

Any task that produces a multi-check, multi-section, or audit-style report:
- Capability smoke-tests / production-readiness audits
- Code review summaries
- System health checks
- Security audits
- Performance reviews
- Any "run N checks and report" workflow

**Do NOT use for:** casual Q&A, single-question answers, creative writing, or when the user explicitly asks for a different format.

## Output Format Rules

**Always follow these rules — they reflect the user's stated preferences:**

1. **No tables.** Use bold headers + bullet lists. Telegram has no table syntax.
2. **Bold section headers.** Use `**1. CAPABILITY NAME**` numbering for scanability.
3. **Per-item status markers:** ✅ working / ⚠️ partial / ❌ failed — one line after each item with a brief note.
4. **Show evidence.** Every claim must include a real snippet, output line, ID, or metric from actual tool execution. Never describe what you *can* do — show what you *did*.
5. **Concise but complete.** No fluff paragraphs. Bullets over prose. Every section earns its place.
6. **Final summary block** at the end with:
   - One-line ✅/⚠️/❌ per capability
   - Headline facts (versions, counts, key numbers)
   - "FAILURES & FIXES" section (error → suggested fix)
   - One-line overall verdict

## Report Template

```
**1. CATEGORY NAME**
- Finding or evidence
- ✅/⚠️/❌ one-line verdict

**2. NEXT CATEGORY**
...

---

**✅/⚠️/❌ Per-Capability Summary:**

**1. NAME** — ✅/⚠️/❌ one-line note
**2. NAME** — ✅/⚠️/❌ one-line note
...

**Headline Facts:**
- Key metric: value
- Key metric: value

**FAILURES & FIXES:**

**N. Short description of failure**
- Error: exact error message or symptom
- Fix: specific command, config change, or step to resolve

**OVERALL VERDICT:**
⚠️/✅/❌ <one line> — <single biggest gap or strength>.
```

## Evidence Capture

When running checks, always capture:
- **Command output** — run the command, include the actual output (not paraphrased)
- **IDs and timestamps** — job IDs, PIDs, session IDs, file paths all count as evidence
- **File contents** — read-back after write to confirm
- **Structured results** — tool return values, exit codes, counts

## Pitfalls

- **Don't fabricate evidence.** If a check can't run (blocked, no permissions, no cost budget), mark ⚠️ and explain why. Never invent output.
- **Don't describe capabilities Instead of testing them.** The whole point is execution evidence.
- **Don't use tables.** Even if it looks cleaner in markdown. Telegram renders them as broken text.
- **Don't skip the FAILURES section** even if everything passed. Say "No failures detected" explicitly.
- **Don't ask clarifying questions mid-audit.** The user expects you to make sensible assumptions and proceed.

## Integration with Other Skills

- **dogfood** — web app QA. Use this formatting skill *within* dogfood's Phase 5 (Report) for the final output.
- **requesting-code-review** — code review. The verdict format (pass/fail per concern) follows this same pattern.
- **codebase-inspection** — codebase metrics. LOC counts and language breakdowns slot into the evidence format here.
