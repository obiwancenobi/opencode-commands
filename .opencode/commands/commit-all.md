---
description: Create a conventional commit from all changes with user approval
agent: build
---

You are executing the `/commit-all` command to create a conventional commit. Follow this workflow:

## Pre-flight Checks

1. **Verify Git Configuration**
   Run: !`git config user.name && git config user.email`
   If either is missing, abort with: "Git user not configured. Set git config user.name and user.email first."

2. **Check Current Branch**
   Run: !`git branch --show-current`
   If branch is `main`, `master`, `develop`, or matches `release/*`, warn user: "You are on a protected branch. Commit anyway? (Yes/No)"
   If user says No, abort.

## Workflow

### 1. Read All Changes
Run these in parallel:
- !`git status`
- !`git diff --cached --stat`
- !`git diff --cached`
- !`git diff --stat`
- !`git diff`
- !`git log --oneline -5`

### 2. Analyze Changes
Examine:
- Which files are staged vs unstaged
- Full diff of all changes
- Recent commit patterns for context

### 3. Generate Commit Message
Create a conventional commit message based on ALL changes:
- Format: `<type>(<scope>): <subject>`
- Types: feat, fix, docs, style, refactor, test, chore, perf
- Scope: primary feature, directory, or component
- Subject: under 72 characters, imperative mood, no period
- Focus on the most significant change

### 4. Present to User
Run: !`question`
Parameters:
- question: "Commit message ready. What would you like to do?"
- header: "Commit Action"
- options:
  - label: "Accept"
    description: "Stage all and commit with this message"
  - label: "Suggest Again"
    description: "Generate an alternative commit message"
  - label: "Cancel"
    description: "Abort the commit"

### 5. Handle Response
- If response = "Accept": Run `git add .` then `git commit -m "<approved_message>"`
- If response = "Suggest Again": Generate new message, present again (reuse question tool)
- If response = "Cancel": Abort with "Commit cancelled."

## Error Handling

- **Nothing to commit**: "Nothing to commit. No changes detected."
- **Commit hook failed**: Show hook error and suggestions to fix
- **Staging failed**: "Failed to stage changes. Check file permissions."
