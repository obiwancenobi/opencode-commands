# Push All

Create a conventional commit from all changes and push to remote with user approval.

## Pre-flight Checks

1. **Verify Git Configuration**
   - Use `execute_command` to run `git config user.name`
     If empty: "Git user not configured. Set git config user.name first."
   - Use `execute_command` to run `git config user.email`
     If empty: "Git user not configured. Set git config user.email first."

2. **Check Branch and Remote**
   - Use `execute_command` to run `git branch --show-current`
   - Use `execute_command` to run `git remote get-url origin 2>/dev/null || echo "no-remote"`
   - If branch is main, master, develop, or release/*: warn user before proceeding
   - If no remote: "No remote configured. Add a remote with: git remote add origin YOUR_URL"

   **If on Protected Branch:**
   Use `ask_user`:
   > You are on a protected branch. What would you like to do?
   >
   > 1. Push Anyway — Push directly from protected branch
   > 2. Create Branch — Create a new branch with conventional naming and push there
   > 3. Cancel — Abort the push

   If user chooses "Create Branch":
   - Suggest branch name: feat/short-description (infer from commit intent)
   - Confirm branch name with user
   - Use `execute_command` to run `git checkout -b feat/your-branch-name`
   - Proceed with push workflow on new branch

## Workflow

### 1. Read All Changes
Use `execute_command` to run:
- `git status`
- `git diff --cached --stat`
- `git diff --cached`
- `git diff --stat`
- `git diff`
- `git log --oneline -5`

### 2. Analyze Changes
- All files changed (staged and unstaged)
- Full diff of all changes
- Remote URL context

### 3. Generate Commit Message
- Format: type(scope): subject
- Types: feat, fix, docs, style, refactor, test, chore, perf
- Subject: under 72 chars, imperative mood, no period

### 4. Present to User
Use `ask_user`:
> Commit message ready. What would you like to do?
>
> 1. Accept — Stage all, commit, and push to remote
> 2. Suggest Again — Generate an alternative commit message
> 3. Cancel — Abort the push

### 5. Handle Response
- If response = "Accept": Use `execute_command` to run `git add . && git commit -m "YOUR_COMMIT_MESSAGE" && git push origin $(git branch --show-current)`
- If upstream missing: Use `execute_command` to run `git push -u origin $(git branch --show-current)`
- If response = "Suggest Again": Generate new message, present again
- If response = "Cancel": Abort with "Push cancelled."

## Error Handling

- **Nothing to commit**: "Nothing to commit. No changes detected."
- **Staging failed**: "Failed to stage changes. Check file permissions."
- **Commit failed**: Show hook error and suggest fixes
- **Push rejected (remote ahead)**: Suggest pull first or force push (caution)
- **Push rejected (permissions)**: "Push rejected - permission denied"
