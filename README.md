# Handwritten Character Recognition Using CNN

**Deep Learning | Computer Vision | Image Classification**

A Convolutional Neural Network (CNN) project that classifies handwritten English letters **A–Z** using the **EMNIST Letters** dataset. The project compares a baseline model with two improved versions, focusing on generalisation through architectural changes, regularisation and image augmentation. A follow-up experiment examines **Convolutional Block Attention Module (CBAM)** and whether attention helps distinguish visually similar letters.

## Results

| Model | Test accuracy | Test loss |
| --- | ---: | ---: |
| Baseline CNN | 91.13% | 0.2894 |
| Improved CNN (V2) | 93.59% | 0.1906 |
| **CNN with data augmentation (V3)** | **94.40%** | **0.1706** |

**Key achievement:** Improved test accuracy by **3.27 percentage points**, from **91.13%** to **94.40%**.

## Dataset and preprocessing

- **Dataset:** EMNIST Letters, with 26 handwritten-character classes (A–Z), represented as 28 × 28 grayscale images.
- **Initial dataset:** 99,040 rows, each containing a label and 784 pixel values.
- **Data quality checks:** Detected invalid labels, duplicate rows and out-of-range pixel values.
- **Cleaning:** Removed invalid-labelled and duplicate rows, leaving **88,798 images**.
- **Image preparation:** Corrected orientation, reshaped images to `28 × 28 × 1` and normalised pixel values to `[0, 1]`.
- **Labels:** Remapped 1–26 to 0–25 and used one-hot encoding.
- **Split:** Stratified **70% training / 15% validation / 15% testing**.

## Model development

**Baseline:** A simple CNN with one convolutional layer (32 filters), max pooling, a dense layer and softmax output.

**V2 – Improved architecture:** Expanded to three convolutional layers (32, 64 and 128 filters), added batch normalisation and dropout, and used early stopping.

**V3 – Data augmentation:** Built on V2 and used small rotations, zooming and horizontal/vertical shifts to expose the CNN to more handwriting variations. No horizontal flipping was applied because flipping letters can change their identity.

## Evaluation and findings

Performance was compared using **training/validation curves, test accuracy, test loss, confusion matrices and per-class results**. The final V3 model improved overall performance and reduced the number of classes below 90% accuracy from **six to three**.

An important remaining limitation was the confusion between visually similar letters, especially **I** and **L**. Augmentation helped other difficult classes, but did not resolve this ambiguity at the source image resolution.

## Follow-up experiment: CBAM attention (Part C)

I extended the original CNN work with a **Convolutional Block Attention Module (CBAM)** that combines channel attention (which feature channels matter) and spatial attention (where important patterns appear).

Two controlled experiments compared the existing CNN architecture against versions enhanced with CBAM. The follow-up also included **three-seed robustness checks** and a detailed analysis of the **I / L** classification errors.

| Follow-up model | Test accuracy | Macro F1 | Total I ↔ L errors |
| --- | ---: | ---: | ---: |
| V2 baseline | 93.95% | 0.9392 | 217 |
| V2 + CBAM | 93.80% | 0.9379 | 216 |
| **V3 with augmentation** | **94.43%** | **0.9443** | 220 |
| V3 + CBAM | 94.08% | 0.9405 | 226 |

*These results are from the Part C controlled experiment; the earlier results table comes from the original Part A runs, so small numeric differences reflect separate experiments.*

**Key finding:** The CBAM extension did **not** improve overall performance. It reduced `L → I` errors but increased `I → L` errors, with little or no net benefit. Adding attention also increased training costs. This demonstrated the importance of evaluating added model complexity against real performance gains instead of assuming a more complex model is automatically better.

See [`emnist_cbam_experiments.ipynb`](emnist_cbam_experiments.ipynb) for source code and [`docs/CBAM_Experiment_Results.html`](docs/CBAM_Experiment_Results.html) for the original executed notebook export, including charts and outputs. HTML exports may need to be downloaded and opened locally to see the complete formatting.

## Tech stack

- **Language:** Python
- **Deep learning:** TensorFlow / Keras
- **Data processing:** Pandas, NumPy
- **Machine learning utilities:** scikit-learn
- **Visualisation:** Matplotlib, Seaborn
- **Development:** Jupyter Notebook

## Repository contents

```text
.
├── README.md
├── emnist_letters_cnn.ipynb   # Part A: CNN training and evaluation
├── emnist_cbam_experiments.ipynb # Part C: CBAM experiments
├── requirements.txt
├── .gitignore
└── docs/
    ├── CNN_Project_Presentation.pdf
    └── CBAM_Experiment_Results.html
```

The **dataset** and **trained model file** are intentionally excluded from the repository.

## How to run

1. Clone or download this repository.
2. Create and activate a Python environment.
3. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

4. Obtain the **EMNIST Letters CSV** used for this assignment, then place it at `data/emnist-letters.csv`. The dataset is **not included** in this repository.
5. From the repository root, launch Jupyter:

   ```bash
   jupyter notebook emnist_letters_cnn.ipynb
   ```

6. Run the notebook cells in order to reproduce the cleaning, training and evaluation process. For the attention experiments, open `emnist_cbam_experiments.ipynb` and use the same `data/emnist-letters.csv` file. Training time and numerical results can vary with the environment and random state.

> **Note:** The notebooks were reconstructed from Jupyter HTML exports. Code cells and written analysis were carried over, but previously rendered outputs are not embedded in the reconstructed notebooks. Local Windows dataset paths were replaced with `data/emnist-letters.csv` for portability. The Part C executed HTML export is included under `docs/` as a results reference. These reconstructed notebooks have not been rerun in this environment.

## Future improvements

- Investigate targeted handling of **I / L** confusion, including improved image resolution or class-specific augmentation; the CBAM experiment alone did not resolve the ambiguity.
- Evaluate the model on independent handwriting samples beyond the course dataset.
- Package the model behind a simple prediction interface.

## Context

Developed as an academic deep learning project. The reported metrics reflect held-out test results for this dataset, not performance guarantees on production handwriting.
