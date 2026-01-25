---
title: README
type: note
permalink: skills/readme
---

# Skills

Procedural skills for Claude Code. These are available as `/slash-commands` in Claude Code, or can be read manually in claude.ai.

## Available Skills

| Skill | Command | Purpose |
|-------|---------|---------|
| [ingest](ingest.md) | `/ingest` | Process files from inbox to sources with frontmatter |
| [update-project-log](update-project-log.md) | `/update-project-log` | Append session decisions to project log |
| [archive-transcript](archive-transcript.md) | `/archive-transcript` | Save chat transcript to corpus |
| [agent-handover](agent-handover.md) | `/agent-handover` | Update handover for next session |
| [update-draft](update-draft.md) | `/update-draft` | Incorporate research into article drafts |

## Session Workflow

**End of every session** (in order):
1. `/update-project-log`
2. `/archive-transcript`
3. `/agent-handover`

**When processing new captures:**
- `/ingest` — moves file from inbox to sources with proper metadata

**When writing:**
- `/update-draft` — incorporate research into drafts

## File Structure

```
corpus/skills/           ← Source of truth (this directory)
  README.md              ← You are here
  ingest.md
  update-project-log.md
  archive-transcript.md
  agent-handover.md
  update-draft.md

.claude/skills/          ← Symlinks for Claude Code
  ingest/SKILL.md        → ../../../corpus/skills/ingest.md
  update-project-log/... → ...
  (etc.)
```

## Adding New Skills

1. Create `corpus/skills/{skill-name}.md` with frontmatter:
   ```yaml
   ---
   name: skill-name
   description: Brief description for skill listing
   ---
   ```

2. Create symlink for Claude Code:
   ```bash
   mkdir -p .claude/skills/{skill-name}
   ln -s ../../../corpus/skills/{skill-name}.md .claude/skills/{skill-name}/SKILL.md
   ```

3. Update this README