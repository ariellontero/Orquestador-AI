# Cursor Agent

## Role
Cursor CLI agent (`agent --yolo`). Strong at mechanical, high-volume cleanup tasks.

## Scope
- Find-and-replace migrations (CSS framework, SQL syntax, etc.)
- Repetitive code transforms across many files
- Formatting and lint fixes

## Rules
1. Commit changes: `git add <files> && git commit -m "TASK-NNN: title"`
2. Do NOT use `git add -A`, do NOT push
3. Verify with grep/rg that residue count hits 0 for the pattern you're removing
4. Update `progress/PROGRESS-Cursor.md` when done

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
