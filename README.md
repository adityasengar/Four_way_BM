# DNA Branch Migration Analysis

Data and analysis code for reproducing Figure 5 from: **"Overcoming the speed limit of four-way DNA branch migration with bulges in toeholds"**

This repository contains oxDNA simulation data and Python analysis scripts for processing raw simulation output, calculating free energy profiles using the Weighted Histogram Analysis Method (WHAM), and generating publication-quality comparison plots.

## Table of Contents

- [Overview](#overview)
- [System Requirements](#system-requirements)
- [Installation](#installation)
- [Directory Structure](#directory-structure)
- [Quick Start](#quick-start)
- [Detailed Analysis Workflow](#detailed-analysis-workflow)
- [Data Description](#data-description)
- [Output Files](#output-files)


## Overview

This analysis compares two DNA branch migration systems:
- **1-bulge system**: Single bulge in toehold region
- **2-bulge system**: Double bulge configuration with umbrella sampling across three windows

The analysis processes 20 independent simulation runs for each system to calculate mean probability profiles and their standard errors.

## System Requirements

- Python 3.6 or higher
- Required Python packages:
  - NumPy (numerical operations)
  - Matplotlib (plotting)

## Installation

1. Clone this repository:
```bash
git clone [repository-url]
cd dna-branch-migration-analysis
```

2. Install required packages:
```bash
pip install numpy matplotlib
```

## Directory Structure

Ensure your data follows this exact structure:

```
your_project_folder/
├── generate_figure_5.py          # Main analysis script
├── README.md                     # This file
└── data/                         # All simulation data
    ├── diff_w1/                  # 1-bulge system folders
    ├── diff_w1x/
    ├── diff_w1x3/
    │   └── d_1/, d_2/, ..., d_20/  # Independent runs
    │       └── energy.dat          # Raw simulation output
    │
    ├── diff_w3_middle_noendx2/   # 2-bulge 'middle' window
    ├── diff_w3_middle_noendx3/
    ├── diff_w3_middle_noendx4/
    │
    ├── diff_w3_startov/          # 2-bulge 'start' window  
    ├── diff_w3_startovx2/
    ├── diff_w3_startovx3/
    │
    ├── diff_w3_endov/            # 2-bulge 'end' window
    ├── diff_w3_endovx/
    ├── diff_w3_endovx2/
    ├── diff_w3_endovx3/
    │
    └── 4strand_2kink/            # WHAM weight files
        ├── wfile_middle_noend.txt
        ├── wfile_startov.txt
        └── wfile_endov.txt
```

## Quick Start

1. Verify your directory structure matches the requirements above
2. Run the complete analysis:
```bash
python generate_figure_5.py
```
3. Check the output in `analysis_output/` and `final_plots/` directories

The script will display progress messages and generate all intermediate and final results automatically.

## Detailed Analysis Workflow

### Step 1: Data Processing
- **1-bulge system**: Extracts columns 7, 9, and 10 from `energy.dat` files across all simulation folders
- **2-bulge system**: Processes three umbrella sampling windows with corresponding weight files

### Step 2: WHAM Analysis
- Removes simulation bias using weight files
- Combines multiple windows into unbiased free energy profiles
- Calculates statistics across 20 independent runs

### Step 3: Plot Generation
- Creates probability vs. position plots
- Generates free energy vs. position comparisons
- Outputs publication-quality figures in multiple formats

## Data Description

### Raw Data Format
Each `energy.dat` file contains simulation output with multiple columns representing different order parameters and energies.

### 2-Bulge System Windows
The 2-bulge analysis uses three umbrella sampling windows:

| Window | Description | Data Folders | Weight File |
|--------|-------------|--------------|-------------|
| Start | Bulge near domain start | `diff_w3_startov*` | `wfile_startov.txt` |
| Middle | Bulge in central region | `diff_w3_middle_noend*` | `wfile_middle_noend.txt` |
| End | Bulge near domain end | `diff_w3_endov*` | `wfile_endov.txt` |

### Key Functions in `generate_figure_5.py`

- `analyze_1_bulge_system()`: Processes 1-bulge data and calculates mean probability profiles
- `analyze_2_bulge_system()`: Performs WHAM analysis on 2-bulge umbrella sampling data  
- `create_final_plots()`: Generates publication-ready comparison plots

## Output Files

### Analysis Output (`analysis_output/`)
- `*.npy`: Intermediate NumPy data arrays
- `*.csv`: Processed data in CSV format for further analysis

### Final Plots (`final_plots/`)
- `figure_5_probability.png/tiff/eps`: Probability vs. position comparison
- `figure_5_free_energy.png/tiff/eps`: Free energy vs. position comparison

All plots are generated in PNG, TIFF, and EPS formats for different publication requirements.

### Common Issues

1. **Missing data folders**: Ensure all required directories exist with the exact names shown above
2. **Missing weight files**: Check that all three weight files are present in `data/4strand_2kink/`
3. **Python package errors**: Verify NumPy and Matplotlib are properly installed
4. **Memory issues**: The analysis processes large datasets; ensure sufficient RAM is available

### Support

If you encounter issues not covered here, please [create an issue/contact information].
