---
argument-hint: "[name] [--restore]"
description: "Save and restore working context for later sessions"
model: claude-opus-4-5-20251101
allowed-tools: ["Bash", "Read", "Write", "Edit", "Glob", "Grep", "AskUserQuestion"]
---

**If `$ARGUMENTS` is empty or not provided:**

Save current working context to continue later.

**Usage:** `/context-dump [name] [--restore]`

**Examples:**
- `/context-dump` - Save current context
- `/context-dump auth-refactor` - Save with name
- `/context-dump --restore` - List and restore context
- `/context-dump --restore auth-refactor` - Restore specific context
- `/context-dump --list` - List all saved contexts

**Workflow:**
1. Capture current git state
2. Save open investigation notes
3. Record hypotheses and findings
4. Store for seamless resumption

Proceed with saving current context.

---

**If `$ARGUMENTS` is provided:**

Save or restore named context.

## Configuration

Parse arguments:
- **Name**: Context identifier
- **--restore**: Restore mode
- **--list**: List saved contexts
- **--delete**: Remove context

## Steps

### Saving Context

1. **Capture Git State**

   ```bash
   # Current branch
   git branch --show-current

   # Uncommitted changes
   git status --porcelain

   # Recent commits
   git log --oneline -10

   # Stash list
   git stash list

   # Current HEAD
   git rev-parse HEAD
   ```

2. **Identify Active Files**

   Determine which files are relevant:
   - Files with uncommitted changes
   - Recently modified files
   - Files mentioned in conversation

   ```bash
   # Changed files
   git diff --name-only

   # Recently modified
   ls -lt --time-style=+%s | head -20
   ```

3. **Capture Investigation Notes**

   Ask user for context:
   - What problem are you solving?
   - What have you tried?
   - What's your current hypothesis?
   - What's blocking you?
   - What should you try next?

4. **Save Context File**

   ```markdown
   # Context: auth-refactor
   **Saved**: 2025-01-15 10:30:00
   **Project**: my-app
   **Branch**: feature/auth-refactor

   ## Problem
   Refactoring authentication to use JWT instead of sessions.

   ## Current State

   ### Git Status
   - Branch: `feature/auth-refactor`
   - HEAD: `abc123`
   - Uncommitted changes in:
     - src/auth/login.ts
     - src/middleware/auth.ts

   ### Modified Files
   | File | Status | Notes |
   |------|--------|-------|
   | src/auth/login.ts | Modified | Halfway through JWT impl |
   | src/middleware/auth.ts | Modified | Token validation added |

   ## Progress

   ### Completed
   - [x] Added JWT library
   - [x] Created token generation function
   - [x] Updated login endpoint

   ### In Progress
   - [ ] Token refresh logic (70% done)
   - [ ] Update middleware to validate JWT

   ### Todo
   - [ ] Migrate existing sessions
   - [ ] Update all protected routes
   - [ ] Add token revocation

   ## Investigation Notes

   ### What I've Tried
   1. Tried using `jsonwebtoken` library - works but no TypeScript types
   2. Switched to `jose` library - better TS support

   ### Current Hypothesis
   The token refresh is failing because we're not handling the async properly in the middleware.

   ### Blockers
   - Need to decide on token storage (localStorage vs httpOnly cookie)
   - Waiting on security review for token expiry time

   ### Key Findings
   - The existing session middleware is in `src/middleware/session.ts`
   - All protected routes use `requireAuth` middleware
   - 47 files import the auth middleware

   ### Next Steps
   1. Finish token refresh implementation
   2. Write tests for token validation
   3. Create migration script for sessions

   ## Code Snippets

   ### Current Token Generation
   ```typescript
   // src/auth/jwt.ts
   const token = await new SignJWT({ userId })
     .setProtectedHeader({ alg: 'HS256' })
     .setExpirationTime('1h')
     .sign(secret);
   ```

   ### Middleware WIP
   ```typescript
   // src/middleware/auth.ts - Line 45
   // TODO: Handle refresh token
   // TODO: Add token revocation check
   ```

   ## Related Resources
   - PR #123 - Initial auth work
   - Issue #456 - JWT migration request
   - Slack thread: #dev 2025-01-14

   ## Environment
   - Node: 18.17.0
   - npm: 9.6.7
   - Key deps: jose@5.2.0

   ---
   *Saved by /context-dump on 2025-01-15*
   ```

5. **Store Context**

   Save to:
   ```
   .claude/contexts/
   └── auth-refactor-2025-01-15.md
   ```

   Or project-local:
   ```
   .context/
   └── auth-refactor.md
   ```

### Restoring Context

1. **List Available Contexts**

   ```bash
   # Find saved contexts
   ls -la .claude/contexts/ .context/ 2>/dev/null
   ```

   Display:
   ```markdown
   # Saved Contexts

   | Name | Date | Branch | Status |
   |------|------|--------|--------|
   | auth-refactor | 2025-01-15 | feature/auth | In progress |
   | bug-123 | 2025-01-10 | fix/bug-123 | Completed |
   | perf-audit | 2025-01-08 | main | On hold |
   ```

2. **Load Context**

   Read the context file and present summary:
   ```markdown
   # Restoring: auth-refactor

   ## Summary
   Refactoring authentication to use JWT instead of sessions.

   ## Current State
   - Branch: feature/auth-refactor
   - Modified: 2 files
   - Progress: 60%

   ## Where You Left Off
   Working on token refresh logic in middleware.

   ## Next Steps
   1. Finish token refresh implementation
   2. Write tests for token validation
   ```

3. **Restore Git State**

   Offer to:
   ```bash
   # Switch to branch
   git checkout feature/auth-refactor

   # Apply stash if exists
   git stash apply stash@{0}
   ```

4. **Provide Continuation Prompt**

   ```markdown
   ## Ready to Continue

   Based on your saved context, here's where to pick up:

   1. **Current file**: `src/middleware/auth.ts:45`
   2. **Task**: Implement token refresh logic
   3. **Blocker**: Decide on token storage approach

   Would you like me to:
   - [ ] Open the relevant files
   - [ ] Continue implementing token refresh
   - [ ] Review the current progress
   ```

## Output Structure

### Saving
```markdown
# Context Saved

**Name**: auth-refactor
**Location**: .claude/contexts/auth-refactor-2025-01-15.md

## Captured
- Git state (branch, changes, commits)
- Investigation notes
- Progress checklist
- Code snippets
- Next steps

## Resume Later
`/context-dump --restore auth-refactor`
```

### Restoring
```markdown
# Context Restored

**Name**: auth-refactor
**Saved**: 2025-01-15

## Summary
[Problem and progress]

## Where You Left Off
[Specific point to continue from]

## Recommended Actions
[Next steps to take]
```

## Context Storage

Default locations:
- `.claude/contexts/` - Claude Code specific
- `.context/` - Project local

Each context includes:
- Git state snapshot
- Investigation notes
- Progress tracking
- Code snippets
- Environment info

## Notes

- Contexts are markdown for easy reading
- Add to .gitignore if private
- Regularly clean old contexts
- Can share contexts with team
- Works across Claude Code sessions
