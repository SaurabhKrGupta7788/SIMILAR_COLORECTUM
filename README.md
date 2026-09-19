# SIMILAR_COLORECTUM: Mask-Guided Multiple Instance Learning for Colorectal Cancer

> **🏆 Published Research**: Accepted for the *Proceedings of Sixth Emerging Trends and Technologies on Intelligent Systems (ETTIS 2026)*, published by **Springer Nature Singapore Pte Ltd.**
> **Title**: Mask-Guided Multiple Instance Learning for Patient-Level Colorectal Cancer Classification from CT Imaging.

This repository hosts the official codebase for utilizing Multiple Instance Learning (MIL) combined with spatial mask-guidance to accurately classify colorectal cancer at the patient level directly from CT imaging.

## Architecture Overview

\\\
Patient CT Scan Volume
    │
    ▼
┌───────────────────────────────────────┐
│  Patch Extraction & Masking           │  ← Generates Bag of Instances (Patches)
│  (Extracts tissue ROIs)               │
└──────────┬────────────────────────────┘
           │
           ▼
┌───────────────────────────────────────┐
│  Feature Extraction (CNN Backbone)    │  ← e.g., ResNet/DenseNet for patch embeddings
└──────────┬────────────────────────────┘
           │
    ┌──────┼───────────────┐
    ▼      ▼               ▼
┌───────┐ ┌─────────────┐ ┌─────────────┐
│ PCA   │ │ Branch MIL  │ │ Data Aug.   │
│ Proj. │ │ Pooling     │ │ Strategies  │
└───┬───┘ └──────┬──────┘ └──────┬──────┘
    │            │               │
    ▼            ▼               ▼
┌───────────────────────────────────────┐
│  Attention-Based MIL Aggregation      │ ← Computes patient-level prediction
└──────────────┬────────────────────────┘
               ▼
┌───────────────────────────────────────┐
│  Explainability (Grad-CAM)            │ ← Visualizes diagnostic hotspots
└───────────────────────────────────────┘
\\\

## System Output

The system generates both classification metrics and explainability maps:

| Output Type | Description |
|---|---|
| **Patient-Level Class** | Binary classification (e.g., Malignant vs. Benign). |
| **Attention Scores** | Highlights which specific patches (instances) contributed most to the diagnosis. |
| **Grad-CAM Heatmaps** | Localizes the exact visual features within the high-attention patches that triggered the prediction. |

## Directory Structure

\\\
├── Branch_MIL.ipynb            # Implementation of branched MIL architecture
├── MIL.ipynb                   # Baseline Multiple Instance Learning pipeline
├── MIL_(PCA_NoPCA).ipynb       # Feature dimensionality reduction comparisons
├── MIL_Aug(PCA_NoPCA).ipynb    # Data augmentation strategies combined with PCA
├── Grad_Cam.ipynb              # Explainability and visual heatmap generation
├── visualisaation.ipynb        # Data visualization and metric plotting
│
├── mil_aug_topk_Xy_binary.csv  # Serialized metrics/dataset mappings
├── mil_weighted_Xy_binary.csv  # Weighted instances mappings
└── README.md                   # Project documentation
\\\

## How to Run

### 1. Prerequisites
- Python 3.9+
- Jupyter Notebook / JupyterLab
- PyTorch & Torchvision
- scikit-learn, OpenCV, matplotlib

### 2. Install Dependencies

\\\ash
pip install torch torchvision numpy pandas scikit-learn opencv-python matplotlib jupyter
\\\

### 3. Run the Application
Since this is a research repository, the code is structured sequentially in notebooks.
1. Open the repository in Jupyter Notebook:
   \\\ash
   jupyter notebook
   \\\
2. Start with \MIL.ipynb\ to understand the baseline extraction and aggregation process.
3. Move to \MIL_Aug(PCA_NoPCA).ipynb\ to run the advanced pipeline (the core of the paper).
4. Run \Grad_Cam.ipynb\ on trained model weights to generate interpretability heatmaps.

## Key Design Decisions

1. **Patient-Level MIL Formulation**: CT scans contain thousands of slices/patches, most of which are healthy even in cancer patients. Framing this as a Multiple Instance Learning (MIL) problem where the patient is a "bag" of patches solves the severe spatial label sparsity problem.
2. **Mask-Guided Extraction**: Instead of blind tiling, patches are extracted using segmentation masks, forcing the MIL attention mechanism to focus purely on relevant anatomical structures rather than background noise.
3. **PCA Feature Reduction**: High-dimensional CNN embeddings for thousands of patches per patient can exhaust memory. The \(PCA_NoPCA)\ notebooks demonstrate that dimensionality reduction drastically improves training efficiency while maintaining diagnostic accuracy.
4. **Grad-CAM Explainability**: In medical imaging, trust is critical. \Grad_Cam.ipynb\ is intentionally separated to ensure every confident prediction can be visually audited by an oncologist.
