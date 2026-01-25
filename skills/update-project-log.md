---
name: update-project-log
description: Append session decisions and progress to the project log. Use at end
  of work sessions or after significant decisions.
permalink: skills/update-project-log
---

# Update project log

Maintain a running log of project decisions, progress, and artifacts. The log serves as context for future Claude sessions and as audit trail.

## Critical rule: append-only

**Previous log entries must NEVER be modified.** The log is an immutable record.

- Always append new sections at the end of the file
- If a previous entry has errors, add a correction in a new entry, don't edit the old one

## Environment detection

This skill works in both Claude Code and claude.ai. First, determine your environment:

**Claude Code:** Can access filesystem and run `git` commands
**claude.ai:** Works from conversation context window only

```bash
# Check environment
if [ -d ~/.claude/projects ]; then
  echo "Claude Code"
else
  echo "claude.ai"
fi
```

---

## Claude Code procedure

### 1. Gather context from git

```bash
# See what changed this session
git status
git diff
git diff --cached

# Recent commits for context
git log --oneline -10
```

### 2. Read current log

```bash
# STATE_REPO is set in .env (e.g., ~/repos/mik-workflow-state)
cat "$STATE_REPO/project-log.md"
```

### 3. Gather context from conversation

Review the conversation history in your context window:
- What tasks were completed?
- What decisions were made and why?
- What problems were encountered?
- What insights emerged?

### 4. Write log entry

Combine git changes + conversation context into a comprehensive entry.

```bash
cat >> "$STATE_REPO/project-log.md" << 'EOF'

## YYYY-MM-DD: Brief descriptive title

### Summary
...
EOF
```

### 5. Commit in state repo

```bash
cd "$STATE_REPO"
git add project-log.md
git commit -m "log: [brief description of session]"
# Note: close-session will handle push and merge to main
```

---

## claude.ai procedure

### 1. Gather context from conversation

Since you can't access git directly, gather ALL context from:
- The conversation history in your context window
- Any files/artifacts created during the session
- Decisions discussed with the user
- Problems solved and insights gained

### 2. Read current log

Ask user to provide current log contents, or read from any uploaded files.

### 3. Write log entry

Create the log entry based on conversation context:

```markdown
## YYYY-MM-DD: Brief descriptive title

### Summary
One paragraph: what was accomplished, why it matters.

### Decisions
- **Decision:** [What was decided]
  - *Context:* Why this came up
  - *Rationale:* Why this choice
  - *Alternatives considered:* What else was evaluated (if any)

### Completed
- [x] Items done this session

### Open questions
- [ ] New questions that emerged
- [ ] Items deferred to future

### Artifacts produced
- List of files created/modified with brief description

### Notes
Freeform observations, insights, gotchas encountered.
```

### 4. Present for manual commit

Provide the entry for the user to append to `$STATE_REPO/project-log.md` and commit.

---

## Entry structure

```markdown
## YYYY-MM-DD: Brief descriptive title

### Summary
One paragraph: what was accomplished, why it matters.

### Decisions
- **Decision:** [What was decided]
  - *Context:* Why this came up
  - *Rationale:* Why this choice
  - *Alternatives considered:* What else was evaluated (if any)

### Completed
- [x] Items done this session

### Open questions
- [ ] New questions that emerged
- [ ] Items deferred to future

### Artifacts produced
- List of files created/modified with brief description

### Notes
Freeform observations, insights, gotchas encountered.
```

## Anti-patterns

- Don't duplicate content already in architecture doc (reference it instead)
- Don't log routine commits, only significant work sessions
- Don't include sensitive info (API keys, passwords)