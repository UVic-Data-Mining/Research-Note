# Paper Name: Towards Benchmark Datasets for Machine Learning Based Website Phishing Detection: An Experimental Study


## 0. Key Findings

1. Random Forest was the best-performing classifier
2. Hybrid feature sets performed better than using a single feature category
3. External service-based features were highly discriminative despite their small number
4. However, external features introduced network delays during feature extraction
5. The examined content-based features were less discriminative than expected
6. Some content-based features were also computationally expensive to extract
7. Using more features did not necessarily lead to better performance
8. Filter-based feature selection improved classification accuracy while reducing the number of features
9. Using hybrid features in a single model performed better than combining multiple classifiers trained on separate feature categories
10. Continuously constructing time-stamped datasets is important for tracking changes in phishing tactics and evaluating the generalizability of detection models over time

---

## 1. Objective

The paper aims to:
- Propose guidelines for constructing reproducible and extensible phishing detection datasets
- Evaluate commonly used phishing detection features
- Compare machine learning classifiers using different feature classes and their combinations
- Evaluate feature selection methods
- Analyze the extraction cost of different features for real-time phishing detection

---

## 2. Dataset Construction

The authors constructed a balanced dataset containing 11,430 URLs:
- 5,715 legitimate URLs
- 5,715 phishing URLs

### Data Sources

Legitimate URLs
- Alexa
- Yandex

Phishing URLs
- PhishTank
- OpenPhish

During preprocessing:
- Duplicate and inactive URLs were removed
- A maximum of **12 URLs from the same domain** were retained
- The source of each URL was recorded
- HTML DOM trees were stored to allow future extraction of content-based features

### Dataset Construction Guidelines

The paper proposes six guidelines:
1. Collect URLs from multiple sources
2. Remove duplicates and avoid excessive samples from the same domain
3. Preserve the original URLs so that new features can be extracted later
4. Preserve HTML DOM data because phishing websites may disappear quickly
5. Maintain a balanced number of phishing and legitimate samples
6. Record collection dates so that changes in phishing strategies can be analyzed over time

---

## 3. Features

A total of **87 features** were collected and divided into three main classes

### URL-Based Features (IU) — 56 Features

Features extracted directly from the URL.

Examples:
- URL length
- Hostname length
- Number of dots
- Number of subdomains
- Number of digits
- Digit ratio
- Use of IP address
- HTTPS
- URL shortening
- Suspicious TLD
- Prefix/suffix
- Phishing-related words

These features are relatively inexpensive to extract because they do not require accessing webpage content or external services

### Content-Based Features (IC) — 24 Features

Features extracted from webpage content and HTML structure

Examples:
- Internal/external hyperlinks
- Login forms
- External CSS
- Redirects
- Favicon and media links
- Iframes
- Popup windows
- Unsafe anchors
- Empty page titles
- Domain name in the page title

### External Service-Based Features (E) — 7 Features

Features obtained through external services

Examples:
- WHOIS registration information
- Domain registration length
- Domain age
- Web traffic
- DNS information
- Google index
- PageRank

These features can be highly discriminative but require additional network or external service requests

---

## 4. Experiment I — Classifier and Feature-Class Comparison

Five machine learning classifiers were evaluated:
- Decision Tree
- Random Forest
- Logistic Regression
- Naive Bayes
- Support Vector Machine (SVM)

The models were evaluated using individual feature classes and combinations of feature classes

### Individual Feature Classes

Using Random Forest:

| Feature Class | Accuracy |
|---|---:|
| URL-based | 91.03% |
| Content-based | 89.87% |
| External service-based | 94.09% |

**Random Forest achieved the best overall performance among the evaluated classifiers**

External service-based features produced the highest accuracy when each feature class was evaluated independently

### Hybrid Features

Combining different feature classes generally improved performance compared with using individual feature classes

The combination of **URL-based and external service-based features** achieved approximately **96.6% accuracy** using Random Forest

Using hybrid features demonstrated that information from multiple feature classes can provide better phishing detection performance than relying on a single feature class

---

## 5. Experiment II — Combining Multiple Models

Instead of combining features into one model, the authors also trained separate classifiers for different feature classes and combined their predictions

The following combination strategies were evaluated:
- AND
- OR
- Stacking
- Majority voting

Conceptually:

    URL Features      → Model 1 ─┐
    Content Features  → Model 2 ─┼→ Combination → Prediction
    External Features → Model 3 ─┘

The results showed that combining separate models did **not** outperform a single classifier trained using hybrid features

Approximate results:

| Combination Method | Accuracy |
|---|---:|
| AND | ~89% |
| OR | ~89% |
| Stacking | ~95.6% |
| Majority Voting | ~95.9% |

Therefore, combining feature classes within a single model was more effective than combining predictions from independently trained models in this experiment

---

## 6. Experiment III — Filter-Based Feature Selection

Four filter-based feature ranking methods were evaluated:
- Chi-square
- Pearson correlation
- Information gain
- Relief

Features were ranked according to their importance. The lowest-ranked features were then progressively removed, and Random Forest was retrained on the remaining features

### Results

Feature selection improved Random Forest performance

The best result was obtained using **chi-square feature ranking**, with approximately:
- **73 selected features**
- **96.83% accuracy**

This was slightly higher than using all 87 features

The experiment also showed that:
- Several content-based features were identified as less important
- All external service-based features were considered important by the examined ranking methods
- A small subset of highly ranked features could still achieve relatively high classification performance

Seven features appeared in the top-25 lists of all four ranking methods and achieved approximately **94.67% accuracy** when used together

---

## 7. Experiment IV — Wrapper-Based Feature Selection

The paper also evaluated wrapper-based feature selection methods

Methods included:
- ClassifierSubsetEval
- WrapperSubsetEval
- Boruta

Unlike filter methods, wrapper methods evaluate feature subsets by repeatedly training a machine learning model

This makes them computationally more expensive

In this study, filter-based ranking combined with progressive removal of low-ranked features provided competitive or better results while requiring less computational effort

---

## 8. Experiment V — Feature Extraction Time

The authors measured the time required to extract different feature classes to evaluate their suitability for real-time phishing detection.

### URL-Based Features

URL-based features were very inexpensive to extract.

The complete URL-based feature set required approximately:
**41.5 ms**

This makes URL-based features suitable for real-time detection

### External Service-Based Features

External features were considerably slower because they required network requests to external services

Their extraction could require approximately **4–5 seconds**

### Content-Based Features

Some content-based features were also unexpectedly expensive

In particular, hyperlink-related features such as **f63 and f65** required substantial processing because they analyze links contained in webpages

Therefore, extraction cost depends not only on the number of features but also on how each feature is obtained

---

## 9. Main Findings

- Random Forest achieved the strongest overall classification performance among the evaluated classifiers
- External service-based features were the most discriminative individual feature class
- URL-based features were fast to extract and suitable for real-time detection
- Some content-based features provided relatively limited discriminative value and could also be computationally expensive
- Hybrid feature sets generally performed better than individual feature classes
- Combining multiple independently trained models did not outperform using hybrid features in a single model
- Feature selection could reduce the number of features without reducing performance and could slightly improve classification accuracy
- Feature extraction time should be considered when designing real-time phishing detection systems
- Phishing characteristics change over time, making reproducible and time-aware benchmark datasets important

---

## 10. Limitations and Future Work

The experimental results are based primarily on the dataset constructed by the authors. The paper emphasizes that phishing detection results can be dataset-dependent

The authors suggest:
- Evaluating the findings using additional datasets constructed according to the proposed guidelines
- Continuously updating datasets to reflect changes in phishing strategies
- Investigating more discriminative content-based features
- Exploring deep learning approaches in future work

---

## 11. Key Takeaway

The paper shows that phishing detection performance depends not only on the choice of classifier but also on the dataset, feature classes, feature selection, and feature extraction cost.

Random Forest with hybrid features achieved strong performance, while feature selection showed that not all 87 features were necessary. The paper therefore emphasizes the importance of reproducible benchmark datasets and evaluating both predictive performance and practical feature extraction cost.
