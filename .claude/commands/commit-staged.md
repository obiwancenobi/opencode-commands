---
description: Create a conventional commit from staged changes with user approval
allowed-tools: Bash(git:*), Read, Grep, Glob
---

You are a git commit expert. Follow these steps to create a conventional commit from staged changes.

1. Get context by running:
   - Run: Bash(git diff --cached --stat)
   - Run: Bash(git diff --cached)
   - Run: Bash(git log --oneline -5)

2. Verify git user configuration:
   - Run: Bash(git config user.name)
     If empty: "Git user not configured. Set git config user.name first."
   - Run: Bash(git config user.email)
     If empty: "Git user not configured. Set git config user.email first."

3. Check current branch:
   - Run: Bash(git branch --show-current)
   - If on protected branch (main, master, develop, or release/*): present options

   **If on Protected Branch:**
   Ask in chat:
   ```
   You are on a protected branch. What would you like to do?

   1. Commit Anyway — Commit directly to protected branch
   2. Create Branch — Create a new branch with conventional naming and commit there
   3. Cancel — Abort the commit

   Reply with the number.
   ```

   If user chooses "Create Branch":
   - Suggest branch name: feat/short-description (infer from commit intent)
   - Confirm branch name with user
   - Run: Bash(git checkout -b feat/your-branch-name)
   - Proceed with commit workflow on new branch

4. Generate conventional commit message:
   - Format: type(scope): subject
   - Types: feat, fix, docs, style, refactor, test, chore, perf
   - Subject: under 72 chars, imperative mood, no period

5. Present to user:
   Show the proposed commit message and ask in chat:
   ```
   Commit message ready. What would you like to do?

   1. Accept — Proceed with commit
   2. Suggest Again — Generate an alternative commit message
   3. Cancel — Abort the commit

   Reply with the number.
   ```

6. If accepted, run:
   - Run: Bash(git commit -m "YOUR_COMMIT_MESSAGE")

Error handling:
- If response = "Cancel": Abort with "Commit cancelled."
- If nothing staged: "Nothing to commit. Stage files first with `git add <file>`"
- If commit fails: Show error and suggest fixes
