---
description: Create a conventional commit from staged changes and push to remote with user approval
agent: build
---

You are executing the `/push-staged` command to commit staged changes and push. Follow this workflow:

## Pre-flight Checks

1. **Verify Git Configuration**
   - !`git config user.name && git config user.email`
   - If missing: "Git user not configured. Set git config user.name and user.email first."

2. **Check Branch and Remote**
   - !`git branch --show-current`
   - !`git remote get-url origin 2>/dev/null || echo "no-remote"`
   - If branch is main, master, develop, or release/*: warn user before proceeding
   - If no remote: "No remote configured. Add a remote with: git remote add origin <url>"

## Workflow

### 1. Read Staged Changes
Run in parallel:
- !`git diff --cached --stat`
- !`git diff --cached`
- !`git log --oneline -5`

### 2. Analyze Changes
- Which files are staged
- What the diff shows
- Remote URL context

### 3. Generate Commit Message
- Format: `<type>(<scope>): <subject>`
- Types: feat, fix, docs, style, refactor, test, chore, perf
- Subject: under 72 chars, imperative mood, no period

### 4. Present to User
Run: !`question`
Parameters:
- question: "Commit message ready. What would you like to do?"
- header: "Push Action"
- options:
  - label: "Accept"
    description: "Commit and push to remote"
  - label: "Suggest Again"
    description: "Generate an alternative commit message"
  - label: "Cancel"
    description: "Abort the push"

### 5. Handle Response
- If response = "Accept": Run `git commit -m "<msg>"` then `git push origin $(git branch --show-current)`
- If upstream missing: `git push -u origin $(git branch --show-current)`
- If response = "Suggest Again": Generate new message, present again
- If response = "Cancel": Abort with "Push cancelled."

## Error Handling

- **Nothing staged**: "Nothing to commit. Stage files first or use `/push-all`"
- **Commit failed**: Show error and suggest fixes
- **Push rejected (remote ahead)**: Suggest pull first or force push (caution)
- **Push rejected (permissions)**: "Push rejected - permission denied"
