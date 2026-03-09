# SinoNOM-BERT

SinoNOM-BERT is a project developed for the **Introduction to Natural Language Processing** course.  
The goal of this project is to explore the application of **BERT-based language models for Sino-Nom (Hán Nôm) text processing**.

Sino-Nom is a historical writing system used in Vietnam, consisting mainly of **Chinese characters and locally created characters**. Due to the limited availability of digital resources and NLP tools for this script, building language models for Sino-Nom remains a challenging research problem.

This project investigates how **pretrained language models for Ancient Chinese** can be adapted and evaluated on **Sino-Nom texts**, with a focus on both **prose and poetry datasets**.

---

# Project Overview

The overall workflow of the project includes the following steps:

1. **Data Preparation**

   A Sino-Nom corpus was collected and processed to build training and evaluation datasets.  
   The data was organized into different subsets for training and testing.

2. **Model Training**

   Instead of training a model from scratch, we fine-tuned the pretrained model:

   - `Jihuai/bert-ancient-chinese`

   This model was selected because of the linguistic similarity between **Classical Chinese** and **Sino-Nom texts**.

3. **Training Strategies**

   We experimented with three different training strategies:

   - **WWM (Whole Word Masking)**  
     Masking entire words during training to improve contextual understanding.

   - **CLM (Character-Level Masking)**  
     Masking individual characters to better capture fine-grained character-level information.

   - **Hybrid (WWM + CLM)**  
     A combination of both approaches to leverage the advantages of each method.

4. **Evaluation**

   The models were evaluated on different tasks to analyze their performance on Sino-Nom language understanding.

   The evaluation dataset was divided into two main categories:

   - **Prose (Văn xuôi)**
   - **Poetry (Thơ ca)**

---

# Experimental Results

## Prose Evaluation

For prose text, the models were evaluated using **precision metrics**:

- **P@1** – Precision at top 1 prediction  
- **P@10** – Precision at top 10 predictions

The results show that different training strategies perform differently depending on the task and token length.

## Poetry Evaluation

For poetry datasets, we evaluated the models using:

- **Matching Accuracy** – ability to correctly predict matching characters
- **Tonal Accuracy** – ability to preserve tonal patterns in poetry

| Method | Matching Accuracy (%) | Tonal Accuracy (%) |
|------|------|------|
| WWM | 43.30 | 83.19 |
| CLM | **43.58** | 81.19 |
| Hybrid | 40.50 | **86.94** |

The results indicate that:

- **CLM** achieves the best **matching accuracy**
- **Hybrid** achieves the best **tonal accuracy**, which is particularly important for poetry structure

---

# Repository Structure
---
SinoNOM-BERT
│
├── Data/ # Sino-Nom dataset and preprocessing
├── Train_Test/ # Training and test dataset splits
├── Training/ # Training scripts and model configurations
├── Eval/ # Evaluation scripts and results
├── README.md
├── LICENSE
└── .gitignore

---

# Technologies Used

- **Python**
- **PyTorch**
- **HuggingFace Transformers**
- Pretrained model: `Jihuai/bert-ancient-chinese`

---

# Future Work

Possible future improvements include:

- Expanding the Sino-Nom corpus
- Training a **Sino-Nom specific tokenizer**
- Pretraining a **BERT model directly on Sino-Nom texts**
- Applying the model to downstream tasks such as:
  - Named Entity Recognition
  - Text classification
  - OCR post-correction for historical documents

---

# License

This project is released under the **MIT License**.
