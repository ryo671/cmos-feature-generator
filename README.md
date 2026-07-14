# CMOS Feature Generation Simulator for Event Detection Systems

This repository provides the official software-based feature generation pipeline to simulate the physical processing and output of a custom feature-extractable CMOS image sensor. It converts standard RGB images (e.g., the COCO dataset) into quantized edge-feature images (8-bit, 3-bit, and 1-bit) for training and evaluating ultra-low-power object detection models.

## Pipeline Overview

The pipeline reproduces the hardware behaviors of the proprietary CMOS image sensor through the following sequential steps:

1. **Pseudo-RAW Generation (Upscaling):** Resizes input RGB images by a factor of 3 to approximate a Full HD resolution and applies color-specific absorption rates.
2. **Grayscale Conversion:** Extracts grayscale information at a 10-bit resolution using simulated RGGB Bayer pattern filters with the formula:
   $$Y = R + 2G + B$$
3. **Hardware Noise Injection:** Mathematically introduces artificial Gaussian noise (**0.5 LSB @ 8-bit** precision) to replicate the physical noise inherent in the sensor's analog subtraction process.
4. **Vertical Differentiation:** Extracts horizontal edges by subtracting vertically adjacent pixels.
5. **Quantization & Bit-shifting:** Applies signal clipping, decimation, and signal truncation to reduce the bit depth.
6. **Horizontal Binning:** Performs pixel binning to generate the final compressed feature images.

## Getting Started

### 1. Clone the Repository
First, clone this repository to your local machine or workspace:
```bash
git clone [https://github.com/your_username/your_repository_name.git](https://github.com/your_username/your_repository_name.git)
cd your_repository_name
```
*(Note: Replace `your_username/your_repository_name` with your actual repository path)*

### 2. Install Prerequisites
Ensure you have Python 3.x installed. You can install all required dependencies using the provided `requirements.txt`:
```bash
pip install -r requirements.txt
```

## Directory Structure & Preparation

Before running the notebook, ensure you have the standard COCO dataset (or any custom RGB dataset) organized as follows:

```text
/workspace/hdd/original_8bit/
 ├── train2017/
 │    ├── 000000000001.jpg
 │    └── ...
 └── val2017/
      ├── 000000000042.jpg
      └── ...
```
*(Note: If your dataset is located elsewhere, you will need to update the path in the notebook as described in the Usage section below.)*

## Usage

1. **Configure Paths:** Open the Jupyter Notebook (`dataset_cre.ipynb`) and modify the input/output directory paths if your environment differs from the default settings:
   ```python
   dataset_dir = "/workspace/hdd/original_8bit"
   save_dir = "/workspace/YOLOv7/coco/images" 
   ```

2. **Select Quantization Level:** The notebook contains three separate execution blocks to generate feature images at different bit depths. Run the block that matches your target hardware specifications:
   - **8-bit Feature Images:** Standard edge-extracted images.
   - **3-bit Feature Images:** Aggressively quantized images (Clipping = 4, Decimation = 4).
   - **1-bit Feature Images:** Binary edge images using a gradient/no-gradient threshold.

3. **Run the Simulation:** Execute the cells. The progress bar (`tqdm`) will display the conversion status, and the generated `.png` images will be saved in your specified output directory.
