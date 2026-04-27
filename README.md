 """# Blood-Brain Barrier Permeability Prediction

## Overview
Fine-tuned ChemBERTa model to predict whether a molecule can cross the Blood-Brain Barrier (BBB).

## Dataset
- **Source:** B3DB (Blood-Brain Barrier Database)
- **Size:** 7,805 compounds
- **Labels:** BBB+ (permeable) / BBB- (non-permeable)

## Model
- **Base Model:** ChemBERTa-zinc-base-v1
- **Task:** Binary classification
- **Framework:** PyTorch + HuggingFace Transformers

## Results
| Metric | Score |
|--------|-------|
| Accuracy | 88% |
| F1 Score | 0.91 |
| ROC-AUC | 0.857 |

## How to Run
1. Open `notebooks/bbb_main.ipynb` in Google Colab
2. Run all cells sequentially
3. Model trains in ~10 minutes on T4 GPU

## Team
- Rohit Saharay
- Tiyas Datta
- Abhik Ghosh
"""

with open('/content/bbb/README.md', 'w') as f:
    f.write(readme)

%cd /content/bbb
!git add README.md
!git commit -m "add README"
!git push origin main
