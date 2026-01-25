---
name: session-capture
description: Analyze session and route documentation to appropriate files. Use at
  end of session to decide what goes where before running other end-of-session skills.
permalink: skills/session-capture
---

# Session Capture

Analyze the current session and route documentation to the appropriate files. This skill determines what was significant about the session and updates the right documents.

## When to use

At or near the end of a session, BEFORE running the standard end-of-session skills (`/update-project-log`, `/archive-transcript`, `/agent-handover`). This skill helps decide WHAT to document WHERE.

## Procedure

### Step 1: Analyze the session

Review the conversation and categorize what happened:

**Architecture decisions** — Did we:
- Discover how something works (system architecture, tool behavior)?
- Make a design choice with tradeoffs?
- Find a bug/limitation and document a workaround?
- Change our understanding of the tech stack?

**Setup/config changes** — Did we:
- Fix a broken installation?
- Update a config file?
- Discover new setup steps?
- Find version requirements?

**Story development** — Did we:
- Complete a phase or milestone?
- Hit an interesting failure and recover?
- Learn something surprising our audience would care about?
- Have a "meta moment" worth narrating?

### Step 2: Route to documents

Based on analysis, update the appropriate files:

| Category | Document | What to add |
|----------|----------|-------------|
| Architecture decision | `corpus/sources/mik-knowledge-system-architecture.md` | New section or update existing. Include version bump. |
| Setup/config change | Current weekend guide (e.g., `weekend-2-guide.md`) | Add/update steps, gotchas, version pins |
| Story development | Current article draft (e.g., `the-residue-article-3-draft.md`) | New section or update narrative |
| Always | `$STATE_REPO/project-log.md` | Summary via `/update-project-log` |

### Step 3: Check for compound updates

Sessions often have multiple types of updates. Example:
- Discovered MCP connector architecture (→ architecture doc)
- Fixed version pinning issue (→ weekend guide)
- Wrote about "the connector maze" (→ article draft)

All three documents should be updated, plus the standard log entry.

### Step 4: Summarize for user

Before executing updates, summarize:

```
This session contains:
- [ ] Architecture: [brief description]
- [ ] Setup: [brief description]
- [ ] Story: [brief description]

I'll update:
- architecture doc v0.X → v0.Y
- weekend-2-guide.md
- article-3-draft.md

Proceed?
```

## Decision heuristics

**When to update architecture doc:**
- "We learned how X actually works" (not just "we used X")
- "We chose A over B because..." (tradeoffs documented)
- "This limitation means we need to..." (constraints discovered)

**When to update weekend guide:**
- "To make this work, you need to..." (setup steps)
- "Pin to version X because..." (gotchas)
- "The config should look like..." (reference configs)

**When to update article draft:**
- "This would be interesting to readers" (teachable moment)
- "This fits the narrative arc" (story continuity)
- "This is the kind of detail that shows real-world experience" (authenticity)

**When to skip:**
- Routine troubleshooting that didn't reveal anything new
- Discussion without decisions
- Research that's still ongoing (capture in log, not permanent docs)

## Example assessment

```
Session: Debugging Basic-Memory MCP issues

Architecture? YES
- Discovered connector inheritance model (cloud vs local sync)
- Found version 0.16+ breaks mcp+cloud coexistence
→ Add MCP Connector Architecture section

Setup? YES
- Need to pin to v0.15.0
- Need full path to uvx in Desktop config
→ Update weekend guide with version pin

Story? YES
- "The connector maze" is a good narrative beat
- Shows complexity behind "just connect MCP"
→ Add section to article 3

Log? ALWAYS
→ Run /update-project-log
```

## After this skill

Run the standard end-of-session sequence:
1. `/update-project-log` — Captures this session's decisions
2. `/archive-transcript` — Saves conversation
3. `/agent-handover` — Updates HANDOVER.md

## Notes

This skill is about **routing** — deciding where information goes. The actual writing still needs human judgment on tone, depth, and audience. When uncertain, err toward capturing in the log (always recoverable) rather than immediately updating permanent docs.
