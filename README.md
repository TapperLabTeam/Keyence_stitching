# Image Stitching Notebook - README

This Jupyter notebook automates the process of stitching microscopy image tiles using ImageJ's Grid/Collection stitching plugin through PyImageJ.

## Author
Antoine Carton-Leclercq, PhD  
Tapper Lab, UMass Chan Medical School, Worcester MA

## Overview

The notebook processes multiple slides of brain tissue sections, where each slide contains multiple slices (XY positions), and each slice consists of a grid of overlapping image tiles. It automatically stitches these tiles together to create complete section images.

## Prerequisites

- FIJI/ImageJ installation
- Conda/Mamba package manager

## Installation

### 1. Install Miniforge3

Download and install Miniforge3 from the official repository:
- Visit: https://github.com/conda-forge/miniforge
- Download the Windows installer
- Run the installer and follow the prompts

### 2. Create the Stitching Environment

Open the Miniforge Prompt (or Anaconda Prompt if using Anaconda) and run:

```bash
# Create a new environment called 'stitching'
conda create -n stitching python=3.9

# Activate the environment
conda activate stitching

# Install required packages
conda install -c conda-forge pyimagej openjdk=11
conda install -c conda-forge ipykernel pandas

# Register the environment as a Jupyter kernel
python -m ipykernel install --user --name=stitching --display-name "Python (stitching)"
```

### 3. Install FIJI

If you don't already have FIJI installed:
- Download from: https://fiji.sc/
- Install to a known location (e.g., `C:\Users\YourUsername\Documents\QUINT\Fiji`)
- Note the installation path for later use

## Configuration

Before running the notebook, you'll need to modify several paths:

### In the first code cell:
```python
mouse_name = "B2_M1_CTL"  # Change to your sample name
mouse_folder = r"C:\Users\acart\Desktop\B2_M1_CTL"  # Path to your data folder
```

### In the second code cell:
```python
ij = imagej.init(r"C:\Users\acart\Documents\QUINT\Fiji", headless=False)
```
Update the path to point to your FIJI installation folder.

## Data Organization

Your data should be organized as follows:

```
mouse_folder/
├── S1_2/               # Slide 1
│   ├── XY01/          # Slice 1
│   │   ├── Image_XY01_00001_Z001_Overlay.tif
│   │   ├── Image_XY01_00002_Z001_Overlay.tif
│   │   └── ...
│   ├── XY02/          # Slice 2
│   └── ...
├── S3_4/              # Slide 2
├── S5_6/              # Slide 3
├── mouse_name_coordinates.csv  # Grid dimensions for each slice
└── Stitched/          # Output folder (created automatically)
```

### Coordinates CSV File

Create a CSV file named `{mouse_name}_coordinates.csv` with columns:
- `rows_S1_2`, `rows_S3_4`, `rows_S5_6`, etc. (number of rows in grid for each slide)
- `cols_S1_2`, `cols_S3_4`, `cols_S5_6`, etc. (number of columns in grid for each slide)

Each row in the CSV corresponds to one slice (XY position).

## Running the Notebook

1. Launch Jupyter Notebook or JupyterLab:
```bash
conda activate stitching
jupyter notebook
```

2. Open `Stitching_images.ipynb`

3. Select the kernel "Python (stitching)" from the kernel menu

4. Run all cells in order

## Output

Stitched images will be saved in the `Stitched/` subfolder within your mouse folder, with filenames:
- `{mouse_name}_s1.tif`
- `{mouse_name}_s2.tif`
- etc.

## Parameters

The stitching uses the following default parameters:
- **Tile overlap**: 30%
- **Fusion method**: Linear Blending
- **Grid pattern**: Snake by rows, Right & Down
- **Regression threshold**: 0.30
- **Max/avg displacement threshold**: 2.50
- **Absolute displacement threshold**: 3.50

These can be modified in the `STITCHING_MACRO` string if needed.

## Troubleshooting

- **Java/ImageJ errors**: Ensure OpenJDK 11 is installed and FIJI path is correct
- **Memory issues**: For large images, ensure sufficient RAM is available
- **Import errors**: Verify all conda packages are installed in the correct environment
- **File not found**: Check that image filenames match the expected pattern in the macro

## Notes

- The stitching process can take several minutes per slice depending on image size and grid dimensions
- Progress is printed to the console as each slice is processed
- Intermediate files (`TileConfiguration.txt`) are saved in each slice folder