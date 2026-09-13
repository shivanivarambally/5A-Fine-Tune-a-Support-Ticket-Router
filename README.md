# Fine-Tune a Support Ticket Router

Week 5 project from the Mastering Agentic AI course. This project fine-tunes `Qwen/Qwen3-1.7B-Base` into a lightweight IT support-ticket router using LoRA and LLaMA Factory.

## What It Does

The model reads a support ticket and predicts one routing label:

- `Active Directory`
- `Computer-Services`
- `EOL`
- `Fileservice`
- `O365`
- `Software`
- `Support general`

The model predicts a queue label. It does not write a customer response.

## Repository Contents

- `Finetune_Support_Ticket_Classifier_Qwen3.ipynb`: Full Colab workflow.
- `support_tickets.csv`: Refined labeled dataset.
- `demo_results.html`: Presentation-style demo with real notebook screenshots, slide navigation, and speaker notes.
- `demo_script.md`: Demo narration designed for less than three minutes.
- `fine_tuning_report.md`: Detailed project report with metrics, parameter changes, and conclusions.
- `demo_assets/`: Notebook screenshots from the two training iterations.

## Notebook Workflow

1. Install LLaMA Factory in a Colab Tesla T4 runtime.
2. Upload `support_tickets.csv` and create a stratified 80/20 split.
3. Register the training data as `support_tickets`.
4. Train `Qwen/Qwen3-1.7B-Base` with LoRA through LLaMA Board.
5. Review the training loss curve.
6. Merge the LoRA adapter into the base model.
7. Run smoke tests and validation inference.
8. Compare the base and fine-tuned models using classification metrics and a confusion matrix.

## Dataset

The final dataset contains 662 labeled tickets:

- 529 training rows
- 133 held-out validation rows

The dataset was refined after reviewing the first run. Clear mislabeled rows were corrected, and examples were added for weak or easily confused categories, especially `Active Directory` and `Support general`. Additional contrast examples were added for O365, Software, and Computer-Services.

The CSV was checked for blank ticket text, duplicate rows, and unknown labels.

## Training Configuration

The second iteration used these settings:

| Parameter | Value |
|---|---:|
| Learning rate | `2e-5` |
| Epochs | `3` |
| LoRA rank | `16` |
| LoRA dropout | `0.05` |
| Batch size | `2` |
| Gradient accumulation | `8` |
| Cutoff length | `512` |
| Max gradient norm | `1.0` |
| Scheduler | `cosine` |
| Warmup ratio | `0.05` |
| Compute type | `fp16` |

The first iteration used learning rate `5e-5`, LoRA rank `8`, no LoRA dropout, cutoff length `2048`, no warmup ratio, and `bf16` compute type. Epochs, batch size, gradient accumulation, max gradient norm, and cosine scheduling stayed the same.

## Results

| Measure | Iteration 1 | Iteration 2 |
|---|---:|---:|
| Dataset size | 585 rows | 662 rows |
| Validation size | 117 | 133 |
| Base accuracy | 24.8% | 31.6% |
| Fine-tuned accuracy | 72.6% | 70.7% |
| Gain over base | +47.8 points | +39.1 points |
| Macro F1 | 0.679 | 0.710 |
| Training loss | 7.1462 to 0.1880 | 7.4624 to 0.2958 |

The latest model reached **70.7% validation accuracy**, compared with **31.6% for the base model**, a gain of **39.1 percentage points**.

Latest per-category recall:

- EOL: **91.7%**
- Fileservice: **77.8%**
- Active Directory: **72.2%**
- Software: **58.3%**
- Computer-Services: **44.4%**

The validation split changed between iterations, so the two fine-tuned accuracy scores are not a perfectly controlled head-to-head comparison. The second iteration had the stronger macro F1, indicating more balanced category performance.

## Evaluation Code Fixes

The notebook evaluation code was corrected after the first report displayed class metrics under the wrong names.

- Classification reports now pass both `labels=LABEL_TOKENS` and `target_names=LABEL_TOKENS`.
- The confusion matrix uses the same explicit label order for rows and columns.
- Confusion-matrix normalization safely handles empty classes.
- The comparison chart uses the explicit label order when calculating per-class F1.

## Demo and Report

Open `demo_results.html` locally in a browser for the presentation deck. It includes:

- Previous and Next slide controls
- Left-side speaker notes
- Keyboard arrow navigation
- Separate readable screens for loss, baseline comparison, and confusion matrices
- Actual screenshots from both notebook runs

The detailed written report is in `fine_tuning_report.md`, and the short narration is in `demo_script.md`.

## Requirements

The notebook is designed for Google Colab with a Tesla T4 GPU. Training uses LLaMA Factory, Transformers, PEFT, PyTorch, pandas, scikit-learn, Matplotlib, and Seaborn.

## Conclusion

Fine-tuning was successful: both runs substantially outperformed their corresponding base models. The latest model is a useful prototype, with `Computer-Services` as the main remaining weakness. A fair final comparison would evaluate both saved models on one fixed test set.
