---
description: Create a conventional commit from all changes with user approval
agent: build
---

You are executing the `/commit-all` command to create a conventional commit. Follow this workflow:

## Pre-flight Checks

1. **Verify Git Configuration**
   - Run: git config user.name
     If empty: "Git user not configured. Set git config user.name first."
   - Run: git config user.email
     If empty: "Git user not configured. Set git config user.email first."

2. **Check Current Branch**
   - Run: git branch --show-current
   If branch is `main`, `master`, `develop`, or matches `release/*`, warn user: "You are on a protected branch. What would you like to do?"
   Present options:
   - label: "Commit Anyway"
     description: "Commit directly to protected branch"
   - label: "Create Branch"
     description: "Create a new branch with conventional naming and commit there"
   - label: "Cancel"
     description: "Abort the commit"

   If user chooses "Create Branch":
   - Suggest branch name: feat/short-description (infer from commit intent)
   - Confirm branch name with user
   - Run: git checkout -b feat/your-branch-name
   - Proceed with commit workflow on new branch

## Workflow

### 1. Read All Changes
Run in parallel:
- Run: git status
- Run: git diff --cached --stat
- Run: git diff --cached
- Run: git diff --stat
- Run: git diff
- Run: git log --oneline -5

### 2. Analyze Changes
Examine:
- Which files are staged vs unstaged
- Full diff of all changes
- Recent commit patterns for context

### 3. Generate Commit Message
- Format: type(scope): subject
- Types: feat, fix, docs, style, refactor, test, chore, perf
- Scope: primary feature, directory, or component
- Subject: under 72 characters, imperative mood, no period
- Focus on the most significant change

### 4. Present to User
Use question tool to ask user:
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
- If response = "Accept": Run git add . && git commit -m "YOUR_COMMIT_MESSAGE"
- If response = "Suggest Again": Generate new message, present again (reuse question tool)
- If response = "Cancel": Abort with "Commit cancelled."

## Error Handling

- **Nothing to commit**: "Nothing to commit. No changes detected."
- **Commit hook failed**: Show hook error and suggestions to fix
- **Staging failed**: "Failed to stage changes. Check file permissions."
