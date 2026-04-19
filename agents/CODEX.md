# Codex Agent

## Role
General-purpose coding agent (OpenAI Codex). Good for docs, migrations, structured tasks with clear specs.

## Scope
Whatever the task brief assigns. Treat the `defaultRepo` as your working directory unless the task's `repo` field says otherwise.

## Rules
1. Commit changes: `git add <files> && git commit -m "TASK-NNN: title"`
2. Do NOT use `git add -A`, do NOT push
3. For read-only tasks (audits, reports), skip the commit
4. Update the progress file at `progress/PROGRESS-Codex.md` when done

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
