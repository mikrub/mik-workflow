# mik-workflow

Personal knowledge workflow tools for Claude Code: session management, corpus ingestion, and procedural skills.

## What This Is

A collection of scripts and Claude Code skills for managing a personal knowledge corpus. Designed to work alongside a separate content repository (your blog, notes, research).

**This repo contains the tools. Your content lives elsewhere.**

## Quick Start

```bash
# 1. Clone
git clone https://github.com/mikrub/mik-workflow.git
cd mik-workflow

# 2. Configure
cp .env.example .env
# Edit .env with your paths

# 3. Add to PATH (or symlink scripts to ~/bin/)
echo 'export PATH="$PATH:~/path/to/mik-workflow/scripts"' >> ~/.zshrc
source ~/.zshrc

# 4. Start a session
start-session my-feature
```

## Scripts

| Script | Purpose |
|--------|---------|
| `start-session` | Create git worktree for isolated Claude Code session |
| `close-session` | Merge worktree branch and cleanup |
| `sync-memory` | Sync corpus to Basic-Memory cloud |
| `mik-fetch-email` | Fetch emails from Gmail to corpus inbox |
| `mik-ingest` | Process inbox files → markdown with frontmatter |
| `mik-describe-images` | Add AI descriptions to images (Gemini Flash) |
| `mik-vision-report` | Generate HTML report for Vision API tests |

## Skills

Claude Code skills (procedural workflows invoked with `/command`):

- `/update-project-log` — Append session decisions to permanent log
- `/archive-transcript` — Save conversation to corpus
- `/agent-handover` — Update handover state for next session
- `/ingest` — Process files with proper frontmatter
- `/session-capture` — Route documentation to appropriate files
- `/resolve-merge` — Resolve merge conflicts semantically

## Environment Variables

Create `.env` from `.env.example`:

```bash
# Required
CORPUS_PATH="$HOME/repos/mysite/corpus"
MAIN_REPO="$HOME/repos/mysite"

# Optional (for specific scripts)
GMAIL_USER="your@gmail.com"
GMAIL_APP_PASSWORD="xxxx xxxx xxxx xxxx"
GEMINI_API_KEY="your-key"
```

## Dependencies

- **[ccswitch](https://github.com/ksred/ccswitch)** — Git worktree session management
- **[MinerU](https://github.com/opendatalab/MinerU)** — PDF/image extraction (optional, for mik-ingest)
- **[Basic-Memory](https://github.com/basicmachines-co/basic-memory)** — Semantic search (optional)

## Session Workflow

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  start-session  │────▶│  Claude Code    │────▶│  close-session  │
│  (terminal)     │     │  (work)         │     │  (terminal)     │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                              │
                              ▼
                        End-of-session:
                        /update-project-log
                        /archive-transcript
                        /agent-handover
                        git commit
```

## Ingestion Pipeline

```
Email/PDF/Image
      │
      ▼
┌─────────────────┐
│ mik-fetch-email │  (Gmail → corpus/inbox/)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   mik-ingest    │  (MinerU extraction + frontmatter)
└────────┬────────┘
         │
         ▼
┌─────────────────────┐
│ mik-describe-images │  (Gemini Vision descriptions)
└─────────────────────┘
         │
         ▼
    corpus/sources/
```

## License

MIT
