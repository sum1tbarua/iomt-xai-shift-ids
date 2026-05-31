# Behavior-aware explainable AI for IoMT intrusion detection under attack-family and cross-dataset shift

This repository contains the experimental notebooks for a behavior-aware and shift-sensitive explainable AI study of Internet of Medical Things (IoMT) intrusion detection. The project evaluates whether high internal intrusion-detection accuracy remains reliable when models are tested under feature reduction, unseen attack-family shift, and external cross-dataset validation.

The study uses CICIoMT2024 WiFi/MQTT traffic as the primary dataset and WUSTL-EHMS-2020 as an external healthcare-security validation dataset.

---

## Project Overview
Many AI-based intrusion-detection studies report high internal accuracy on a single dataset. However, IoMT security systems deployed in real healthcare networks must also be explainable, robust to unseen attack families, and transferable across different network environments.

This project investigates those issues through:

- controlled internal evaluation on balanced CICIoMT2024 splits,
- baseline comparison using Logistic Regression, Random Forest, and XGBoost,
- feature-reduction analysis using Top-20, Top-10, and Top-5 feature subsets,
- SHAP-based feature-level explanation,
- behavior-group contribution analysis,
- cross-attack-family shift evaluation,
- family-balanced unseen attack sensitivity testing,
- harmonized external validation using WUSTL-EHMS,
- cross-dataset feature-correlation and SHAP-rank analysis.

The central finding is that strong internal performance alone is not sufficient evidence of IoMT IDS reliability. Models can perform extremely well on internal balanced splits while failing under unseen attack families or external dataset shift.


---

## Repository Structure

```text
├── README.md
├── notebooks/
│   ├── 01_full_dataset_experiment.ipynb
│   ├── 02_feature_reduction_experiment.ipynb
│   ├── 03_shap_full_and_top20_analysis.ipynb
│   ├── 04_behavior_group_contribution_analysis.ipynb
│   ├── 05_attack_family_shift_experiment.ipynb
│   ├── 06_family_balanced_shift_sensitivity.ipynb
│   ├── 07_wustl_external_validation.ipynb
│   └── 08_cross_dataset_correlation_and_shap_rank.ipynb
```

---

## Notebook Descriptions

| Notebook | Purpose |
|---|---|
| `01_full_dataset_experiment.ipynb` | Trains and evaluates Logistic Regression, Random Forest, and XGBoost on balanced CICIoMT2024 train/validation/test splits. |
| `02_feature_reduction_experiment.ipynb` | Evaluates XGBoost using all 45 features and reduced Top-20, Top-10, and Top-5 feature subsets. |
| `03_shap_full_and_top20_analysis.ipynb` | Computes SHAP explanations for the full 45-feature XGBoost model and the Top-20 XGBoost model. |
| `04_behavior_group_contribution_analysis.ipynb` | Aggregates SHAP values into interpretable network-flow behavior groups. |
| `05_attack_family_shift_experiment.ipynb` | Trains on seen DDoS/DoS attack families and evaluates on unseen Recon, ARP, and Malformed attack families. |
| `06_family_balanced_shift_sensitivity.ipynb` | Performs a family-balanced unseen attack-family sensitivity test to control for unseen-family imbalance. |
| `07_wustl_external_validation.ipynb` | Performs harmonized external validation using WUSTL-EHMS and evaluates CICIoMT-to-WUSTL transfer. |
| `08_cross_dataset_correlation_and_shap_rank.ipynb` | Compares CICIoMT2024 and WUSTL-EHMS using Spearman feature correlation and SHAP-rank analysis. |

---

## Datasets

This project uses two public healthcare-network security datasets.

### CICIoMT2024

CICIoMT2024 is used as the primary IoMT intrusion-detection dataset. The experiments focus on WiFi/MQTT traffic and binary classification between benign and attack traffic.

Raw dataset scale used in the study:

- Raw training records: `n = 7,160,831`
- Raw testing records: `n = 1,614,182`

Balanced internal splits were constructed from the original train/test partitions to reduce class-distribution bias.

### WUSTL-EHMS-2020

WUSTL-EHMS-2020 is used as an external healthcare-security validation dataset.

Dataset scale:

- Total records: `n = 16,318`
- Normal: `14,272`
- Attack: `2,046`

Because CICIoMT2024 and WUSTL-EHMS use different raw feature schemas, a 10-feature harmonized network-flow representation was created for cross-dataset evaluation.

---

## Data Availability Notice

Raw datasets are not included in this repository due to file size and dataset redistribution considerations. Users should download CICIoMT2024 and WUSTL-EHMS-2020 from their official sources and cite the original dataset papers.

If processed balanced datasets are included in this repository, they are provided only for reproducibility of the experiments in this project. Users should still cite the original datasets when using these files.

---

## Key Experimental Components

### 1. Internal CICIoMT2024 Evaluation

The first experiment compares Logistic Regression, Random Forest, and XGBoost on balanced CICIoMT2024 splits.

The balanced internal datasets contain:

| Split | Samples | Benign | Attack |
|---|---:|---:|---:|
| Training | 300,000 | 150,000 | 150,000 |
| Validation | 80,000 | 40,000 | 40,000 |
| Testing | 75,214 | 37,607 | 37,607 |

XGBoost achieved the strongest internal performance and was used for the feature-reduction and explainability experiments.

---

### 2. Feature-Reduction Analysis

The full XGBoost model was trained using all 45 CICIoMT2024 features. Built-in XGBoost feature importance was then used to create reduced feature subsets:

- Top-20 features,
- Top-10 features,
- Top-5 features.

The Top-20 feature model preserved near-equivalent internal performance while reducing feature complexity.

---

### 3. SHAP-Based Explainability

SHAP was used to interpret XGBoost predictions at the feature level.

The analysis showed that features such as:

- `IAT`,
- `Magnitue`,
- `rst_count`,
- `Tot size`,
- `AVG`,
- `Rate`

were among the most important contributors in the full model.

The full XGBoost model and the Top-20 XGBoost model shared 8 of their Top-10 SHAP features, producing a Top-10 SHAP overlap score of `0.80`.

---

### 4. Behavior-Group Contribution Analysis

To move beyond isolated feature ranking, network-flow features were grouped into interpretable behavior categories:

- timing/rate behavior,
- packet-size behavior,
- TCP flag/count behavior,
- protocol behavior,
- statistical-flow behavior.

This allowed the project to quantify which types of network behavior contributed most strongly to intrusion-detection decisions.

---

### 5. Cross-Attack-Family Shift Evaluation

A shift experiment was conducted to test whether a model trained on seen attack families generalizes to unseen attacks.

Training and validation attacks:

- DDoS,
- DoS.

Unseen test attacks:

- Recon,
- ARP spoofing,
- Malformed MQTT.

The model showed strong performance on seen attack families but substantially weaker recall under unseen attack-family shift. Family-wise recall revealed that the model partially detected Recon attacks but failed to detect ARP and Malformed attacks.

A family-balanced sensitivity test was also performed using equal samples from Recon, ARP, and Malformed attacks. This confirmed that aggregate binary metrics can hide complete failure on minority unseen attack families.

---

### 6. External Validation on WUSTL-EHMS

External validation was performed using WUSTL-EHMS through a harmonized 10-feature network-flow representation.

The harmonized features were:

- `duration`,
- `rate`,
- `total_bytes`,
- `total_packets`,
- `avg_packet_size`,
- `max_packet_size`,
- `min_packet_size`,
- `inter_packet_time`,
- `src_load`,
- `dst_load`.

The direct CICIoMT-to-WUSTL transfer experiment revealed severe cross-dataset distribution mismatch. WUSTL in-domain training showed that the harmonized features were still predictive within WUSTL, but explanation patterns differed from CICIoMT2024.

---

### 7. Cross-Dataset Correlation and SHAP-Rank Analysis

To evaluate explanation stability across datasets, the project compares CICIoMT2024 and WUSTL-EHMS using:

- Spearman feature-correlation analysis,
- SHAP feature-importance ranking,
- SHAP-rank correlation.

The SHAP-rank comparison showed weak agreement between CICIoMT2024 and WUSTL-EHMS feature-importance rankings, suggesting that harmonized feature names alone do not guarantee stable explanatory behavior across datasets.

---

## Main Takeaway

This project demonstrates that high internal IoMT intrusion-detection accuracy is not sufficient evidence of deployment readiness. A reliable AI-based IoMT IDS should also be evaluated for:

- feature-reduction robustness,
- explanation stability,
- behavior-level contribution,
- unseen attack-family generalization,
- cross-dataset transferability,
- external validation.

The project provides a reproducible experimental workflow for evaluating IoMT intrusion-detection models under more realistic shift-aware conditions.

---

## Environment

The notebooks were developed using Google Colab and Python.

Commonly used libraries include:

```text
pandas
numpy
scikit-learn
xgboost
shap
matplotlib
```

Install the required packages manually in Colab if needed:

```python
!pip install xgboost shap
```

---

## How to Run

1. Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY_NAME.git
cd YOUR_REPOSITORY_NAME
```

2. Download the required datasets from their official sources.

3. Place the raw or processed data files in the expected local or Google Drive directory.

4. Run the notebooks in order:

```text
01 → 02 → 03 → 04 → 05 → 06 → 07 → 08
```

Each notebook corresponds to a specific experiment and can also be reviewed independently.

---

## Manuscript Status

This repository supports the experimental workflow for the manuscript:

**Behavior-Aware Explainable AI for IoMT Intrusion Detection Under Cross-Attack-Family and Cross-Dataset Shift**

Manuscript status: submitted.

---

## Citation

If you use this repository, please cite the original datasets and this project repository. A formal citation will be added if the manuscript is accepted.

```bibtex
@misc{barua2026iomt_xai_shift_ids,
  author       = {Barua, Sumit},
  title        = {Behavior-Aware Explainable AI for IoMT Intrusion Detection Under Cross-Attack-Family and Cross-Dataset Shift},
  year         = {2026},
  howpublished = {GitHub repository},
  note         = {Available at: https://github.com/YOUR_USERNAME/YOUR_REPOSITORY_NAME}
}
```

---

## Author

**Sumit Barua**  
Department of Computer Science  
Western Michigan University  
Kalamazoo, MI, USA  

---

## Disclaimer

This repository is intended for academic research and reproducibility. The models and experiments are not intended for direct clinical or operational deployment without further validation, calibration, and security testing.