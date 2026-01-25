# Vision API Test Suite

Benchmark Vision API providers for image description quality. Tests the `mik-describe-images` script.

## Structure

```
tests/vision-api/
├── README.md           # This file
├── inputs/             # Source test files
│   ├── diagram-*.png   # Architecture/flow diagrams
│   ├── chart-*.png     # Charts and graphs
│   ├── screenshot-*.png # UI screenshots
│   └── document-*.pdf  # PDFs with embedded images
└── results/            # Provider outputs (gitignored)
    └── YYYY-MM-DD-provider-name/
        ├── *.md        # Markdown with descriptions
        └── report.html # Visual comparison
```

## Test Categories

| Category | Tests | What We're Measuring |
|----------|-------|---------------------|
| Diagrams | Architecture, flowcharts | Structure transcription, relationship extraction |
| Charts | Bar, line, pie | Axis labels, data point recognition, trend description |
| Screenshots | UI, terminal | Text extraction, layout understanding |
| Documents | PDF with images | Combined text + image description |

## Adding Test Files

Test files should be:
- **Public domain or CC0** licensed (for open-source sharing)
- **Varied complexity** (simple → complex)
- **Representative** of real corpus content

Good sources:
- [Unsplash](https://unsplash.com) (photos, CC0)
- [Pixabay](https://pixabay.com) (various, CC0)
- [Draw.io](https://draw.io) (create your own diagrams)
- Wikipedia (public domain diagrams)

## Running Tests

```bash
# Process all inputs with current provider
for f in tests/vision-api/inputs/*; do
  mik-describe-images "$f" --output tests/vision-api/results/$(date +%Y-%m-%d)-gemini-2.5-flash/
done

# Generate comparison report
mik-vision-report --open
```

## Search Assertions

After indexing results, these queries should find specific files:

| Query | Should Find |
|-------|-------------|
| "database schema" | diagram-database-*.md |
| "revenue growth" | chart-revenue-*.md |
| "login button" | screenshot-login-*.md |

## Comparing Providers

To benchmark a new provider:

1. Create results folder: `results/YYYY-MM-DD-provider-name/`
2. Process inputs with new provider
3. Generate report: `mik-vision-report results/YYYY-MM-DD-provider-name/`
4. Compare visually and via search assertions

## Current Baseline

- **Gemini 2.5 Flash** — Primary provider (free tier friendly)

Future providers to test:
- Claude Vision (Anthropic)
- GPT-4V (OpenAI)
- Llama Vision (Meta)
- Local models (LLaVA, etc.)
