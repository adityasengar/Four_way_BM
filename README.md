# DNA Branch Migration Analysis

This repository contains the oxDNA simulation data and Python analysis scripts required to reproduce Figure 5 from the paper: **"Overcoming the speed limit of four-way DNA branch migration with bulges in toeholds"**.

The project investigates a novel mechanism to accelerate four-way DNA branch migration (FWBM) by introducing unpaired bases (bulges) into the toehold region. Standard FWBM is often slow, limiting its use in dynamic DNA nanotechnology. This work demonstrates, through both experiment and simulation, that bulges can destabilize the DNA junction and provide an alternative migration pathway, leading to a significant increase in reaction speed.

The provided code processes raw simulation output for a 1-bulge and a 2-bulge system, calculates their free energy profiles, and generates the final publication-quality comparison plots.

## System Requirements

- **Python 3.6+**
- **NumPy**: `pip install numpy`
- **Matplotlib**: `pip install matplotlib`

## Directory Structure

For the analysis to run correctly, your data must be organized in the following structure. Each `d_i/` folder represents an independent simulation run and contains the necessary files to execute an oxDNA simulation.

```
your_project_folder/
├── generate_figure_5.ipynb
├── README.md
└── data/
    ├── diff_w1*, diff_w1x*, etc. # 1-bulge system folders
    │   └── d_1/, d_2/, ..., d_20/
    │       ├── input             # Defines simulation parameters
    │       ├── generated.top     # System topology
    │       ├── generated.dat     # Initial configuration
    │       ├── sequence.txt      # DNA sequences
    │       ├── op.txt / op2.txt  # Order parameter definitions
    │       ├── wfile.txt         # Umbrella sampling weights
    │       └── energy.dat        # Simulation output used for analysis
    │
    ├── diff_w3_middle_noend*, etc. # 2-bulge 'middle' window folders
    ├── diff_w3_startov*, etc.      # 2-bulge 'start' window folders
    ├── diff_w3_endov*, etc.        # 2-bulge 'end' window folders
    │
    └── 4strand_2kink/
        ├── wfile_middle_noend.txt # WHAM analysis weight files
        ├── wfile_startov.txt
        └── wfile_endov.txt
```

## About the oxDNA Simulation Data

The simulations were performed using oxDNA, a coarse-grained model ideal for capturing the structural and thermodynamic properties of DNA. For more detailed information on the model and its usage, please refer to the [official oxDNA Documentation](https://lorenzo-rovigatti.github.io/oxDNA/index.html).

The files within each `d_i/` directory are runtime files for oxDNA simulations. Their roles are as follows:

- **`input`**: A text file defining system parameters such as temperature, salt concentration, simulation algorithm, and total simulation steps.
- **`generated.top` / `generated.dat`**: These are the topology and configuration files, respectively. They define the molecule's structure, connectivity, and the initial positions of the nucleotides in the simulation box.
- **`sequence.txt`**: Contains the specific DNA sequences of the strands used in the simulation.
- **`op.txt` / `op2.txt`**: These are the order parameter files, which are critical for tracking the system's state. They specify which base pairs and interactions should be monitored during the simulation. For instance, they are used to track the formation of "default" versus "displaced" base pairs to determine the position of the bulge.
- **`wfile.txt`**: The weight file used for umbrella sampling. It defines the energy biases (weights) applied to the different states defined by the order parameters in `op2.txt`. This technique encourages the simulation to explore high-energy states, ensuring a complete sampling of the energy landscape.

The simulations were run using the Virtual Move Monte Carlo (VMMC) algorithm. In this workflow, it is crucial to first define the interactions to be tracked in `op2.txt`. The corresponding biasing potentials are then defined in `wfile.txt`, and both are used by the VMMC algorithm to efficiently sample the system's conformational space.

## Quick Start 🚀

1. Clone the repository and navigate into the project directory.
2. Ensure your `data/` directory is structured as shown above.
3. Install the required Python packages.
4. Download the data. Before running the script, download the Simulation_data_analysis.zip file from the following link: https://zenodo.org/records/15623394. Unzip it, and ensure the data folder is in the same directory as the Python/Jupyter notebook.
5. Run the entire analysis pipeline in the jupyter notebook

The script will print its progress and create two directories: `wham_analysis_output/` for intermediate data and `final_plots/` for the final figures.

---

## Part 1: Analysis of the 1-Bulge System

This part calculates the free energy profile for the system with a single bulge.

### 📋 Method

The analysis is performed by the `analyze_1_bulge_system()` function:

1. **Data Extraction**: For each of the 20 independent simulation runs, the script reads the `energy.dat` output file.

2. **Histogramming & Projection**: It constructs a weighted histogram of the relevant order parameters and projects it onto the reaction coordinate (bulge position) to get a 1D probability distribution.

3. **Statistical Analysis**: The process is repeated for all 20 runs to calculate the mean probability profile and its standard error of the mean (SEM).

### 🗂️ Inputs

- **Simulation Output**: `energy.dat` files from the `data/diff_w1*/d_*/` directories.

---

## Part 2: Pre-processing of 2-Bulge System Data

This step organizes the raw data for the 2-bulge system, which required a more complex simulation setup.

### 📋 Method

This logic is inside the `analyze_2_bulge_system()` function:

1. **Data Consolidation**: The 2-bulge system simulation was slow to sample and was therefore performed in three overlapping windows ('start', 'middle', 'end') to improve efficiency. This function consolidates the `energy.dat` files for each window across all 20 runs.

2. **Saving Intermediate Data**: The consolidated data is saved into `.npy` files in the `wham_analysis_output/` directory, preparing them for the next stage.

### 🗂️ Inputs

- **Simulation Output**: `energy.dat` files from all `diff_w3_*` directories.

---

## Part 3: WHAM Analysis of the 2-Bulge System

This core statistical step combines the data from the three biased simulation windows into a single, unbiased free energy profile.

### 📋 Method

The Weighted Histogram Analysis Method (WHAM) is used to rigorously analyze the umbrella sampling data:

1. **Load Data**: For each run, the script loads the pre-processed `.npy` files and the corresponding WHAM weight files from `data/4strand_2kink/`.

2. **WHAM Iterations**: It solves a set of self-consistent equations to remove the simulation biases and combine the data from the three windows.

3. **Statistical Analysis**: The resulting unbiased probability distribution is calculated for each of the 20 runs, and these are then averaged to find the mean profile and its SEM.

### 🗂️ Inputs

- **Pre-processed Data**: `.npy` files from Part 2.
- **WHAM Weight Files**: Located in `data/4strand_2kink/`.

---

## Part 4: Final Figure Generation

This part generates the final publication-quality plots comparing the 1-bulge and 2-bulge systems, reproducing Figure 5 of the manuscript.

### 📋 Method

The `create_final_plots()` function receives the final data from both analyses and generates two comparison plots:

1. **Probability Profile Comparison**: This plot shows the probability of the bulge's position for both systems. The y-axis is logarithmic to highlight key differences.

2. **Free Energy Profile Comparison**: This plot shows the free energy landscapes (F/kT = −ln(P)). It provides a direct visualization of the energy barriers and stability, explaining why the bulge-containing systems are faster.

### ✨ Generated Files and Final Results

The script creates the `final_plots/` directory and saves the following figures in PNG, TIFF, and EPS formats:

#### 1. Probability Profile Comparison
- **Files**: `Prob_enhanced.png`, `.tiff`, `.eps`
- **Description**: Compares the probability distributions of the bulge location for the 1-bulge and 2-bulge systems.

#### 2. Free Energy Profile Comparison
- **Files**: `Free_energy_enhanced.png`, `.tiff`, `.eps`
- **Description**: Compares the free energy landscapes, visualizing the difference in energy barriers and thermodynamic stability between the two systems.
