---
description: Create a conventional commit from staged changes with user approval
agent: build
---

You are a git commit expert. Follow these steps to create a conventional commit from staged changes.

1. Get context by running:
   - !`git diff --cached --stat`
   - !`git diff --cached`
   - !`git log --oneline -5`

2. Verify git user configuration:
   - !`git config user.name && git config user.email`
   - If missing: "Git user not configured. Set git config user.name and user.email first."

3. Check current branch:
   - !`git branch --show-current`
   - If on protected branch (main, master, develop, or release/*): present options
   
   **If on Protected Branch:**
   Present options:
   - label: "Commit Anyway"
     description: "Commit directly to protected branch"
   - label: "Create Branch"
     description: "Create a new branch with conventional naming and commit there"
   - label: "Cancel"
     description: "Abort the commit"
   
   If user chooses "Create Branch":
   - Suggest branch name: `feat/short-description` (infer from commit intent)
   - Confirm branch name with user
   - Run: !`git checkout -b <suggested-branch-name>`
   - Proceed with commit workflow on new branch

4. Generate conventional commit message:
   - Format: `<type>(<scope>): <subject>`
   - Types: feat, fix, docs, style, refactor, test, chore, perf
   - Subject: under 72 chars, imperative mood, no period

5. Present to user:
Run: !`question`
Parameters:
- question: "Commit message ready. What would you like to do?"
- header: "Commit Action"
- options:
  - label: "Accept"
    description: "Proceed with commit"
  - label: "Suggest Again"
    description: "Generate an alternative commit message"
  - label: "Cancel"
    description: "Abort the commit"

6. If accepted, run:
   - `git commit -m "<approved_message>"`

Error handling:
- If response = "Cancel": Abort with "Commit cancelled."
- If nothing staged: "Nothing to commit. Stage files first with `git add <file>`"
- If commit fails: Show error and suggest fixes
