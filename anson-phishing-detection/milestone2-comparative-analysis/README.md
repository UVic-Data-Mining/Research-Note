# Comparative Analysis of Phishing Detection
## Interpretable analysis of non-detection on AI-generated phishing-like URLs

## 1. Comparison framework
We compare the hierarchy’s overlapping research roles: detection (D1–D2), generation for evaluation or training (S1–S2), and distribution shift, stress testing and diagnosis (E1–E3). The questions are what a method observes, how it processes that evidence, what its experiment establishes, and what remains unresolved. Table 1 and Figure 1 support this comparison; they are not a cross-paper accuracy ranking. A learned representation changes the encoding of information, whereas an extra information source changes what the system can observe.

## 2. Detection: representation versus information access
Hannousse–Yahiouche [1] compare 87 URL, content and external-service features on 11,430 balanced observations, including extraction cost. Bahnsen et al. [2] compare a character-level long short-term memory network (LSTM) with an engineered-feature random forest (RF) that also uses an Alexa-list indicator. Named inputs aid inspection, but do not make every decision transparent. URLNet [3] combines character- and word-level convolutional representations; its timestamp-ordered evaluation concerns malicious URLs more broadly than phishing. URLTran [4] uses transformers: in its own benchmark, true-positive rate is 86.80% versus 71.20% for its URLNet baseline at 0.01% false-positive rate. This matched comparison does not establish a universal ranking or supply missing webpage evidence.

## 3. References
[1] A. Hannousse and S. Yahiouche. 2021. Towards benchmark datasets for machine learning based website phishing detection: An experimental study. *Engineering Applications of Artificial Intelligence* 104, 104347.

[2] A. Correa Bahnsen *et al.* 2017. Classifying Phishing URLs Using Recurrent Neural Networks. *APWG eCrime*, 1–8.

[3] H. Le *et al.* 2018. URLNet: Learning a URL Representation with Deep Learning for Malicious URL Detection. *arXiv:1802.03162* (preprint).

[4] P. Maneriker *et al.* 2021. URLTran: Improving Phishing URL Detection Using Transformers. *IEEE MILCOM*.
