# MSK-AttProm-HN

**MSK-AttProm-HN** is a bioinformatics machine learning project for robust bacterial promoter prediction and sigma-type classification using multi-scale k-mer features and attention-based fusion.

Full project title:

**Multi-Scale K-mer Attention with Hard-Negative Evaluation for Robust Bacterial Promoter and Sigma-Type Prediction**

---

## Project Overview

This project focuses on bacterial promoter prediction using the **iPromoter-2L benchmark dataset**.

The system performs two main tasks:

1. **Binary promoter prediction**
   - Promoter
   - Non-promoter

2. **Sigma-type classification**
   - sigma24
   - sigma28
   - sigma32
   - sigma38
   - sigma54
   - sigma70

The main idea is to extract **3-mer, 4-mer, and 5-mer** features from DNA sequences and process them using a multi-branch neural model with learnable attention fusion.

---

## Motivation

Promoter prediction is important because promoters control transcription initiation and gene regulation.

However, promoter prediction is challenging because:

- Promoter patterns are short and variable.
- Different sigma factors recognize different promoter types.
- Sigma-type classes are highly imbalanced.
- Random non-promoter samples may be too easy due to GC-content differences.

To address this, this project introduces a **hard-negative evaluation strategy**, where non-promoter sequences are selected to be similar to promoter sequences based on GC-content and k-mer composition.

---

## Dataset

The project uses the **iPromoter-2L benchmark dataset**.

Dataset files:

~~~text
nonPromoter.fasta
sigma24.fasta
sigma28.fasta
sigma32.fasta
sigma38.fasta
sigma54.fasta
sigma70.fasta
~~~

Each DNA sequence has a fixed length of **81 bp**.

### Dataset Distribution

| Class | Count |
|---|---:|
| nonPromoter | 2860 |
| sigma24 | 484 |
| sigma28 | 134 |
| sigma32 | 291 |
| sigma38 | 163 |
| sigma54 | 94 |
| sigma70 | 1694 |
| **Total** | **5720** |

For binary classification:

| Type | Count | Label |
|---|---:|---:|
| Non-promoter | 2860 | 0 |
| Promoter | 2860 | 1 |

---

## Feature Extraction

Each DNA sequence is converted into normalized k-mer frequency features.

Used k-mer scales:

| Feature | Dimension |
|---|---:|
| 3-mer | 64 |
| 4-mer | 256 |
| 5-mer | 1024 |
| Concatenated | 1344 |

For baseline machine learning models, all features are concatenated into a single 1344-dimensional vector.

For the proposed model, 3-mer, 4-mer, and 5-mer features are kept as separate branches.

---

## Proposed Model

The proposed model is called **MSK-AttProm-HN**.

It consists of:

- 3-mer feature branch
- 4-mer feature branch
- 5-mer feature branch
- Learnable attention/gating fusion
- Classifier head

Basic architecture:

~~~text
3-mer features -> 3-mer branch -> h3
4-mer features -> 4-mer branch -> h4
5-mer features -> 5-mer branch -> h5
                              |
                              v
                  Attention / Gating Fusion
                              |
                              v
                    Fused Representation
                              |
                              v
                      Final Prediction
~~~

The model learns how much importance should be given to each k-mer scale.

---

## Baseline Models

The following baseline models are implemented:

- Logistic Regression
- Support Vector Machine
- Random Forest
- XGBoost
- MLP without Attention

These baselines are used to compare the proposed attention-based model with traditional machine learning and simple neural network approaches.

---

## Hard-Negative Evaluation

A major part of this project is hard-negative robustness evaluation.

Instead of testing only with random non-promoters, this project creates hard negatives using:

- GC-content similarity
- k-mer cosine similarity

Two test settings are compared:

1. **Normal balanced test**
   - Promoters + random non-promoters

2. **Hard-negative test**
   - Promoters + GC/k-mer matched non-promoters

Robustness is measured using F1-score drop:

~~~text
F1 drop = Normal F1 - Hard-negative F1
~~~

Lower F1 drop means better robustness.

---

## Main Results

### Binary Promoter Prediction

| Metric | Value |
|---|---:|
| Accuracy | 0.8103 |
| Precision | 0.8233 |
| Recall | 0.7902 |
| F1-score | 0.8064 |
| MCC | 0.6211 |
| ROC-AUC | 0.8784 |

### Sigma-Type Prediction

| Metric | Value |
|---|---:|
| Accuracy | 0.4633 |
| Macro-F1 | 0.3106 |
| Weighted-F1 | 0.4811 |
| MCC | 0.1716 |
| Macro ROC-AUC | 0.6643 |

---

## Key Findings

- Multi-scale k-mer features are useful for bacterial promoter prediction.
- 5-mer features received the highest attention weight.
- Standard random-negative evaluation may overestimate model robustness.
- Hard-negative testing provides a more challenging evaluation setting.
- MSK-AttProm-HN achieved the smallest F1 degradation under hard-negative evaluation.
- Promoter-supporting k-mers were mostly A/T-rich.
- Non-promoter-supporting k-mers were mostly GC-rich.

---

## Repository Structure


~~~text
MSK-AttProm-HN/
|
├── data/
│   ├── nonPromoter.fasta
│   ├── sigma24.fasta
│   ├── sigma28.fasta
│   ├── sigma32.fasta
│   ├── sigma38.fasta
│   ├── sigma54.fasta
│   └── sigma70.fasta
|
├── notebooks/
│   └── MSK_AttProm_HN.ipynb
|
├── outputs/
│   ├── figures/
│   ├── tables/
│   └── models/
├── README.md
~~~

---

## Requirements

Main Python libraries:

~~~text
numpy
pandas
matplotlib
seaborn
scikit-learn
xgboost
torch
tqdm
~~~

Install dependencies:

~~~bash
pip install -r requirements.txt
~~~

---

## How to Run

1. Clone the repository.

~~~bash
git clone https://github.com/Rafsun-Islam/MSK-AttProm.git
cd MSK-AttProm-HN
~~~

2. Place the iPromoter-2L FASTA files inside the `data/` folder.

3. Open the notebook:

~~~text
notebooks/MSK_AttProm_HN.ipynb
~~~

4. Run all cells step by step.

5. Generated results will be saved in the `outputs/` folder.

---

## Outputs

The notebook generates:

- Baseline model results
- Proposed model results
- Confusion matrices
- Training curves
- Hard-negative evaluation tables
- Ablation study results
- Attention weight analysis
- k-mer importance tables
- Result plots for report/paper

---

## Limitations

- The dataset is relatively small.
- Sigma-type classes are imbalanced.
- Hard negatives are computationally selected, not biologically validated.
- k-mer frequency features do not directly capture positional promoter motifs.

---

## Future Work

Possible future improvements:

- Use larger bacterial promoter datasets.
- Add position-aware k-mer features.
- Try CNN or Transformer-based sequence models.
- Improve sigma-type classification using data augmentation or focal loss.
- Perform cross-species validation.

---

## Authors

- Rafsun Islam Taskin

Department of Computer Science and Engineering, East West University

---

## References

1. Liu, B., Yang, F., Huang, D. S., & Chou, K. C.  
   *iPromoter-2L: a two-layer predictor for identifying promoters and their types by multi-window-based PseKNC.*  
   Bioinformatics, 2018.

2. Tahir, M., Hayat, M., & Chong, K. T.  
   *iPromoter-BnCNN: a novel branched CNN-based predictor for identifying and classifying sigma promoters.*  
   Bioinformatics, 2020.

3. Chevez-Guardado, R., & Peña-Castillo, L.  
   *Promotech: a general tool for bacterial promoter recognition.*  
   Genome Biology, 2021.
