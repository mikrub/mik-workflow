---
name: update-draft
description: Incorporate research into article drafts without dumping raw text in
  chat. Use when adding sources or restructuring drafts.
permalink: skills/update-draft
---

# Update draft

## When to use

- Adding new research/sources to an existing draft
- Restructuring or reorganizing a draft
- Expanding a section based on new conversation
- Revising based on feedback

Don't use for:
- Starting a new draft from scratch (just create the file)
- Final polish/copyedit (different workflow)
- Publishing (change `draft: false` and push)

## Procedure

### 1. Find the draft

```bash
# Search by filename or title
ls content/posts/*draft*
grep -l "draft: true" content/posts/*.md
```

### 2. Understand current state

Before modifying, note:
- Current structure (sections, flow)
- Word count / length
- What's solid vs. placeholder

### 3. Incorporate new material

**"Incorporate this source":**
- Read the source material
- Identify where it fits in current structure
- Weave in naturally (don't just append)
- Add to references section if exists

**"Expand section X":**
- Find the section
- Develop with more detail, examples, evidence
- Maintain voice consistency
- Check transitions to adjacent sections

**"Restructure":**
- Propose new structure first
- Get confirmation before major surgery
- Move content to new locations
- Fix transitions

**"Revise based on feedback":**
- Address specific feedback points
- Track what was changed
- Don't over-correct (maintain author voice)

### 4. Commit

```bash
git add content/posts/{draft-file}.md
git commit -m "draft({slug}): {brief description of changes}"
```

## Coherence guidelines

**Voice consistency:** Match the existing tone. If draft is formal, don't add casual asides.

**Flow:** New material should feel native, not bolted on. Transitions matter.

**Length discipline:** Adding material tends to bloat. After incorporating, check if anything can be cut.