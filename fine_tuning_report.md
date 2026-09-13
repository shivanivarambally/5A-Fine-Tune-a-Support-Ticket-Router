# Support Ticket Router Fine-Tuning Report

## Executive Summary

Fine-tuning was successful. The latest model reached **70.7% validation accuracy**, compared with **31.6% for the base model**, for a gain of **39.1 percentage points**.

The second run had a slightly lower accuracy than the first run, but a better macro F1, indicating more balanced performance across routing categories.

## Project Workflow

1. Prepared the labeled support-ticket dataset.
2. Created a stratified 80/20 train/validation split.
3. Converted training examples to ShareGPT JSON format.
4. Fine-tuned `Qwen/Qwen3-1.7B-Base` using LoRA through LLaMA Factory.
5. Reviewed the training loss curve.
6. Merged the adapter into the base model.
7. Ran smoke tests and validation inference.
8. Compared the base model with the fine-tuned model.

## Iteration Comparison

| Measure | Iteration 1 | Iteration 2 |
|---|---:|---:|
| Dataset size | 585 rows | 662 rows |
| Validation size | 117 tickets | 133 tickets |
| Base accuracy | 24.8% | 31.6% |
| Fine-tuned accuracy | 72.6% | 70.7% |
| Gain over base | +47.8 points | +39.1 points |
| Macro F1 | 0.679 | 0.710 |
| Training loss | 7.1462 to 0.1880 | 7.4624 to 0.2958 |

The validation sets changed between iterations, so the fine-tuned accuracy values are not a perfectly controlled head-to-head comparison.

The first iteration's per-class report also displayed category names in an incorrect order. The latest notebook fixes this by passing the explicit label order to every classification report and comparison chart.

## Iteration 1 Results

```text
Starting loss : 7.1462
Final loss    : 0.1880
Total drop    : 6.9582

Validation samples: 117
Base accuracy:       24.8%
Fine-tuned accuracy: 72.6%
Gain over base:     +47.8 percentage points
Macro F1:             0.679
```

## Iteration 2 Results

### Corrected Classification Report

| Category | Precision | Recall | F1 | Support |
|---|---:|---:|---:|---:|
| Support general | 0.600 | 0.667 | 0.632 | 36 |
| Fileservice | 0.750 | 0.778 | 0.764 | 27 |
| O365 | 0.700 | 0.737 | 0.718 | 19 |
| EOL | 0.917 | 0.917 | 0.917 | 12 |
| Software | 0.778 | 0.583 | 0.667 | 12 |
| Active Directory | 0.684 | 0.722 | 0.703 | 18 |
| Computer-Services | 0.800 | 0.444 | 0.571 | 9 |
| **Accuracy** |  |  | **0.707** | **133** |
| **Macro average** | **0.747** | **0.693** | **0.710** | **133** |
| **Weighted average** | **0.714** | **0.707** | **0.705** | **133** |

### Training Loss

```text
Starting loss : 7.4624
Final loss    : 0.2958
Total drop    : 7.1665

Loss is low — model has likely converged well.
```

### Confusion Matrix

Rows are true labels and columns are predicted labels.

| True \\ Predicted | Support general | Fileservice | O365 | EOL | Software | Active Directory | Computer-Services |
|---|---:|---:|---:|---:|---:|---:|---:|
| Support general | **24** | 3 | 5 | 1 | 1 | 2 | 0 |
| Fileservice | 2 | **21** | 0 | 0 | 1 | 3 | 0 |
| O365 | 2 | 3 | **14** | 0 | 0 | 0 | 0 |
| EOL | 0 | 0 | 0 | **11** | 0 | 0 | 1 |
| Software | 3 | 0 | 1 | 0 | **7** | 1 | 0 |
| Active Directory | 5 | 0 | 0 | 0 | 0 | **13** | 0 |
| Computer-Services | 4 | 1 | 0 | 0 | 0 | 0 | **4** |

Important recall values:

- EOL: **91.7%**
- Fileservice: **77.8%**
- Active Directory: **72.2%**
- Software: **58.3%**
- Computer-Services: **44.4%**

### Baseline Comparison

```text
Base accuracy  : 31.6%
Fine-tuned acc : 70.7%
Delta          : +39.1%
```

## Conclusion

- Both fine-tuning iterations clearly outperformed their corresponding base models.
- The first run had higher measured accuracy, but it used a different validation split.
- The second run had a stronger macro F1, suggesting more balanced category performance.
- The latest model is a successful prototype, but not yet reliable enough for fully autonomous ticket routing.
- The main remaining weakness is `Computer-Services`, especially device-related tickets being routed to `Support general`.

## Recommended Next Step

Freeze one stratified test split and evaluate both saved models on that same split. Continue improving the `Computer-Services` and `Software` examples, then compare the models using the same label order and metrics.

## Submission Artifacts

- Notebook: `Finetune_Support_Ticket_Classifier_Qwen3.ipynb`
- Dataset: `support_tickets.csv`
- HTML report: `fine_tuning_report.html`
- Markdown report: `fine_tuning_report.md`
