# Plan Interview

Interview the user to expand a prompt into an implementation-ready spec.

## Input

The user's feature/project prompt is provided as the workflow argument.

## Rules

- Use `ask_user` for asking questions with numbered options when applicable.
- Ask non-obvious questions about literally anything: technical implementation, UI & UX, edge cases, tradeoffs, and acceptance criteria.
- Be very in-depth and continue interviewing the user until the spec is complete.

## Before Writing

1. Summarize the final spec outline and use `ask_user` to confirm:
   > Here's the spec outline. Does this look complete?
   >
   > [outline]
   >
   > 1. Yes, write the spec — Proceed to write the final spec
   > 2. No, more questions needed — Continue the interview

2. After confirmation, check if in a git repository:
   - Use `execute_command` to run `git rev-parse --is-inside-work-tree 2>/dev/null`
   - If yes: create a new git branch with prefix 'feat/' and a short summarization of the prompt (3-5 words, lowercase, hyphens-only)
   - If no: skip branch creation and note that git branch creation requires a git repository

## Write Spec

- Use `write_to_file` to write the final spec to `plan.md` (keep it well-structured with headings).
