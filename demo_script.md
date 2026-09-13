# Week 5 Demo Script

**Target duration: about 2 minutes 30 seconds**

## Opening

Hi, this is my Week 5 project: a fine-tuned support-ticket router.

The goal was to take the Qwen3 1.7B base model and teach it to route IT support tickets into one of seven queues: Active Directory, Computer-Services, EOL, Fileservice, O365, Software, or Support general.

The model does not write a response. It reads the ticket, predicts one label, and sends that label to the appropriate support queue.

## Dataset Preparation

I started with a labeled CSV of support tickets. The notebook creates a stratified 80/20 train and validation split, converts the training data into ShareGPT format, and registers the dataset with LLaMA Factory.

After reviewing the first results, I corrected ambiguous labels and added examples for weak categories, especially Active Directory and Support general. The final dataset contains 662 rows, with 529 training examples and 133 held-out validation examples.

## Fine-Tuning

I trained Qwen3 using LoRA through the LLaMA Board interface on a Tesla T4 GPU. LoRA keeps the original model frozen and learns a small adapter containing the task-specific routing information.

I completed two fine-tuning iterations. In the latest run, the training loss decreased from 7.4624 to 0.2958 and then leveled off, which indicates that training converged.

After training, I merged the adapter into the base model and ran smoke tests on clear examples such as account creation, shared-folder access, Outlook problems, server retirement, and software installation.

## Evaluation

The latest base model scored 31.6% accuracy on the validation set. The fine-tuned model scored 70.7%, which is an improvement of 39.1 percentage points.

The latest macro F1 score was 0.710. EOL was the strongest category with 91.7% recall. Active Directory reached 72.2% recall, and Fileservice reached 77.8% recall.

The main remaining weakness is Computer-Services, with 44.4% recall. Some device-related tickets were still routed to Support general.

## Closing

Overall, fine-tuning was successful: the model learned the routing task and substantially outperformed the base model. The next improvement would be to evaluate both iterations on the same fixed test set and add more clearly labeled Computer-Services and Software examples.

Thank you.
