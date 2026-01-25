# mik-workflow

Personal knowledge workflow tools: session management, corpus ingestion, and Claude Code skills.

Part of a three-repo system:
- **mik-workflow** (this repo) — Scripts and skills
- **mik-workflow-state** — Session state (HANDOVER.md, project-log.md)
- **mikrub.com** — Content repo (Hugo site + corpus)

## Quick Start

```bash
# 1. Clone
git clone https://github.com/mikrub/mik-workflow.git
cd mik-workflow

# 2. Configure
cp .env.example .env
# Edit .env with your paths

# 3. Add scripts to PATH
echo 'export PATH="$PATH:~/Dropbox/repos/mik-workflow/scripts"' >> ~/.zshrc
source ~/.zshrc

# 4. Start a session
start-session my-feature
```

## Scripts

| Script | Purpose |
|--------|---------|
| `start-session` | Create git worktree + state branch |
| `close-session` | Merge both repos and cleanup |
| `sync-memory` | Sync corpus to Basic-Memory cloud |
| `mik-fetch-email` | Fetch emails from Gmail to corpus inbox |
| `mik-ingest` | Process inbox files → markdown with frontmatter |
| `mik-describe-images` | Add AI descriptions to images (Gemini Flash) |
| `mik-vision-report` | Generate HTML report for Vision API tests |

## Skills

Claude Code skills (procedural workflows invoked with `/command`):

| Skill | Purpose |
|-------|---------|
| `/update-project-log` | Append session decisions to project log |
| `/archive-transcript` | Save conversation to corpus |
| `/agent-handover` | Update HANDOVER.md for next session |
| `/ingest` | Process files with proper frontmatter |
| `/session-capture` | Route documentation to appropriate files |
| `/update-draft` | Incorporate research into article drafts |
| `/resolve-merge` | Resolve merge conflicts semantically |

## Environment Variables

Create `.env` from `.env.example`:

```bash
# Required
CORPUS_PATH="$HOME/Dropbox/repos/mikrub.com/corpus"
MAIN_REPO="$HOME/Dropbox/repos/mikrub.com"
STATE_REPO="$HOME/Dropbox/repos/mik-workflow-state"

# Optional
GMAIL_USER="your@gmail.com"
GMAIL_APP_PASSWORD="xxxx xxxx xxxx xxxx"
GEMINI_API_KEY="your-key"
```

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

## Dependencies

- **[ccswitch](https://github.com/ksred/ccswitch)** — Git worktree session management
- **[MinerU](https://github.com/opendatalab/MinerU)** — PDF/image extraction (optional)
- **[Basic-Memory](https://github.com/basicmachines-co/basic-memory)** — Semantic search (optional)

## License

MIT
