# command-context-dump

A Claude Code slash command for saving and restoring working context.

## Installation

```bash
# Clone to your preferred location
git clone git@github.com:claude-commands/command-context-dump.git <clone-path>/command-context-dump

# Symlink (use full path to cloned repo)
ln -s <clone-path>/command-context-dump/context-dump.md ~/.claude/commands/context-dump.md
```

## Usage

```text
/context-dump                        # Save current context
/context-dump auth-refactor          # Save with name
/context-dump --restore              # List saved contexts
/context-dump --restore auth-refactor # Restore specific context
/context-dump --list                 # List all contexts
```

## What it does

1. Captures current git state (branch, changes, commits)
2. Saves investigation notes and hypotheses
3. Records progress and next steps
4. Stores for seamless later resumption
5. Restores context with actionable summary

## Context Includes

- Git branch and uncommitted changes
- Progress checklist (completed/in-progress/todo)
- Investigation notes and findings
- Current hypotheses and blockers
- Code snippets and references
- Next steps to continue

## Example Output

### Saving

```markdown
# Context: auth-refactor
**Branch**: feature/auth-refactor

## Problem
Refactoring authentication to use JWT

## Progress
- [x] Added JWT library
- [ ] Token refresh logic (70% done)

## Next Steps
1. Finish token refresh
2. Write tests
```

### Restoring

```markdown
# Restoring: auth-refactor

## Where You Left Off
Token refresh logic in middleware

## Next Steps
1. Finish implementation
2. Run tests
```

## Storage

Contexts saved to:

- `.claude/contexts/` - Claude Code directory
- `.context/` - Project local

## Requirements

- Git repository
- Claude Code with Opus 4.5 model access

## Updates

```bash
cd <clone-path>/command-context-dump && git pull
```
