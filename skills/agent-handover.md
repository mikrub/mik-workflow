---
name: agent-handover
description: Create handover artifact for context continuity between Claude sessions.
  Use at end of every session.
permalink: skills/agent-handover
---

# Agent handover

Enable context continuity between Claude sessions. When ending a session, create a handover artifact. When starting a new session, read the handover to understand current state.

## Files involved

- `corpus/inbox/HANDOVER.md` — Current state artifact (overwritten each session)
- `corpus/sources/mikrub-project-log.md` — Persistent history (appended)
- `.claude/skills/` — Procedural knowledge (stable reference)

## Starting a new session

### 1. Read the handover

```bash
cat corpus/inbox/HANDOVER.md
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
cat corpus/sources/mikrub-project-log.md
```

## Ending a session

### 1. Update the project log first

Use `/update-project-log` skill to append session summary.

### 2. Fetch latest HANDOVER from main (CRITICAL for parallel sessions)

With ccswitch, multiple sessions may run in parallel. Another session may have updated HANDOVER.md and merged to main. **Always fetch before writing:**

```bash
# Check for upstream changes
git fetch origin main
git diff HEAD origin/main -- corpus/inbox/HANDOVER.md
```

If there are changes:
```bash
# Get the latest version from main
git checkout origin/main -- corpus/inbox/HANDOVER.md
```

Then **merge** your session's updates into the fetched version — don't blindly overwrite.

**What to preserve from other sessions:**
- Their "Just completed" section (move to a "Recently completed" or just note it)
- Any "Next session" plans they added
- Reference sections (MinerU setup, etc.) unless outdated
- Reminders and deadlines

**What to update with your session:**
- "Just completed" — your work
- "Current state" table — update relevant rows
- Add your reference sections if new (e.g., Email capture setup)

### 3. Write the handover artifact

Update `corpus/inbox/HANDOVER.md` with current state:

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
- Project log: `corpus/sources/mikrub-project-log.md`
- Skills: `.claude/skills/`
- This handover: `corpus/inbox/HANDOVER.md`

## Commands to verify state

```bash
git status
hugo server -D  # local preview
```
```

### 3. Commit both

```bash
git add corpus/inbox/HANDOVER.md corpus/sources/mikrub-project-log.md
git commit -m "handover: [brief description]"
git push
```

## Anti-patterns

- Don't put sensitive info in handover (it's in the repo)
- Don't duplicate the entire project log (reference it)
- Don't leave handover from weeks ago (overwrite each session)
- Don't skip the handover "because it's quick" — context loss compounds
- Don't put bugs or feature backlog here — use `gh issue create` instead (see CLAUDE.md)
- **Don't overwrite without fetching from main first** — parallel sessions will lose work
