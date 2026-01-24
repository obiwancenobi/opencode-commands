# OpenCode Commands

A repository containing command specifications for git operations and planning workflows. These specifications define slash commands that follow structured workflows for an AI coding assistant.

## Overview

This repository contains Markdown files with YAML frontmatter that define slash commands for an AI coding assistant. Each command specification provides a structured workflow for common development tasks, ensuring consistent and safe git operations.

### What This Repository Does

The repository provides pre-defined command specifications that:
- Guide git operations with conventional commit standards
- Ensure safe practices through pre-flight checks
- Provide user approval workflows before destructive operations
- Standardize commit message formatting across projects
- Facilitate feature planning through structured interviews

### Why It Exists

This repository exists to:
1. **Standardize Git Workflows**: Ensure consistent commit messages and git operations across all projects
2. **Prevent Mistakes**: Implement pre-flight checks and user approval steps to prevent accidental commits
3. **Enforce Best Practices**: Use conventional commit formats and protect sensitive branches
4. **Streamline Planning**: Provide structured workflows for feature specification and planning

### Key Concepts and Terminology

| Term | Definition |
|------|------------|
| **Command Specification** | A Markdown file with YAML frontmatter that defines a slash command's behavior |
| **Conventional Commit** | A commit message format following `<type>(<scope>): <subject>` pattern |
| **Pre-flight Check** | Validation steps run before executing a command (e.g., git config verification) |
| **Agent** | The AI assistant type that should execute the command (e.g., `build`, `general`) |
| **Frontmatter** | YAML metadata at the top of command specification files |

## Command Specifications

### Available Commands

| Command | Description | Agent |
|---------|-------------|-------|
| `/commit-all` | Create a conventional commit from all changes with user approval | build |
| `/commit-staged` | Create a conventional commit from staged changes with user approval | build |
| `/push-all` | Commit all changes and push to remote with user approval | build |
| `/push-staged` | Commit staged changes and push to remote with user approval | build |
| `/add-documentation` | Add comprehensive documentation for code/features | build |
| `/plan-interview` | Interview to expand a spec from prompt | build |

### Command File Structure

Each command specification follows this structure:

```yaml
---
description: Brief description of the command
agent: build
---

## Pre-flight Checks
[Validation steps]

## Workflow
[Step-by-step instructions]

## Error Handling
[Common errors and responses]
```

### Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `description` | Yes | Brief description shown in command help |
| `agent` | Yes | AI assistant type that should execute (e.g., `build`, `general`) |

## Conventional Commits

All git commands in this repository use conventional commit messages.

### Commit Message Format

```
<type>(<scope>): <subject>
```

### Commit Types

| Type | Description |
|------|-------------|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation only changes |
| `style` | Changes that do not affect the meaning of the code (white-space, formatting, etc) |
| `refactor` | A code change that neither fixes a bug nor adds a feature |
| `test` | Adding missing tests or correcting existing tests |
| `chore` | Changes to the build process or auxiliary tools |
| `perf` | A code change that improves performance |

### Examples

```
feat(auth): add user authentication module
fix(database): resolve connection timeout issue
docs(readme): update installation instructions
refactor(utils): simplify date formatting logic
```

### Subject Line Rules

- Maximum 72 characters
- Use imperative mood (e.g., "add" not "added")
- No period at the end
- Focus on what the change does

## API Documentation

### Tool Execution Syntax

Commands use special syntax for tool execution:

| Syntax | Purpose |
|--------|---------|
| ``!`command` `` | Execute a bash command |
| `!`question` ` | Prompt the user for input |

### Question Tool Parameters

When user interaction is needed, use the question tool with:

```yaml
question: "Clear question about what to do next"
header: "Short context header"
options:
  - label: "Action 1"
    description: "What this action does"
  - label: "Cancel"
    description: "Abort the operation"
```

### Git Commands Reference

The following git commands are used across commands:

| Command | Purpose |
|---------|---------|
| `git config user.name` | Get configured git username |
| `git config user.email` | Get configured git email |
| `git branch --show-current` | Get current branch name |
| `git remote get-url origin` | Get remote repository URL |
| `git status` | Show working tree status |
| `git diff --cached` | Show staged changes |
| `git diff` | Show unstaged changes |
| `git log --oneline -5` | Show recent commits |
| `git add .` | Stage all changes |
| `git commit -m "<msg>"` | Create a commit |
| `git push origin <branch>` | Push to remote |

## Implementation Details

### Architecture Overview

The repository follows a simple structure:

```
opencode-commands/
├── .opencode/
│   ├── commands/
│   │   ├── commit-all.md
│   │   ├── commit-staged.md
│   │   ├── push-all.md
│   │   ├── push-staged.md
│   │   ├── add-documentation.md
│   │   └── plan-interview.md
│   └── package.json
├── AGENTS.md
└── README.md
```

### Design Decisions

1. **Markdown with Frontmatter**: Chosen for readability and ease of parsing
2. **Agent Type Specification**: Ensures commands are executed by appropriate AI assistants
3. **User Approval Workflows**: All destructive operations require explicit user confirmation
4. **Parallel Execution**: Independent git commands run in parallel for efficiency
5. **Error Handling First**: Pre-flight checks run before any action to fail fast

### Dependencies

This repository has no runtime dependencies. It is a documentation-only repository containing command specifications.

## Usage Examples

### Creating a Commit with `/commit-all`

1. Make changes to your files
2. Execute `/commit-all` command
3. AI assistant runs pre-flight checks
4. Review generated commit message
5. Accept or suggest alternatives
6. Commit is created with approval

### Pushing Changes with `/push-staged`

1. Stage specific files: `git add <file>`
2. Execute `/push-staged` command
3. AI assistant verifies staged changes
4. Generate conventional commit message
5. Get user approval
6. Commit and push to remote

### Planning a Feature with `/plan-interview`

1. Execute `/plan-interview <feature-description>`
2. AI assistant asks clarifying questions
3. Review and confirm final spec outline
4. Git branch is created (if in git repo)
5. Spec is written to `plan.md`

## Best Practices

### Writing New Commands

1. **Always include pre-flight checks**: Verify git config, branch status, and remote availability
2. **Use user approval workflows**: Never perform destructive operations without confirmation
3. **Follow conventional commits**: Use standardized commit message format
4. **Handle errors gracefully**: Provide clear error messages and suggestions
5. **Use parallel execution**: Run independent commands simultaneously

### Branch Naming

- Feature branches: `feat/<short-description>`
- Use lowercase, hyphens only
- Keep descriptions to 3-5 words

### Protected Branches

The following branches are protected and trigger warnings:
- `main`
- `master`
- `develop`
- `release/*`

## Common Pitfalls to Avoid

### Git Configuration

**Don't**: Skip git config verification
**Do**: Always verify `user.name` and `user.email` before operations

### Commit Message Format

**Don't**: Use vague messages like "fixed stuff" or "update"
**Do**: Follow conventional commit format with clear subject lines

### Branch Protection

**Don't**: Commit directly to protected branches
**Do**: Warn users and require explicit confirmation

### User Interaction

**Don't**: Skip user approval for destructive operations
**Do**: Always present changes for review before executing

### Error Handling

**Don't**: Ignore errors or provide unhelpful messages
**Do**: Provide clear error messages with suggestions for resolution

## Validation

### Markdown Validation

```bash
# Check frontmatter structure
grep -n "^---" *.md

# Validate YAML syntax
yq eval 'select(document_index == 0)' *.md

# Check required fields
grep -l "description:" *.md
grep -l "agent:" *.md
```

### Git Validation

```bash
# Verify git configuration
git config user.name && git config user.email

# Check current branch
git branch --show-current

# Verify remote exists
git remote get-url origin
```

## Contributing

To add a new command:

1. Create a new Markdown file in `.opencode/commands/`
2. Use the naming pattern `<action>-<target>.md`
3. Include YAML frontmatter with `description` and `agent`
4. Follow the established workflow format
5. Add comprehensive error handling
6. Test the workflow manually

## License

This repository is used for AI-assisted software engineering workflows.
