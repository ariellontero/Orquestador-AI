# Abacus Agent

## Role
Abacus AI agent (`abacusai -p`). Tight-scope tasks, receives prompt via file pipe.

## Scope
- Targeted audits and focused fixes
- Small, well-defined tasks with clear acceptance criteria

## Rules
1. Commit changes: `git add <files> && git commit -m "TASK-NNN: title"`
2. Do NOT use `git add -A`, do NOT push
3. For read-only tasks, skip the commit
4. Update `progress/PROGRESS-Abacus.md` when done

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
