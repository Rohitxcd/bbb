# Blood-Brain Barrier Permeability Prediction using ChemBERTa



---

## Overview

The **Blood-Brain Barrier (BBB)** is a highly selective membrane that protects the brain from harmful substances in the bloodstream. However, it also prevents most drugs from entering the brain.

Approximately **98% of small-molecule drugs fail to cross the BBB**, making drug development for neurological diseases extremely challenging. Traditional laboratory testing methods (animal models and clinical experiments) are slow, expensive, and ethically complex.

This project builds a **machine learning model that predicts whether a molecule can cross the Blood-Brain Barrier using only its molecular structure**.

The model takes a **SMILES string representation of a molecule** as input and predicts:

- **BBB+** → molecule can cross the BBB
- **BBB-** → molecule cannot cross the BBB

The system uses **ChemBERTa**, a transformer model pretrained on millions of chemical molecules.

---

## Dataset

The model was trained using the **B3DB (Blood-Brain Barrier Database)**.

Dataset statistics:

| Property | Value |
|---|---|
| Total compounds | 7,807 |
| After SMILES validation | 7,805 |
| BBB+ (permeable) | 4,956 |
| BBB- (non-permeable) | 2,851 |

Columns used:

- **SMILES** → molecular structure (input)
- **Label** → BBB+ or BBB- (target)

All other columns such as CID, IUPAC names, and logBB values were removed.

The dataset was split using **stratified sampling**:

- 80% Training
- 10% Validation
- 10% Test

---

## What is SMILES?

**SMILES (Simplified Molecular Input Line Entry System)** represents chemical molecules as text strings.

Examples:

| Molecule | SMILES |
|---|---|
| Aspirin | CC(=O)Oc1ccccc1C(=O)O |
| Water | O |
| Ethanol | CCO |
| Caffeine | Cn1cnc2c1c(=O)n(c(=O)n2C)C |

SMILES allows chemical structures to be processed using **Natural Language Processing models** such as BERT.

---

## Model: ChemBERTa

This project uses **ChemBERTa**, a transformer model based on RoBERTa and trained specifically on chemical data.

Model details:

| Component | Detail |
|---|---|
| Base architecture | RoBERTa |
| Pretraining dataset | ZINC database (~77M molecules) |
| Transformer layers | 6 |
| Hidden size | 768 |
| Attention heads | 12 |
| Vocabulary size | 767 tokens |
| Max sequence length | 128 |
| Parameters | ~84 million |

Instead of manually engineering chemical descriptors, the model **learns molecular patterns directly from SMILES strings**.

---

## Project Pipeline

### 1. Data Loading
Dataset is loaded using **pandas**.

### 2. Label Encoding
BBB labels are converted to numeric format.

BBB+ → 1  
BBB- → 0

### 3. SMILES Validation
Each SMILES string is validated and canonicalized using **RDKit**.

Invalid molecules are removed.

### 4. Train / Validation / Test Split
Performed using **scikit-learn stratified splitting**.

- 80% Training
- 10% Validation
- 10% Test

### 5. Tokenization
SMILES strings are tokenized using the **ChemBERTa tokenizer** with:

- max length = 128
- padding
- truncation

### 6. DataLoader Creation
PyTorch DataLoaders are used for batching.

Batch size = 16

---

## Training Configuration

The pretrained ChemBERTa model is **fine-tuned** on the BBB classification task.

Training hyperparameters:

| Parameter | Value |
|---|---|
| Optimizer | AdamW |
| Learning rate | 2e-5 |
| Epochs | 5 |
| Batch size | 16 |
| Max sequence length | 128 |
| Weight decay | 0.01 |
| Scheduler | Linear warmup |
| Hardware | Google Colab T4 GPU |

---

## Training Results

| Epoch | Loss | Validation Accuracy | F1 Score |
|---|---|---|---|
| 1 | 0.4545 | 85.38% | 0.887 |
| 2 | 0.3110 | 87.05% | 0.902 |
| 3 | 0.2463 | **88.97%** | **0.916** |
| 4 | 0.1899 | 88.46% | 0.913 |
| 5 | 0.1487 | 88.33% | 0.910 |

Best performance occurred at **Epoch 3**, after which slight overfitting began.

---

## Final Model Performance

| Metric | Score |
|---|---|
| Test Accuracy | **88%** |
| F1 Score | **0.91** |
| ROC-AUC | **0.857** |
| BBB+ Recall | **93%** |
| BBB- Recall | **79%** |

The model performs within the **85–92% accuracy range reported in published research on B3DB datasets**.

---

## Tech Stack

| Tool | Purpose |
|---|---|
| Python | Programming language |
| PyTorch | Deep learning framework |
| HuggingFace Transformers | ChemBERTa model and tokenizer |
| RDKit | SMILES validation |
| scikit-learn | Dataset splitting and evaluation |
| pandas | Data processing |
| matplotlib / seaborn | Visualization |
| Google Colab | GPU training |

---

## Limitations

### Class Imbalance
BBB+ molecules outnumber BBB- molecules in the dataset.

BBB+ = 4,956  
BBB- = 2,851

This can bias the model toward predicting BBB+.

### Limited Explainability
Transformer models provide high accuracy but are harder to interpret than traditional ML models.

---

## Future Improvements

Planned improvements include:

1. Weighted loss function to address class imbalance
2. Early stopping to automatically select the best training epoch
3. SHAP explainability for identifying important molecular substructures
4. Inference pipeline for predicting new SMILES molecules
5. Attention visualization with BertViz


---

## Key Takeaway
Fine-tuned **ChemBERTa (pretrained on ~77 million molecules)** on BBB-labelled compounds to predict Blood-Brain Barrier permeability using only raw SMILES strings, achieving **88% test accuracy** without manual feature engineering.

