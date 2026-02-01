# CLAUDE.md

Project instructions for Claude Code when working in this repository.

## Project Overview

Session management, corpus ingestion, and Claude Code skills. This is the **tools repo** of a three-repo system:

- `mik-workflow/` — **This repo.** Scripts and skills
- `mik-workflow-state/` — Session state (HANDOVER.md, project-log.md)
- `mikrub.com/` — Content repo (Hugo site + corpus)

## Repository Structure

```
mik-workflow/
├── scripts/           # CLI tools (symlink to ~/bin/)
│   ├── start-session  # Create worktree + state branch
│   ├── close-session  # Merge repos and cleanup
│   ├── sync-memory    # Basic-Memory cloud sync
│   ├── mik-ingest     # Process inbox → sources
│   ├── mik-fetch-email
│   ├── mik-describe-images
│   └── mik-vision-report
├── skills/            # Claude Code skills (/slash commands)
├── tests/             # Vision API test suite
├── .env.example       # Environment template
└── docker-compose.yml # Basic-Memory SSE server
```

## Environment Variables

Scripts source from `.env` in repo root:

```bash
# Required
CORPUS_PATH="$HOME/Dropbox/repos/mikrub.com/corpus"
MAIN_REPO="$HOME/Dropbox/repos/mikrub.com"
STATE_REPO="$HOME/Dropbox/repos/mik-workflow-state"

# Optional
GMAIL_USER, GMAIL_APP_PASSWORD  # For mik-fetch-email
GEMINI_API_KEY                   # For mik-describe-images
```

## Session Workflow

```bash
# Start (terminal)
start-session my-feature    # Creates worktree + state branch

# Work in Claude Code...
# End of session skills:
/update-project-log         # Appends to $STATE_REPO/project-log.md
/archive-transcript         # Saves to mikrub.com/corpus/transcripts/
/agent-handover             # Updates $STATE_REPO/HANDOVER.md

# Commit and close (terminal)
git add -A && git commit -m "session: description"
close-session my-feature    # Merges both repos, cleanup
```

## Skills

Skills are procedural workflows invoked with `/skill-name`:

| Skill | Purpose |
|-------|---------|
| `/update-project-log` | Append session decisions to project log |
| `/archive-transcript` | Save conversation to corpus |
| `/agent-handover` | Update HANDOVER.md for next session |
| `/ingest` | Process files from inbox to sources |
| `/session-capture` | Route documentation to appropriate files |
| `/update-draft` | Incorporate research into article drafts |
| `/resolve-merge` | Resolve git merge conflicts semantically |

## Basic-Memory Sync

`sync-memory` syncs local corpus to cloud. See `mikrub.com/corpus/sources/mik-knowledge-system-architecture.md` for full architecture.

```bash
sync-memory           # Daily bidirectional sync
sync-memory --check   # Verify sync status
sync-memory --resync  # Reset baseline (first run)
```

## Dependencies

- [ccswitch](https://github.com/ksred/ccswitch) — Git worktree session management
- [MinerU](https://github.com/opendatalab/MinerU) — PDF/image → markdown
- [Basic-Memory](https://github.com/basicmachines-co/basic-memory) — Semantic search MCP
- Gemini API — Vision descriptions (optional)
