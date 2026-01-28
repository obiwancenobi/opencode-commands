---
description: Create a conventional commit from all changes and push to remote with user approval
agent: build
---

You are executing the `/push-all` command to commit all changes and push. Follow this workflow:

## Pre-flight Checks

1. **Verify Git Configuration**
   - !`git config user.name && git config user.email`
   - If missing: "Git user not configured. Set git config user.name and user.email first."

2. **Check Branch and Remote**
   - !`git branch --show-current`
   - !`git remote get-url origin 2>/dev/null || echo "no-remote"`
   - If branch is main, master, develop, or release/*: warn user before proceeding
   - If no remote: "No remote configured. Add a remote with: git remote add origin <url>"
   
   **If on Protected Branch:**
   Present options:
   - label: "Push Anyway"
     description: "Push directly from protected branch"
   - label: "Create Branch"
     description: "Create a new branch with conventional naming and push there"
   - label: "Cancel"
     description: "Abort the push"
   
   If user chooses "Create Branch":
   - Suggest branch name: `feat/short-description` (infer from commit intent)
   - Confirm branch name with user
   - Run: !`git checkout -b <suggested-branch-name>`
   - Proceed with push workflow on new branch

## Workflow

### 1. Read All Changes
Run in parallel:
- !`git status`
- !`git diff --cached --stat`
- !`git diff --cached`
- !`git diff --stat`
- !`git diff`
- !`git log --oneline -5`

### 2. Analyze Changes
- All files changed (staged and unstaged)
- Full diff of all changes
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
    description: "Stage all, commit, and push to remote"
  - label: "Suggest Again"
    description: "Generate an alternative commit message"
  - label: "Cancel"
    description: "Abort the push"

### 5. Handle Response
- If response = "Accept": Run `git add .` then `git commit -m "<msg>"` then `git push origin $(git branch --show-current)`
- If upstream missing: `git push -u origin $(git branch --show-current)`
- If response = "Suggest Again": Generate new message, present again
- If response = "Cancel": Abort with "Push cancelled."

## Error Handling

- **Nothing to commit**: "Nothing to commit. No changes detected."
- **Staging failed**: "Failed to stage changes. Check file permissions."
- **Commit failed**: Show hook error and suggest fixes
- **Push rejected (remote ahead)**: Suggest pull first or force push (caution)
- **Push rejected (permissions)**: "Push rejected - permission denied"
