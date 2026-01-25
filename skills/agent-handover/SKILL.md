---
name: agent-handover
description: Create handover artifact for context continuity between Claude sessions.
  Use at end of every session.
permalink: skills/agent-handover
---

# Agent handover

Enable context continuity between Claude sessions. When ending a session, create a handover artifact. When starting a new session, read the handover to understand current state.

## Files involved

- `$STATE_REPO/HANDOVER.md` — Current state artifact (overwritten each session)
- `$STATE_REPO/project-log.md` — Persistent history (appended)
- `skills/` — Procedural knowledge (stable reference)

**Note:** `STATE_REPO` is configured in `.env` and points to a separate private git repo (e.g., `~/repos/mik-workflow-state/`). This enables branch-based merge protection for parallel sessions.

## Starting a new session

### 1. Read the handover

```bash
# STATE_REPO is set in .env (e.g., ~/repos/mik-workflow-state)
cat "$STATE_REPO/HANDOVER.md"
```

This tells you:
- What was just completed
- What's next / in progress
- Key context and decisions
- Where to find things

### 2. Verify current state

```bash
git status
git log --oneline -5
```

### 3. Read project log if needed

```bash
cat "$STATE_REPO/project-log.md"
```

## Ending a session

### 1. Update the project log first

Use `/update-project-log` skill to append session summary.

### 2. Update HANDOVER in state repo

The state repo uses branch-based merge protection. Each session gets its own branch (created by `start-session`), and `close-session` handles the merge. This means:

- You edit `$STATE_REPO/HANDOVER.md` directly in your session
- Git handles merge conflicts when sessions close
- No need to manually fetch/merge from main

**What to include:**
- "Just completed" — your work
- "Current state" table — update relevant rows
- Add your reference sections if new (e.g., Email capture setup)
- Preserve existing reference sections unless outdated

### 3. Write the handover artifact

Update `$STATE_REPO/HANDOVER.md` with current state:

```markdown
---
date_updated: YYYY-MM-DD HH:MM
session_summary: "Brief one-liner of what was accomplished"
---

# Handover: mikrub.com project

## Just completed

- Bullet list of what this session accomplished
- Be specific: "Deployed site to Cloudflare Pages" not "worked on deployment"

## Current state

- Site: [status]
- Email: [status]
- Corpus: [status]

## Next steps

1. Immediate next task
2. Other pending items
3. Future considerations

## Key context

- Important decisions made this session
- Gotchas or workarounds to remember

## File locations

- Architecture: `corpus/sources/mik-knowledge-system-architecture.md`
- Project log: `$STATE_REPO/project-log.md`
- Skills: `.claude/skills/`
- This handover: `$STATE_REPO/HANDOVER.md`

## Commands to verify state

```bash
git status
hugo server -D  # local preview
```
```

### 4. Commit in state repo

```bash
cd "$STATE_REPO"
git add HANDOVER.md project-log.md
git commit -m "handover: [brief description]"
# Note: close-session will handle push and merge to main
```

## Anti-patterns

- Don't put sensitive info in handover (it's in the repo)
- Don't duplicate the entire project log (reference it)
- Don't leave handover from weeks ago (overwrite each session)
- Don't skip the handover "because it's quick" — context loss compounds
- Don't put bugs or feature backlog here — use `gh issue create` instead (see CLAUDE.md)
