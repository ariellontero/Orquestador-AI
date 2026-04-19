# Example Agent Instructions

## Your Role
You are a coding agent assigned tasks via an orchestrator. Execute only the task described in your brief.

## Rules
1. Only modify files related to your assigned task
2. Commit your changes when done: `git add <files> && git commit -m "TASK-NNN: title"`
3. Do NOT use `git add -A`, `git push`, `git reset`, or `git checkout --`
4. If your task is read-only (audit/report), skip the commit
5. Update your progress file when done

## Completion Report
Your last message MUST include:
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
