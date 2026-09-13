# Week 5 Demo Script

**Target duration: about 2 minutes 30 seconds**

## Opening

Hi, this is my Week 5 project: a fine-tuned Qwen3 1.7B support-ticket router. It reads an IT ticket, predicts one of seven queue labels, and routes it; it does not write a customer response.

## Dataset Preparation

I prepared a labeled CSV, created a stratified 80/20 split, converted the training rows to ShareGPT format, and registered the dataset with LLaMA Factory. After reviewing the first results, I corrected labels and added examples for Active Directory and Support general. The final dataset has 662 rows: 529 for training and 133 held out for validation.

## Fine-Tuning

I trained with LoRA through LLaMA Board on a Tesla T4. The base model stayed frozen while a small adapter learned the routing task. I completed two iterations. In the latest run, loss fell from 7.4624 to 0.2958 and leveled off, indicating convergence.

The second run changed the learning rate from 5e-5 to 2e-5, increased LoRA rank from 8 to 16, added 0.05 LoRA dropout, reduced cutoff length from 2048 to 512, added a 0.05 warmup ratio, and changed compute type from bf16 to fp16 for the T4 GPU. Epochs, batch size, gradient accumulation, max gradient norm, and cosine scheduling stayed the same.

After training, I merged the adapter into the base model and ran smoke tests for account creation, shared-folder access, Outlook, server retirement, and software installation.

I also corrected the evaluation code. Reports, F1 charts, and confusion-matrix rows and columns now use one explicit category order. Safe normalization handles empty classes, preventing metrics from appearing under the wrong names.

## Evaluation

The base model scored 31.6% accuracy. The fine-tuned model scored 70.7%, an improvement of 39.1 percentage points. Macro F1 was 0.710. EOL was strongest at 91.7% recall; Active Directory reached 72.2% and Fileservice 77.8%. Computer-Services was weakest at 44.4% recall.

## Closing

Overall, fine-tuning was successful and substantially outperformed the base model. Next, I would evaluate both iterations on the same fixed test set and add clearer Computer-Services and Software examples.

Thank you.
