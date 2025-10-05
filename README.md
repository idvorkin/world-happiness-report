# World Happiness Report

This repository hosts markdown versions of the World Happiness Report with extracted images and data for easier analysis and accessibility.

## About

The [World Happiness Report](https://worldhappiness.report/) is a landmark survey of the state of global happiness that ranks countries by how happy their citizens perceive themselves to be. This repository converts the official PDF reports into markdown format for better:

- **Accessibility**: Searchable, screen-reader friendly text
- **Analysis**: Easy text processing and data extraction
- **Version Control**: Track changes between report versions
- **Integration**: Use in documentation, notebooks, and applications

## Repository Structure

```
.
├── README.md                           # This file
├── HOW_TO_CONVERT_PDF_TO_MARKDOWN.md  # Guide for converting PDFs
├── reports/                            # Converted markdown reports (to be created)
│   ├── 2025/
│   │   ├── WHR2025.md
│   │   └── images/
│   ├── 2024/
│   └── ...
└── scripts/                            # Conversion scripts (to be created)
    └── convert.sh
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

## Contributing

Contributions welcome! To add a new report:

1. Download the PDF
2. Convert using the guide in [HOW_TO_CONVERT_PDF_TO_MARKDOWN.md](HOW_TO_CONVERT_PDF_TO_MARKDOWN.md)
3. Organize output in `reports/YYYY/`
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

- **Official Site**: https://worldhappiness.report/
- **Reports Archive**: https://worldhappiness.report/archive/

## License

The World Happiness Reports are published by the Sustainable Development Solutions Network. Please refer to the original reports for licensing information.

This repository contains converted versions for accessibility and analysis purposes. All content belongs to the original authors.

## Resources

- [Marker PDF Converter](https://github.com/VikParuchuri/marker)
- [World Happiness Report Official Site](https://worldhappiness.report/)
- [Conversion Guide](HOW_TO_CONVERT_PDF_TO_MARKDOWN.md)

---

**Last Updated**: October 5, 2025
