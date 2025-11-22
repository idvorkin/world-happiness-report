# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository converts World Happiness Report PDFs into markdown format for easier analysis and accessibility. The reports (2012-2025) are stored as both PDFs and converted markdown with extracted images.

**Data Source**: https://worldhappiness.report/ and https://files.worldhappiness.report/

## Repository Structure

```
.
├── pdfs/              # Source PDFs (WHR2012.pdf - WHR2025.pdf)
├── reports/           # Converted markdown reports organized by year
│   ├── 2012/
│   │   └── WHR2012/
│   │       ├── WHR2012.md          # Converted markdown
│   │       ├── WHR2012_meta.json   # Conversion metadata
│   │       └── _page_N_*.jpeg      # Extracted images
│   ├── 2013/ ... 2025/             # Same structure for each year
└── HOW_TO_CONVERT_PDF_TO_MARKDOWN.md  # Detailed conversion guide
```

## Core Workflow: Converting PDFs to Markdown

### Installation

```bash
# Install Marker PDF converter (required for conversions)
uv tool install marker-pdf
```

**First-time setup**: Marker downloads ~3GB of ML models on first run. Ensure 5GB+ free disk space.

### Converting a Single Report

```bash
# Download a report
curl -L -o pdfs/WHR2025.pdf https://files.worldhappiness.report/WHR25.pdf

# Check CPU cores to optimize workers
sysctl -n hw.ncpu  # macOS
nproc              # Linux

# Convert to markdown (use 50-70% of cores for workers)
# Example: 6 workers for a 10-core system
marker_single pdfs/WHR2025.pdf --output_dir reports/2025 --pdftext_workers 6
```

**Processing time**: 15-30 minutes for a typical 200-300 page report (first run adds 5-10 min for model downloads).

### Batch Conversion Script

For converting multiple years at once:

```bash
#!/bin/bash
# Determine optimal worker count (50-70% of CPU cores)
CORES=$(sysctl -n hw.ncpu 2>/dev/null || nproc)
WORKERS=$(( CORES * 6 / 10 ))

for year in 2025 2024 2023 2022; do
    echo "Converting WHR${year}..."
    marker_single pdfs/WHR${year}.pdf --output_dir reports/${year} --pdftext_workers ${WORKERS}
    echo "✓ Completed ${year}"
done
```

## Output Structure

After conversion, each year's directory contains:

- `WHR{YEAR}/WHR{YEAR}.md` - The main markdown file with properly formatted multi-column text, tables, and image references
- `WHR{YEAR}/_page_N_Figure_X.jpeg` - Extracted figures, charts, and diagrams
- `WHR{YEAR}/_page_N_Picture_X.jpeg` - Extracted photos and pictures
- `WHR{YEAR}/WHR{YEAR}_meta.json` - Conversion metadata (can be deleted)

## Searching and Analysis

```bash
# Find mentions of a topic across all years
grep -r "Finland" reports/

# Find in specific year
grep -n "happiness score" reports/2025/WHR2025/WHR2025.md

# Extract all tables (they're in markdown format)
grep -A 10 "^|" reports/2025/WHR2025/WHR2025.md

# Count images in a report
ls reports/2025/WHR2025/*.jpeg | wc -l
```

## Common Tasks

### Adding a New Report Year

1. Download the PDF:
   ```bash
   curl -L -o pdfs/WHR{YEAR}.pdf https://files.worldhappiness.report/WHR{YY}.pdf
   ```

2. Convert to markdown:
   ```bash
   marker_single pdfs/WHR{YEAR}.pdf --output_dir reports/{YEAR} --pdftext_workers 6
   ```

3. Verify quality:
   ```bash
   # Check multi-column text flows correctly
   head -200 reports/{YEAR}/WHR{YEAR}/WHR{YEAR}.md

   # Verify images extracted
   ls reports/{YEAR}/WHR{YEAR}/*.jpeg | wc -l
   ```

4. Commit:
   ```bash
   git add reports/{YEAR}/
   git commit -m "Add World Happiness Report {YEAR}"
   ```

### Monitoring Long-Running Conversions

```bash
# Run in background
marker_single file.pdf --output_dir . --pdftext_workers 6 > conversion.log 2>&1 &

# Monitor progress
tail -f conversion.log

# Check if still running
ps aux | grep marker_single
```

## Known Conversion Quality

Marker handles these elements well:
- Multi-column layouts (academic papers, reports)
- Tables (converted to markdown tables)
- Figures and images (extracted with references)
- Headers, formatting, page numbers, footnotes
- Complex document structures

May have issues with:
- Hand-written content
- Very complex mathematical equations
- Scanned documents with poor quality

## Report URL Patterns

- Latest: `https://files.worldhappiness.report/WHR25.pdf` (note: YY format, not YYYY)
- 2024: `https://files.worldhappiness.report/WHR24.pdf`
- Earlier years follow same pattern: WHR{YY}.pdf

## Quality Assurance Checklist

After converting a report, verify:
- Multi-column text flows in correct reading order
- Tables are properly formatted as markdown
- Images are extracted and referenced in the markdown
- Headers and formatting are preserved
- Page numbers and footnotes are intact

## Git Workflow

The repository uses standard git workflow on the `main` branch. When committing:
- Add converted reports to git
- Remove temporary `*_meta.json` files before committing (optional)
- Use descriptive commit messages like "Add World Happiness Report 2025"

## Development Philosophy

This is a data repository focused on:
- Accurate PDF-to-markdown conversion
- Preserving document structure and images
- Making reports searchable and accessible

When working in this repo, prioritize data accuracy and clean markdown output over code complexity.
