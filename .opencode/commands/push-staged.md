---
description: Create a conventional commit from staged changes and push to remote with user approval
agent: build
---

You are executing the `/push-staged` command to commit staged changes and push. Follow this workflow:

## Pre-flight Checks

1. **Verify Git Configuration**
   - Run: git config user.name
     If empty: "Git user not configured. Set git config user.name first."
   - Run: git config user.email
     If empty: "Git user not configured. Set git config user.email first."

2. **Check Branch and Remote**
   - Run: git branch --show-current
   - Run: git remote get-url origin 2>/dev/null || echo "no-remote"
   - If branch is main, master, develop, or release/*: warn user before proceeding
   - If no remote: "No remote configured. Add a remote with: git remote add origin YOUR_URL"

   **If on Protected Branch:**
   Present options:
   - label: "Push Anyway"
     description: "Push directly from protected branch"
   - label: "Create Branch"
     description: "Create a new branch with conventional naming and push there"
   - label: "Cancel"
     description: "Abort the push"

   If user chooses "Create Branch":
   - Suggest branch name: feat/short-description (infer from commit intent)
   - Confirm branch name with user
   - Run: git checkout -b feat/your-branch-name
   - Proceed with push workflow on new branch

## Workflow

### 1. Read Staged Changes
Run in parallel:
- Run: git diff --cached --stat
- Run: git diff --cached
- Run: git log --oneline -5

### 2. Analyze Changes
- Which files are staged
- What the diff shows
- Remote URL context

### 3. Generate Commit Message
- Format: type(scope): subject
- Types: feat, fix, docs, style, refactor, test, chore, perf
- Subject: under 72 chars, imperative mood, no period

### 4. Present to User
Use question tool to ask user:
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
- If response = "Accept": Run git commit -m "YOUR_COMMIT_MESSAGE" && git push origin $(git branch --show-current)
- If upstream missing: git push -u origin $(git branch --show-current)
- If response = "Suggest Again": Generate new message, present again
- If response = "Cancel": Abort with "Push cancelled."

## Error Handling

- **Nothing staged**: "Nothing to commit. Stage files first or use `/push-all`"
- **Commit failed**: Show error and suggest fixes
- **Push rejected (remote ahead)**: Suggest pull first or force push (caution)
- **Push rejected (permissions)**: "Push rejected - permission denied"
