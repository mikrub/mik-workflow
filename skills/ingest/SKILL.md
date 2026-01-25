---
name: ingest
description: Process files from corpus/inbox into corpus/sources with proper frontmatter
  and naming. Use for new captures, research, or drafts.
permalink: skills/ingest
---

# Ingest

Move a file from `corpus/inbox/` (or another location) into `corpus/sources/` with proper frontmatter and naming convention.

## When to use

- Processing a newly captured file (web clip, PDF, screenshot)
- Starting a new draft from raw notes
- Adding research material to the corpus
- Any file that needs to become a proper corpus item

## Procedure

### 1. Find the file

**If user specifies a file:** Use that path directly.

**If no file specified:** Find the newest file in inbox:

```bash
ls -t corpus/inbox/ | head -5
```

If multiple files or unclear which one, ask the user:
> "Found these files in inbox: [list]. Which one should I process?"

Skip `HANDOVER.md` — that's not for ingestion.

### 2. Read and analyze the file

```bash
# Read the file
cat corpus/inbox/{filename}

# Check if it's from a web clip (MarkDownload adds source URL)
head -20 corpus/inbox/{filename}
```

Determine from content:
- **source_type:** research (external), draft (user's writing), log, meta
- **capture_format:** web_clip, pdf, image, email, manual
- **Existing title or URL** (MarkDownload includes these)

### 3. Gather metadata

If not obvious from content, ask the user:

> "I'll add this to the corpus. Please confirm or adjust:
> - **Title:** {suggested title}
> - **Type:** research | draft | log | meta
> - **Tags:** {suggested tags based on content}
> - **Summary:** {one-line summary}"

For web clips, extract the source URL from the file header if present.

### 4. Generate frontmatter

Create frontmatter following the schema:

```yaml
---
id: {slug-from-title}-{YYYY-MM}
title: "{title}"
source_url: {url if web clip, omit otherwise}
source_type: {research | draft | log | meta}
capture_format: {web_clip | pdf | image | email | manual}
date_captured: {YYYY-MM-DD}
date_published: {if known, omit otherwise}
public: false
tags:
  - {tag1}
  - {tag2}
summary: |
  {One to two sentence summary}
---
```

### 5. Create the processed file

Filename format: `{slug}.md` (date lives in frontmatter, not filename)

```bash
# Create new file with frontmatter + original content
cat > corpus/sources/{slug}.md << 'EOF'
{frontmatter}

{original content, cleaned up if needed}
EOF
```

### 6. Remove from inbox

```bash
rm corpus/inbox/{original-filename}
```

### 7. Commit

```bash
git add corpus/sources/{slug}.md
git rm corpus/inbox/{original-filename}  # if it was tracked
git commit -m "corpus: add {slug} ({source_type})"
```

---

## Frontmatter field reference

| Field | Required | Values | Notes |
|-------|----------|--------|-------|
| id | Yes | `{slug}-{YYYY-MM}` | Stable, URL-safe |
| title | Yes | String | Descriptive |
| source_url | If web | URL | Original source |
| source_type | Yes | research, draft, log, meta | What kind of content |
| capture_format | Yes | web_clip, pdf, image, email, manual | How it was captured |
| date_captured | Yes | YYYY-MM-DD | When added to corpus |
| date_published | If known | YYYY-MM-DD | Original publication date |
| public | Yes | false (default) | true = published to site |
| tags | Yes | List | For search/organization |
| summary | Yes | Text | 1-2 sentences for search previews |

## Examples

### Web clip (MarkDownload)

Input file might have:
```
# Article Title

> Clipped from https://example.com/article

Content...
```

Becomes:
```yaml
---
id: article-title-2026-01
title: "Article Title"
source_url: https://example.com/article
source_type: research
capture_format: web_clip
date_captured: 2026-01-19
public: false
tags:
  - {relevant-topic}
summary: |
  {Brief description of what the article covers}
---
```

### New draft

Input: rough notes or outline

```yaml
---
id: my-new-article-2026-01
title: "My New Article"
source_type: draft
capture_format: manual
date_captured: 2026-01-19
public: false
tags:
  - {topic}
summary: |
  Draft exploring {topic}
---
```

### PDF stub

For PDFs, create a markdown stub that references the binary:

```yaml
---
id: research-paper-2026-01
title: "Research Paper Title"
source_url: https://arxiv.org/...
source_type: research
capture_format: pdf
date_captured: 2026-01-19
public: false
tags:
  - {topic}
summary: |
  {What the paper is about}
---

PDF: `../assets/research-paper.pdf`

## Key points

- {Extract key points here for searchability}
```

Move the actual PDF to `corpus/assets/`.