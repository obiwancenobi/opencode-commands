# Commit All

Create a conventional commit from all changes with user approval.

## Pre-flight Checks

1. **Verify Git Configuration**
   - Use `execute_command` to run `git config user.name`
     If empty: "Git user not configured. Set git config user.name first."
   - Use `execute_command` to run `git config user.email`
     If empty: "Git user not configured. Set git config user.email first."

2. **Check Current Branch**
   - Use `execute_command` to run `git branch --show-current`
   If branch is `main`, `master`, `develop`, or matches `release/*`, warn user: "You are on a protected branch. What would you like to do?"
   Use `ask_user`:
   > You are on a protected branch. What would you like to do?
   >
   > 1. Commit Anyway — Commit directly to protected branch
   > 2. Create Branch — Create a new branch with conventional naming and commit there
   > 3. Cancel — Abort the commit

   If user chooses "Create Branch":
   - Suggest branch name: feat/short-description (infer from commit intent)
   - Confirm branch name with user
   - Use `execute_command` to run `git checkout -b feat/your-branch-name`
   - Proceed with commit workflow on new branch

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
Use `ask_user`:
> Commit message ready. What would you like to do?
>
> 1. Accept — Stage all and commit with this message
> 2. Suggest Again — Generate an alternative commit message
> 3. Cancel — Abort the commit

### 5. Handle Response
- If response = "Accept": Use `execute_command` to run `git add . && git commit -m "YOUR_COMMIT_MESSAGE"`
- If response = "Suggest Again": Generate new message, present again
- If response = "Cancel": Abort with "Commit cancelled."

## Error Handling

- **Nothing to commit**: "Nothing to commit. No changes detected."
- **Commit hook failed**: Show hook error and suggestions to fix
- **Staging failed**: "Failed to stage changes. Check file permissions."
