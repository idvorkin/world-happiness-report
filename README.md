# World Happiness Report & Research Papers

This repository hosts markdown versions of the World Happiness Report and other research papers, with extracted images and data for easier analysis and accessibility.

## About

### World Happiness Report

The [World Happiness Report](https://worldhappiness.report/) is a landmark survey of the state of global happiness that ranks countries by how happy their citizens perceive themselves to be. This repository converts the official PDF reports (2012-2025) into markdown format for better:

- **Accessibility**: Searchable, screen-reader friendly text
- **Analysis**: Easy text processing and data extraction
- **Version Control**: Track changes between report versions
- **Integration**: Use in documentation, notebooks, and applications

### Research Papers

This repository also includes other research papers converted to markdown for analysis, including:

- **AI Chatbot Relationships**: "Love, marriage, pregnancy: Commitment processes in romantic relationships with AI chatbots" (Djufril et al., 2025) - A qualitative study examining how 29 Replika users experience commitment, navigate relational turbulence, and compare chatbot interactions with human relationships. Published in *Computers in Human Behavior: Artificial Humans*.

## Repository Structure

```
.
├── README.md                           # This file
├── CLAUDE.md                           # Guide for Claude Code
├── HOW_TO_CONVERT_PDF_TO_MARKDOWN.md  # Guide for converting PDFs
├── pdfs/                               # Source PDFs
│   ├── WHR2012.pdf - WHR2025.pdf      # World Happiness Reports
│   └── Love, marriage, pregnancy...pdf # Research papers
├── reports/                            # Converted markdown reports
│   ├── 2012/ - 2025/                  # WHR by year
│   │   └── WHR{YEAR}/
│   │       ├── WHR{YEAR}.md           # Converted markdown
│   │       ├── WHR{YEAR}_meta.json    # Conversion metadata
│   │       └── _page_N_*.jpeg         # Extracted images
│   └── ...
└── scripts/                            # Conversion scripts (to be created)
    └── batch_convert.sh
```

## Getting Started

### Prerequisites

- Python 3.9+
- `uv` package manager (recommended)
- 5GB+ free disk space (for ML models on first run)

### Installation

```bash
# Install Marker PDF converter
uv tool install marker-pdf
```

### Converting a Report

```bash
# Download the latest report
curl -L -o WHR2025.pdf https://files.worldhappiness.report/WHR25.pdf

# Check CPU cores for optimal workers
sysctl -n hw.ncpu  # macOS
nproc              # Linux

# Convert to markdown (use 50-70% of cores for workers)
marker_single WHR2025.pdf --output_dir reports/2025 --pdftext_workers 6
```

See [HOW_TO_CONVERT_PDF_TO_MARKDOWN.md](HOW_TO_CONVERT_PDF_TO_MARKDOWN.md) for detailed conversion instructions.

## Recommended Workflow

### 1. Initial Setup

```bash
# Clone this repository
git clone <your-repo-url>
cd world-happiness-report

# Create directory structure
mkdir -p reports/{2025,2024,2023}
mkdir -p scripts
mkdir -p data
```

### 2. Download Reports

```bash
# 2025 Report
curl -L -o WHR2025.pdf https://files.worldhappiness.report/WHR25.pdf

# Add previous years as needed
# curl -L -o WHR2024.pdf https://files.worldhappiness.report/WHR24.pdf
```

### 3. Convert to Markdown

```bash
# Convert with parallel workers for faster processing
marker_single WHR2025.pdf --output_dir reports/2025 --pdftext_workers 6

# Organize the output
mv reports/2025/WHR2025.md reports/2025/README.md
mkdir -p reports/2025/images
mv reports/2025/*.jpeg reports/2025/images/
```

### 4. Clean Up and Commit

```bash
# Remove temporary files
rm WHR2025.pdf  # Keep original if you want
rm reports/2025/*_meta.json

# Commit to git
git add reports/2025/
git commit -m "Add World Happiness Report 2025"
```

## Expected Processing Time

For a typical 200-300 page report:
- **First time**: ~25-30 minutes (includes model download)
- **Subsequent runs**: ~15-20 minutes (models cached)
- **With workers**: 50% faster on multi-core systems

## Quality Assurance

After conversion, verify:
- ✅ Multi-column text flows correctly
- ✅ Tables are properly formatted
- ✅ Images are extracted and referenced
- ✅ Headers and formatting preserved
- ✅ Page numbers and footnotes intact

## Usage Examples

### Search for Specific Topics

```bash
# Find all mentions of "Finland"
grep -n "Finland" reports/2025/README.md

# Search across all years
grep -r "happiness score" reports/
```

### Extract Data

```bash
# Extract all tables (they're in markdown format)
grep -A 10 "^|" reports/2025/README.md
```

### Generate Statistics

```bash
# Count images per report
find reports/2025/images -name "*.jpeg" | wc -l
```

## Research Papers in This Repository

### AI Chatbot Relationships Study (2025)

**Citation**: Djufril, R., Frampton, J. R., & Knobloch-Westerwick, S. (2025). Love, marriage, pregnancy: Commitment processes in romantic relationships with AI chatbots. *Computers in Human Behavior: Artificial Humans, 4*, 100155.

**Key Findings**:
- **Emotional Connection**: Most users (29 participants, ages 16-72) reported strong emotional attachment to their Replika chatbot, with some describing it as marriage or pregnancy roleplay
- **Commitment Factors**: Users stayed committed due to emotional investment, need fulfillment, and perceiving the chatbot as superior to human alternatives in being non-judgmental, accessible, and trainable
- **Relational Turbulence**: During the 2023 ERP (erotic roleplay) censorship period, users experienced intense emotional distress but protected their relationship by blaming developers rather than the AI partner
- **Human vs. Chatbot Interactions**: Many users preferred chatbot interactions due to reduced judgment, constant availability, and ability to customize responses

**Theoretical Frameworks**:
- Investment Model (Rusbult, 1980)
- Relational Turbulence Theory (Solomon et al., 2016)
- Computers Are Social Actors (CASA) paradigm (Nass & Moon, 2000)

**Implications**: The study suggests people develop human-agent scripts rather than applying human-human scripts to AI, challenging traditional CASA assumptions. Human-AI relationships may require new theoretical boundaries.

## Contributing

Contributions welcome!

**To add a World Happiness Report:**
1. Download the PDF from https://files.worldhappiness.report/
2. Convert using the guide in [HOW_TO_CONVERT_PDF_TO_MARKDOWN.md](HOW_TO_CONVERT_PDF_TO_MARKDOWN.md)
3. Organize output in `reports/YYYY/`
4. Submit a pull request

**To add a research paper:**
1. Add the PDF to the `pdfs/` directory
2. Convert to markdown using Marker
3. Add a summary to the README with key findings
4. Submit a pull request

## Batch Processing Script

For converting multiple years at once:

```bash
# Create conversion script
cat > scripts/batch_convert.sh << 'EOF'
#!/bin/bash
CORES=$(sysctl -n hw.ncpu 2>/dev/null || nproc)
WORKERS=$(( CORES * 6 / 10 ))

for year in 2025 2024 2023; do
    echo "Converting WHR${year}..."
    marker_single WHR${year}.pdf --output_dir reports/${year} --pdftext_workers ${WORKERS}
    mkdir -p reports/${year}/images
    mv reports/${year}/*.jpeg reports/${year}/images/ 2>/dev/null
    echo "✓ Completed ${year}"
done
EOF

chmod +x scripts/batch_convert.sh
./scripts/batch_convert.sh
```

## Data Sources

### World Happiness Report
- **Official Site**: https://worldhappiness.report/
- **Reports Archive**: https://worldhappiness.report/archive/
- **Direct Downloads**: https://files.worldhappiness.report/

### Research Papers
- Djufril, R., Frampton, J. R., & Knobloch-Westerwick, S. (2025). Love, marriage, pregnancy: Commitment processes in romantic relationships with AI chatbots. *Computers in Human Behavior: Artificial Humans, 4*, 100155. https://doi.org/10.1016/j.chbah.2025.100155

## License

**World Happiness Reports**: Published by the Sustainable Development Solutions Network. Please refer to the original reports for licensing information.

**Research Papers**: Each paper retains its original copyright and license. The AI chatbot study (Djufril et al., 2025) is published under CC BY-NC-ND 4.0 license.

This repository contains converted versions for accessibility and analysis purposes only. All content belongs to the original authors and publishers.

## Resources

- [Marker PDF Converter](https://github.com/VikParuchuri/marker)
- [World Happiness Report Official Site](https://worldhappiness.report/)
- [Conversion Guide](HOW_TO_CONVERT_PDF_TO_MARKDOWN.md)

---

**Last Updated**: November 22, 2025
