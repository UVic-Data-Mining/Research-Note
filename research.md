# Ranked Papers for Our Phishing Project

## Current Research Direction

Use **AI-generated phishing URLs to augment the training dataset**, then investigate whether this improves the detection of **new, real phishing URLs that were not seen during training**.

The basic idea is:

```text
Older real phishing URLs + AI-generated phishing URLs
        ↓
Train phishing detector
        ↓
Test on newer REAL phishing URLs
        ↓
Compare against detector trained only on real data
```

The important point is that the final test set should contain **real phishing URLs only**. This allows us to investigate whether AI-generated training data actually improves generalisation to new real-world phishing attacks.

# 1. PhreshPhish: A Real-World, High-Quality, Large-Scale Phishing Website Dataset and Benchmark

**Dalton et al. (2025/2026)**

## What It Does

PhreshPhish introduces a large and relatively modern phishing dataset.

The authors argue that many existing phishing-detection studies may obtain overly optimistic results because of issues such as:

- Data leakage
- Duplicate or highly similar URLs
- Unrealistic train/test splits
- Outdated phishing data
- Unrealistic class distributions

The dataset contains recent phishing samples and is designed to support more realistic evaluation of phishing-detection models.

## Why It Is Important for Us

This could potentially be our **main dataset**.

Our project requires something similar to:

```text
Older real URLs
        ↓
Training set

Newer real URLs
        ↓
Testing set
```

rather than randomly mixing all URLs before performing an 80/20 train-test split.

This is important because our main question is whether AI-generated training data helps the detector recognise **future or unseen real phishing URLs**.

## What We Need to Look For

When reading this paper, focus on:

- How is the dataset collected?
- Are raw URLs provided?
- Does each URL have a timestamp or collection date?
- How do they construct the training and testing sets?
- Do they use chronological splits?
- How do they prevent data leakage?
- How do they deal with duplicate or similar URLs?
- What URL features are available?
- What baseline classifiers do they use?
- What evaluation metrics do they use?

## How We Could Build on It

Use their realistic evaluation setup but introduce different training conditions:

```text
Real training data
        vs
Real + AI-generated training data
```

Both models would then be tested on the same newer, real phishing dataset.

---

# 2. Phishing URL Detection Generalisation Using Unsupervised Domain Adaptation

**Rashid et al. (2024)**

## What It Does

This paper investigates whether phishing detectors actually **generalise**.

A model may perform very well when its training and testing samples come from the same dataset but perform significantly worse when evaluated using phishing data collected from another source.

The authors investigate this distribution-shift problem and use domain adaptation to improve cross-dataset performance.

## Why It Is Important for Us

This paper establishes the **main problem that our project is trying to address**:

> A phishing detector performing well on its original dataset does not necessarily mean that it will perform well on new phishing URLs.

Their approach is:

```text
Domain adaptation
        ↓
Improve generalisation
```

Our approach would instead investigate:

```text
AI-generated training augmentation
        ↓
Improve generalisation?
```

Therefore, we are addressing a similar problem using a different approach.

## What We Need to Look For

Focus on:

- How do they define generalisation?
- Which datasets do they use?
- How do they perform cross-dataset testing?
- How much does performance decrease?
- Which classifiers do they use?
- Which features do they use?
- What evaluation metrics do they use?
- Why do they think models fail to generalise?
- How is their experiment structured?
- What limitations do they identify?

## How We Could Build on It

Instead of adapting the model after encountering another domain, investigate whether:

> Synthetic phishing examples during training can make the model more robust before it encounters new phishing data.

---

# 3. Phishing Webpage Detection Using Structured URL Generation

**Asiri & Alasmari (2026)**

## What It Does

This is probably the paper **closest to our original idea**.

The researchers generate additional phishing URLs and add them to the model's training data.

The general process is:

```text
Real phishing URLs
        ↓
Generative model
        ↓
Synthetic phishing URLs
        ↓
Real + synthetic training data
        ↓
Phishing detector
```

They investigate whether these generated URLs improve phishing-detection performance.

They also compare the learned generation method against simpler methods of generating additional samples.

## Why It Is Important for Us

This paper means that our research question cannot simply be:

> Does adding generated phishing URLs to training improve phishing detection?

Something very similar has already been investigated.

Our main difference would instead be:

> Does generated augmentation improve detection of **temporally newer, real phishing URLs that were not available during training?**

## What We Need to Look For

We should inspect this paper particularly carefully.

Focus on:

- How exactly do they generate URLs?
- What generative model do they use?
- How many synthetic URLs are generated?
- How do they validate generated URLs?
- Do they remove duplicates?
- What percentage of the training dataset becomes synthetic?
- What baseline do they compare against?
- Do they compare against simple augmentation?
- How do they split the dataset?
- Is the test set chronologically newer?
- Is the test set completely real?
- Could generated samples leak information from the test set?
- Which classifiers benefit from augmentation?
- How large is the improvement?
- What limitations do the authors mention?

## How We Could Build on It

Their question is approximately:

```text
Does structured generated data improve phishing detection?
```

Our question would be:

```text
Does AI-generated data improve GENERALISATION
to temporally newer real phishing URLs?
```

---

# 4. Knowledge-Grounded LLM-Driven Augmentation via Graph RAG for Phishing URL Detection

**Kim & Buu (2026)**

## What It Does

This work investigates using an **LLM to generate phishing URL training examples**.

Instead of simply asking an LLM to generate phishing URLs, they provide phishing-related knowledge to guide the generation process.

The approximate pipeline is:

```text
Phishing knowledge
        ↓
Knowledge graph / retrieval
        ↓
LLM
        ↓
Synthetic phishing URLs
        ↓
Validation and deduplication
        ↓
Training data
```

## Why It Is Important for Us

Our group considered using an existing pretrained LLM rather than building a custom LSTM or GAN.

However, this paper shows that:

> Simply replacing a GAN with an LLM is probably not enough to make our project novel.

Therefore, our main contribution should probably come from investigating the effect of synthetic data on **future real-world generalisation**, rather than developing another generation method.

## What We Need to Look For

Focus on:

- Which LLM do they use?
- Is the LLM fine-tuned or only prompted?
- How are prompts constructed?
- What information is provided to the LLM?
- How are generated URLs validated?
- How do they remove duplicates?
- How do they identify low-quality generated samples?
- How many synthetic samples are generated?
- What synthetic-to-real ratio is used?
- Which datasets do they use?
- How do they split the data?
- Do they test on future real phishing URLs?
- Do they test across datasets?
- Do they compare against simpler augmentation?
- What limitations do they mention?

## How We Could Build on It

We probably do not need something as complicated as GraphRAG.

Instead, we could use:

```text
Simple controlled AI generation + Real training data
            ↓
       Detector
            ↓
Later real phishing URLs
```

Our contribution would focus more on **whether synthetic training data actually helps generalisation**.

---