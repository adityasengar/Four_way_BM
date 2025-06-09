# DNA Branch Migration Analysis

This repository contains the oxDNA simulation data and Python analysis scripts required to reproduce Figure 5 from the paper: **"Overcoming the speed limit of four-way DNA branch migration with bulges in toeholds."**

The code processes raw simulation output for two distinct DNA systems, calculates their free energy profiles, and generates publication-quality comparison plots.

## System Requirements

- **Python 3.6+**
- **NumPy**: `pip install numpy`
- **Matplotlib**: `pip install matplotlib`

## Directory Structure

For the analysis to run correctly, your data must be organized in the following structure relative to the main script, `generate_figure_5.py`:

```
your_project_folder/
├── generate_figure_5.py
├── README.md
└── data/
    ├── diff_w1/
    ├── diff_w1x/
    ├── ... (other 1-bulge folders)
    │   └── d_1/, d_2/, ..., d_20/
    │       └── energy.dat
    │
    ├── diff_w3_middle_noendx2/
    ├── ... (other 2-bulge 'middle' window folders)
    │
    ├── diff_w3_startov/
    ├── ... (other 2-bulge 'start' window folders)
    │
    ├── diff_w3_endov/
    ├── ... (other 2-bulge 'end' window folders)
    │
    └── 4strand_2kink/
        ├── wfile_middle_noend.txt
        ├── wfile_startov.txt
        └── wfile_endov.txt
```

## Quick Start 🚀

1. Clone the repository and navigate into the project directory.
2. Ensure your `data/` directory is structured as shown above.
3. Install the required Python packages.
4. Run the entire analysis pipeline with a single command:

```bash
python generate_figure_5.py
```

The script will print its progress to the console and automatically create two new directories: `wham_analysis_output/` for intermediate data and `final_plots/` for the final figures.

---

## Part 1: Analysis of the 1-Bulge System

This part of the analysis calculates the free energy profile for the simpler system containing a single bulge.

### 📋 Method

The analysis is performed by the `analyze_1_bulge_system()` function. Since this system was not simulated with umbrella sampling, the analysis is a direct calculation:

1. **Data Extraction**: For each of the 20 independent simulation runs, the script reads all `energy.dat` files from the `data/diff_w1*` folders. It extracts data from columns 7, 9, and 10, which represent the order parameters and statistical weights.

2. **Histogramming**: The data is used to construct a weighted 2D histogram of the order parameters.

3. **Projection & Normalization**: The 2D histogram is projected onto the reaction coordinate axis (the bulge's position) to create a 1D probability distribution.

4. **Statistical Analysis**: The process is repeated for all 20 runs, and the final mean probability profile and its standard error of the mean (SEM) are calculated.

### 🗂️ Inputs

- **Raw Data**: `energy.dat` files located in `data/diff_w1*/d_*/`.

### ✨ Intermediate Results

The function returns the calculated positions, mean probabilities, and standard errors for the 1-bulge system, which are then passed to the final plotting function.

---

## Part 2: Pre-processing of 2-Bulge System Data

This step reads and organizes the raw data for the more complex 2-bulge system, which was simulated using umbrella sampling across three overlapping windows.

### 📋 Method

This logic is contained within the `analyze_2_bulge_system()` function and serves as the initial data preparation step for WHAM:

1. **File Aggregation**: The script identifies three distinct groups of folders corresponding to the 'start', 'middle', and 'end' simulation windows.

2. **Data Consolidation**: For each of the 20 runs, it reads the `energy.dat` files from all folders belonging to a specific window and combines them. It extracts the final 5 columns of data from these files.

3. **Saving Intermediate Data**: The consolidated data for each run and each window is saved as a NumPy array (`.npy` file). This pre-processing step makes the subsequent WHAM analysis much faster and more efficient.

### 🗂️ Inputs

- **Start Window Data**: `energy.dat` files in `data/diff_w3_startov*/`.
- **Middle Window Data**: `energy.dat` files in `data/diff_w3_middle_noend*/`.
- **End Window Data**: `energy.dat` files in `data/diff_w3_endov*/`.

### ✨ Generated Files

This step creates the `wham_analysis_output/` directory and populates it with the following files, which contain the data for all 20 simulation runs:

- `preprocessed_data_window1.npy`: Pre-processed data for the 'middle' window.
- `preprocessed_data_window2.npy`: Pre-processed data for the 'start' window.
- `preprocessed_data_window3.npy`: Pre-processed data for the 'end' window.

---

## Part 3: WHAM Analysis of the 2-Bulge System

This is the core statistical part of the analysis, where the biased data from the three windows is combined into a single, unbiased free energy profile.

### 📋 Method

The Weighted Histogram Analysis Method (WHAM) is a powerful statistical technique used to analyze data from umbrella sampling simulations. The `analyze_2_bulge_system()` function performs this as follows:

1. **Load Data**: For each of the 20 independent runs, the script loads the pre-processed `.npy` files (from Part 2) and the corresponding biasing weight files.

2. **WHAM Iterations**: It solves a set of self-consistent equations to iteratively find the unbiased probability distribution that best represents the data from all three windows simultaneously.

3. **Projection & Normalization**: Once the WHAM calculation converges, the resulting multi-dimensional probability distribution is projected onto the reaction coordinate of interest (bulge position) to get the final 1D probability profile for that run.

4. **Statistical Analysis**: This process is repeated for all 20 runs to calculate the final mean probability and SEM.

### 🗂️ Inputs

- **Pre-processed Data**: The `.npy` files generated in Part 2.
- **WHAM Weight Files**: Located in `data/4strand_2kink/`, these files contain the information about the biasing potentials applied during the simulations:
  - `wfile_middle_noend.txt`
  - `wfile_startov.txt`
  - `wfile_endov.txt`

### ✨ Generated Files

- `wham_analysis_output/4strand_2bulge_prob_python_with_error.csv`: A detailed CSV file containing the final calculated data for the 2-bulge system: Position, Mean Probability, SEM of Probability, Free Energy, and SEM of Free Energy.

---

## Part 4: Final Figure Generation

This final part takes the results from both systems and generates the publication-quality plots comparing their behavior.

### 📋 Method

The `create_final_plots()` function handles all plotting:

1. **Data Collection**: It receives the final processed data (mean probabilities and errors) from the 1-bulge and 2-bulge analyses.

2. **Free Energy Calculation**: It calculates the free energy profiles as F/kT = −ln(P) and propagates the standard errors accordingly. The profiles are normalized to start at 0 kT for clear comparison.

3. **Plot Generation**: Using matplotlib, it creates two highly styled comparison plots with clear labels, legends, and error bars.

### ✨ Generated Files and Final Results

This is the main output of the entire pipeline. The script creates the `final_plots/` directory and saves the following figures in PNG, TIFF, and EPS formats:

#### 1. Probability Profile Comparison
- **Files**: `Prob_enhanced.png`, `.tiff`, `.eps`
- **Description**: This plot shows the probability of the bulge's position for the 1-bulge (red) and 2-bulge (blue) systems. The y-axis is logarithmic to emphasize differences across the entire energy landscape. Error bars indicate the standard error of the mean (SEM).

#### 2. Free Energy Profile Comparison
- **Files**: `Free_energy_enhanced.png`, `.tiff`, `.eps`
- **Description**: This plot presents the central result—the free energy landscapes in units of kT. It directly visualizes the energy barriers, the stability of intermediate states, and the overall thermodynamic difference between the two systems.
