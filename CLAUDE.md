# CLAUDE.md

Project instructions for Claude Code when working in this repository.

## Project Overview

Personal knowledge workflow tools: session management, corpus ingestion, and Claude Code skills. Designed to work alongside a separate corpus/content repository.

## Repository Structure

```
mik-workflow/
├── scripts/           # CLI tools (symlink to ~/bin/ or add to PATH)
│   ├── start-session  # Create worktree session
│   ├── close-session  # Merge and cleanup session
│   ├── sync-memory    # Sync corpus to Basic-Memory cloud
│   ├── mik-ingest     # Process inbox → sources
│   ├── mik-fetch-email    # Fetch from Gmail
│   ├── mik-describe-images # Add Vision API descriptions
│   └── mik-vision-report   # Generate test reports
├── skills/            # Claude Code skills (procedural workflows)
├── tests/             # Vision API test suite
├── .env.example       # Environment template
└── docker-compose.yml # Basic-Memory SSE server
```

## Setup

1. Clone this repo
2. Copy `.env.example` to `.env` and fill in values
3. Either:
   - Add `scripts/` to PATH: `export PATH="$PATH:/path/to/mik-workflow/scripts"`
   - Or symlink individual scripts to `~/bin/`
4. Configure skills in your corpus repo's `.claude/skills/` directory

## Environment Variables

Scripts source from `.env` in repo root. Required variables:

- `CORPUS_PATH` — Path to corpus directory (e.g., `~/repos/mysite/corpus`)
- `MAIN_REPO` — Path to main content repo (for close-session)

Optional:
- `GMAIL_USER`, `GMAIL_APP_PASSWORD` — For mik-fetch-email
- `GEMINI_API_KEY` — For mik-describe-images

## Session Workflow

```bash
# Start new session (creates worktree)
start-session my-feature

# Work in Claude Code...

# End session (in Claude Code)
/update-project-log
/archive-transcript
/agent-handover
git add -A && git commit -m "session: description"

# Merge and cleanup (in terminal)
close-session my-feature
```

## Skills

Skills are procedural workflows for Claude Code. They're invoked with `/skill-name` in the chat.

Available skills:
- `/update-project-log` — Append session decisions to project log
- `/archive-transcript` — Save conversation to corpus
- `/agent-handover` — Update HANDOVER.md for next session
- `/ingest` — Process files from inbox to sources
- `/session-capture` — Route documentation to appropriate files
- `/update-draft` — Incorporate research into article drafts
- `/resolve-merge` — Resolve git merge conflicts semantically

To use these skills in your corpus repo, symlink them:
```bash
mkdir -p .claude/skills/update-project-log
ln -s /path/to/mik-workflow/skills/update-project-log.md .claude/skills/update-project-log/SKILL.md
```

## Script Categories

**System scripts** (standalone, manual):
- `start-session` / `close-session` — Session lifecycle
- `sync-memory` — Basic-Memory cloud sync

**Pipeline scripts** (sequential workflow):
```
mik-fetch-email → mik-ingest → mik-describe-images
```

## Dependencies

- [ccswitch](https://github.com/ksred/ccswitch) — Git worktree session management
- [MinerU](https://github.com/opendatalab/MinerU) — PDF/image → markdown (for mik-ingest)
- [Basic-Memory](https://github.com/basicmachines-co/basic-memory) — Semantic search MCP
- Gemini API — For Vision descriptions (optional)
