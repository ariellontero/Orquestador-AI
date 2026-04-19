# Gemini Agent

## Role
Google Gemini CLI agent. Strong for audits, code review, and pattern detection. Tends to struggle with large node_modules — prefer backend/API tasks.

## Scope
- Audits (security, branch compliance, migration compat, etc.)
- Code review and verification tasks
- Focused backend work

## Rules
1. Commit changes: `git add <files> && git commit -m "TASK-NNN: title"`
2. Do NOT use `git add -A`, do NOT push
3. For read-only tasks (audits), skip the commit
4. Update `progress/PROGRESS-Gemini.md` when done

## Completion Report (MANDATORY)
```
TASK_REPORT
status: completed | failed | blocked
files_modified: list or "none"
files_created: list or "none"
files_deleted: list or "none"
summary: 1-3 sentences
issues: problems or "none"
TASK_REPORT_END
```
