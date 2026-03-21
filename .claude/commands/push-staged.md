---
description: Create a conventional commit from staged changes and push to remote with user approval
allowed-tools: Bash(git:*), Read, Grep, Glob
---

You are executing the `/push-staged` command to commit staged changes and push. Follow this workflow:

## Pre-flight Checks

1. **Verify Git Configuration**
   - Run: Bash(git config user.name)
     If empty: "Git user not configured. Set git config user.name first."
   - Run: Bash(git config user.email)
     If empty: "Git user not configured. Set git config user.email first."

2. **Check Branch and Remote**
   - Run: Bash(git branch --show-current)
   - Run: Bash(git remote get-url origin 2>/dev/null || echo "no-remote")
   - If branch is main, master, develop, or release/*: warn user before proceeding
   - If no remote: "No remote configured. Add a remote with: git remote add origin YOUR_URL"

   **If on Protected Branch:**
   Ask in chat:
   ```
   You are on a protected branch. What would you like to do?

   1. Push Anyway — Push directly from protected branch
   2. Create Branch — Create a new branch with conventional naming and push there
   3. Cancel — Abort the push

   Reply with the number.
   ```

   If user chooses "Create Branch":
   - Suggest branch name: feat/short-description (infer from commit intent)
   - Confirm branch name with user
   - Run: Bash(git checkout -b feat/your-branch-name)
   - Proceed with push workflow on new branch

## Workflow

### 1. Read Staged Changes
Run in parallel:
- Run: Bash(git diff --cached --stat)
- Run: Bash(git diff --cached)
- Run: Bash(git log --oneline -5)

### 2. Analyze Changes
- Which files are staged
- What the diff shows
- Remote URL context

### 3. Generate Commit Message
- Format: type(scope): subject
- Types: feat, fix, docs, style, refactor, test, chore, perf
- Subject: under 72 chars, imperative mood, no period

### 4. Present to User
Show the proposed commit message and ask in chat:
```
Commit message ready. What would you like to do?

1. Accept — Commit and push to remote
2. Suggest Again — Generate an alternative commit message
3. Cancel — Abort the push

Reply with the number.
```

### 5. Handle Response
- If response = "Accept": Run Bash(git commit -m "YOUR_COMMIT_MESSAGE" && git push origin $(git branch --show-current))
- If upstream missing: Bash(git push -u origin $(git branch --show-current))
- If response = "Suggest Again": Generate new message, present again
- If response = "Cancel": Abort with "Push cancelled."

## Error Handling

- **Nothing staged**: "Nothing to commit. Stage files first or use `/push-all`"
- **Commit failed**: Show error and suggest fixes
- **Push rejected (remote ahead)**: Suggest pull first or force push (caution)
- **Push rejected (permissions)**: "Push rejected - permission denied"
