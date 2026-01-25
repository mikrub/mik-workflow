---
name: archive-transcript
description: Save chat transcript to corpus for semantic search. Use at end of significant
  work sessions before closing chat.
permalink: skills/archive-transcript
---

# Archive transcript

Save the full chat transcript to the corpus for semantic search. Captures reasoning, decisions, tool usage, and full context.

**Important:** Transcripts go to `mikrub.com/corpus/transcripts/` (the content repo), NOT to mik-workflow or mik-workflow-state. The content repo is where Basic-Memory indexes from.

## Environment detection

This skill works in both Claude Code and claude.ai. First, determine your environment:

**Claude Code:** Can access `~/.claude/projects/` directly
**claude.ai:** Has `/mnt/transcripts/` ephemeral filesystem

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

In Claude Code, transcripts are auto-saved. You can copy them directly to the repo.

### 1. Find current session transcript

Claude Code maintains a `sessions-index.json` with metadata including `gitBranch`. This allows finding the correct transcript even with parallel sessions.

```bash
# Get current branch
CURRENT_BRANCH=$(git branch --show-current)

# Get project path encoded for Claude's directory structure
PROJECT_PATH=$(pwd | sed 's|/|-|g' | sed 's|^-||')
SESSIONS_DIR="$HOME/.claude/projects/-${PROJECT_PATH}"

# Find transcript for current branch from sessions-index.json
if [ -f "$SESSIONS_DIR/sessions-index.json" ]; then
  # Get the most recent session on current branch
  TRANSCRIPT=$(cat "$SESSIONS_DIR/sessions-index.json" | \
    python3 -c "import json,sys; d=json.load(sys.stdin); \
    sessions=[e for e in d.get('entries',[]) if e.get('gitBranch')=='$CURRENT_BRANCH']; \
    print(sessions[-1]['fullPath'] if sessions else '')")

  if [ -n "$TRANSCRIPT" ]; then
    echo "Found transcript: $TRANSCRIPT"
  else
    echo "No transcript found for branch: $CURRENT_BRANCH"
    # Fallback: most recent .jsonl file
    TRANSCRIPT=$(ls -t "$SESSIONS_DIR"/*.jsonl 2>/dev/null | head -1)
    echo "Falling back to most recent: $TRANSCRIPT"
  fi
else
  # Fallback: most recent .jsonl file
  TRANSCRIPT=$(ls -t "$SESSIONS_DIR"/*.jsonl 2>/dev/null | head -1)
  echo "No sessions-index.json, using most recent: $TRANSCRIPT"
fi
```

**Note:** When using `ccswitch` for parallel sessions, each worktree has its own branch, so this will correctly identify the transcript for the current session.

### 2. Copy transcript to corpus

```bash
# Copy to mikrub.com corpus (where Basic-Memory indexes)
CORPUS_TRANSCRIPTS="$HOME/Library/CloudStorage/Dropbox/repos/mikrub.com/corpus/transcripts"
cp "$TRANSCRIPT" "$CORPUS_TRANSCRIPTS/$(date +%Y-%m-%d)-{slug}.jsonl"
```

### 3. Create markdown wrapper

Create `mikrub.com/corpus/transcripts/{date}-{slug}.md`:

```markdown
---
id: transcript-{date}-{slug}
title: "Chat transcript: {descriptive title}"
source_type: transcript
date_created: {date}
tags:
  - transcript
  - {project-tag}
---

# Chat transcript: {title}

**Date:** {date}
**Environment:** Claude Code
**Session:** {brief description of what was accomplished}

## Key decisions

- {Decision 1}
- {Decision 2}

## Summary

{Brief summary of session}

## Full transcript

See: `{date}-{slug}.jsonl`
```

### 4. Commit in mikrub.com repo

```bash
cd ~/Library/CloudStorage/Dropbox/repos/mikrub.com
git add corpus/transcripts/
git commit -m "transcript: {brief session description}"
git push
```

---

## claude.ai procedure

In claude.ai, transcripts are ephemeral in `/mnt/transcripts/`. **Must archive before closing chat.**

### 1. Check available transcripts

```bash
ls -la /mnt/transcripts/
cat /mnt/transcripts/journal.txt
```

### 2. Copy transcript to outputs

```bash
cp /mnt/transcripts/{transcript-filename}.txt /mnt/user-data/outputs/
```

### 3. Create markdown wrapper

```bash
cat > /mnt/user-data/outputs/{date}-{slug}.md << 'EOF'
---
id: transcript-{date}-{slug}
title: "Chat transcript: {descriptive title}"
source_type: transcript
date_created: {date}
tags:
  - transcript
  - {project-tag}
---

# Chat transcript: {title}

**Date:** {date}
**Environment:** claude.ai
**Session:** {brief description}

## Key decisions

- {Decision 1}
- {Decision 2}

## Full transcript

See: `{filename}.txt`
EOF
```

### 4. Present files for download

Use the file presentation tool to make files available for download.

### 5. Manual steps after download

```bash
cd ~/mikrub.com
mv ~/Downloads/{files} corpus/transcripts/
git add corpus/transcripts/
git commit -m "transcript: {brief session description}"
git push
```

---

## Notes

- Transcripts can be large (300-500KB per session)
- Git LFS is configured for `corpus/transcripts/*.txt` and `*.jsonl`
- The markdown wrapper helps Khoj surface transcripts in semantic search
- Don't archive trivial Q&A sessions, only significant work