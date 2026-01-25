---
name: resolve-merge
description: Resolve git merge conflicts with semantic awareness. Use when close-session
  fails with conflicts.
permalink: skills/resolve-merge
---

# Resolve Merge

Intelligently resolve git merge conflicts by understanding the semantic meaning of conflicted files. Git sees text conflicts; this skill sees document purposes.

## When to use

When `close-session <name>` fails with merge conflicts in either:
- The main content repo (mik-workflow)
- The state repo (`$STATE_REPO` - contains HANDOVER.md, project-log.md)

```
CONFLICT (content): Merge conflict in HANDOVER.md
Automatic merge failed; fix conflicts and then commit the result.
```

## Prerequisites check

Before proceeding, verify:

```bash
# Must be on main branch
git branch --show-current  # Should output: main

# Must be in main repo, not a worktree
pwd  # Should be ~/Dropbox/repos/mikrub.com (not ~/.ccswitch/worktrees/...)

# Must have unresolved conflicts
git status  # Should show "Unmerged paths"
```

**If in a worktree:** You need to resolve from the main repo. Run:
```bash
cd ~/Dropbox/repos/mikrub.com
```

## Procedure

### Step 1: Identify conflicted files

```bash
git status | grep "both modified"
```

### Step 2: Categorize each file

Read each conflicted file and identify its type:

| File pattern | Location | Type | Merge strategy |
|--------------|----------|------|----------------|
| `HANDOVER.md` | `$STATE_REPO` | Session state | Combine "Just completed" sections, take latest state table |
| `project-log.md` | `$STATE_REPO` | Append-only history | Keep ALL entries from both sides (never lose history) |
| `*-draft*.md` | content repo | Living draft | Combine additions, resolve conflicting edits by content |
| `*.md` in `skills/` | mik-workflow | Procedure doc | Keep both additions (e.g., both anti-patterns) |
| `mik-knowledge-system-architecture.md` | corpus | Architecture doc | Bump version, merge sections |
| `weekend-*-guide.md` | corpus | Setup guide | Keep both additions, update status |
| Config files | varies | Settings | Case-by-case, usually take newer |

### Step 3: Resolve each file

For each conflicted file:

1. **Read the file** with conflict markers
2. **Understand both sides:**
   - `<<<<<<< HEAD` = what's already on main
   - `>>>>>>> feature/branch` = what the merging branch added
3. **Apply semantic merge** based on file type
4. **Write the resolved version** (no conflict markers)
5. **Stage the file:** `git add <file>`

### Step 4: Commit the merge

```bash
git commit -m "$(cat <<'EOF'
Merge feature/<branch>: <brief description>

Resolved conflicts in:
- file1.md: <what was merged>
- file2.md: <what was merged>

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
EOF
)"
```

### Step 5: Push and clean up

```bash
git push origin main
```

Then clean up the original worktree:
```bash
ccswitch cleanup <session-name>
```

## Merge strategies by file type

### HANDOVER.md (session state, in STATE_REPO)

**Philosophy:** HANDOVER reflects current state, not history.

- **Frontmatter:** Take latest timestamp, combine session summaries
- **"Just completed":** Combine both sessions' bullets
- **"Current state" table:** Take the more complete/recent status for each row
- **Reference sections:** Keep all (MinerU setup, Email setup, etc.)
- **"Next session":** Combine options from both
- **File locations:** Merge to show all new files

### project-log.md (append-only, in STATE_REPO)

**Philosophy:** Never lose history. All entries are valid.

- Keep ALL session entries from both sides
- Order chronologically (if same day, order by logical sequence)
- Never modify existing entries (append-only is sacred)

### Article drafts (*-draft*.md)

**Philosophy:** Accumulate material; don't lose insights.

- Keep all new sections from both sides
- If same section edited differently, combine or note both perspectives
- Update footer timestamps to reflect all sources

### Skills (corpus/skills/*.md)

**Philosophy:** Procedures should be complete and cover all cases.

- Keep all added sections (e.g., both new anti-patterns)
- If conflicting instructions, take the more complete/safer version
- Ensure procedure still flows logically

### Architecture doc

**Philosophy:** This is the source of truth; conflicts indicate divergent decisions.

- Bump version number
- Add both decisions if they're about different topics
- If conflicting decisions on same topic, flag for human review

## Example resolution

Given HANDOVER.md conflict:
```
<<<<<<< HEAD
## Just completed
- Moved backlog to GitHub Issues
=======
## Just completed
- Created mik-fetch-email script
>>>>>>> feature/email-capture
```

Resolved:
```
## Just completed
- Created mik-fetch-email script
- Moved backlog to GitHub Issues
```

## Anti-patterns

- Don't just take one side (loses work)
- Don't blindly accept "theirs" or "ours" (git strategies don't understand content)
- Don't leave conflict markers in files
- Don't merge without reading the actual content
- Don't forget to update timestamps/versions when combining content

## After resolution

Consider running `/session-capture` to document the merge itself if it revealed something interesting about the workflow.
