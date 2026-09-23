# Literature Review: Phishing URL Detection

## 1. Project Motivation

Phishing detection is already well studied, so rather than simply comparing classifiers, we hope to identify a **meaningful limitation in existing approaches**.

**Candidate datasets:** PhiUSIIL, PhreshPhish

**Directions currently being explored:**

- **Lightweight URL-based features:** can they retain strong detection performance without requiring richer webpage-content features?
- **Performance vs. information cost:** the trade-off between predictive performance and the amount/cost of information required for detection.
- **Cross-dataset or temporal robustness:** does a detector continue to perform well on independently collected or newer phishing data?
- **Combined direction:** can a lightweight detector achieve strong performance *while also* generalizing well to independent data?

---

## 2. Literature Reviewed

### 2.1 Identifying Suspicious URLs: An Application of Large-Scale Online Learning (2009)

**Summary**

- Builds a lightweight model that uses URL information alone to predict phishing links, reaching **99% accuracy**.
- Uses lexical and host-based features only; no page context.
- Examines tokens by their position in the URL (e.g., `.com` or `ebay` appearing out of place).
- Considers IP and geographical information in URLs.
- Uses Perceptron and other linear/online learning algorithms (predict → observe result → update the next prediction).

**Limitations**

- Relies on host-based features (DNS, etc.), which is a drawback for a purely lightweight approach.

**Takeaway**

- The paper describes its algorithms in good detail; we could likely replicate it fairly easily.

---

### 2.2 Evaluating the Impact of Feature Engineering in Phishing URL Detection: A Comparative Study of URL, HTML, and Derived Features

**URL features used in the study**

URL features play an essential role in phishing URL detection by offering key insights into a web page's legitimacy. The main URL features used are:

`IsHTTPS`, `IsDomainIP`, `TLD`, `URLLength`, `NoOfSubDomain`, `NoOfDots`, `NoOfObfuscatedChar`, `NoOfEqual`, `NoOfQmark`, `NoOfAmp`, `NoOfDigits`

**Table 2: Initial parameters for each model**

| Model                  | Parameters                                   |
|------------------------|----------------------------------------------|
| Random Forest          | `n_estimators=10`                            |
| Naive Bayes            | default                                      |
| k-Nearest Neighbors    | `n_neighbors=1`                              |
| Logistic Regression    | default                                      |
| Decision Tree          | `max_depth=30`                               |
| Support Vector Machine | `kernel='linear'`, `C=1.0`, `random_state=42`|
| Gradient Boosting      | `max_depth=4`, `learning_rate=0.7`           |
| LightGBM               | `random_state=42`                            |
| XGBClassifier          | `max_depth=4`, `learning_rate=0.7`           |
| CatBoost               | `learning_rate=0.1`                          |

**Table 3: ML model performance on URL-based features**

| Model                  | Accuracy | Precision | Recall | F1 Score |
|------------------------|---------:|----------:|-------:|---------:|
| Random Forest          | 0.9658   | 0.9704    | 0.9595 | 0.9649   |
| Naive Bayes            | 0.7274   | 0.9942    | 0.4471 | 0.6168   |
| k-Nearest Neighbors    | 0.9459   | 0.9418    | 0.9483 | 0.9450   |
| Logistic Regression    | 0.9190   | 0.9441    | 0.8874 | 0.9149   |
| Decision Tree          | 0.9659   | 0.9711    | 0.9591 | 0.9650   |
| Support Vector Machine | 0.9219   | 0.9583    | 0.8790 | 0.9170   |
| Gradient Boosting      | 0.9675   | 0.9726    | 0.9609 | 0.9667   |
| LightGBM               | 0.9674   | 0.9726    | 0.9606 | 0.9666   |
| XGBoost                | 0.9667   | 0.9728    | 0.9589 | 0.9658   |
| CatBoost               | 0.9673   | 0.9713    | 0.9618 | 0.9665   |

**Figure 3:** Feature-importance heatmap of URL-based features across models (see original PDF). URL length, TLD, and number of subdomains appear to be the most valuable features.

**Ideas sparked by this paper**

- **Minimal-feature detector:** super-lightweight identification based on only a small number of parameters (e.g., URL length, TLD, and number of subdomains), since these are the most valuable features.
- **Explainability layer:** build a SHAP/LIME-based explanation on top of the best classifier and evaluate whether the top features it surfaces are stable across the dataset or noisy/dataset-specific. Ties into gap #6 and offers a good "AI trust" angle for a data mining course.
- **AI-generated phishing test:** build a model on this dataset, then source AI-based phishing links, test the model against them, and try to identify why AI phishing is thriving.

---

### 2.3 DeepPhish: Simulating Malicious AI

- Link: <https://albahnsen.wordpress.com/wp-content/uploads/2018/05/deepphish-simulating-maliciousai_submitted.pdf>
- Takes an existing dataset of phishing links and a phishing detection model, then builds a new model that **generates phishing URLs** based on the most effective "threat actors," using a long short-term memory (LSTM) network.
- Key result:

  > "Using the algorithm with the data of two threat actors, they were able to improve their effectiveness rate, measured as the percentage of attacks not blocked by a proactive phishing detection system, from 0.69% to 20.9%, and from 4.91% to 36.28%, respectively."

- **Open question:** do institutions give feedback on phishing URLs? If so, could a model be trained on, for example, Gmail's responses to attempted phishing emails?

**Related coverage**

- PCWorld, *Cyber gangsters with AI: how phishing emails become uncannily real*: <https://www.pcworld.com/article/2967414/cyber-gangsters-with-ai-how-phishing-emails-becomeuncannily-real.html>
  - Pop-culture article discussing the overall situation and DeepPhish.

---

### 2.4 Exploring the Dark Side of AI: Advanced Phishing Attack Design and Deployment Using ChatGPT

- Link: <https://ieeexplore.ieee.org/abstract/document/10288940>
- Not accessible from a work PC; should be accessible through UVic.
- Interesting title; still to be reviewed.

---

### 2.5 Springer: AI and the Emergence of AI Phishing

- Link: <https://link.springer.com/article/10.1007/s10462-024-10973-2>
- Great background paper on the overall threat and emergence of AI phishing. **Section 4.3** is directly relevant to our work.
- Raises the point that IT/security teams within businesses focus more on **human training and awareness** than on technical identification of phishing content.

---

### 2.6 Other Resources and Data Sources

- **stopwatch.ai:** could be interesting; appears to allow generating attacks for different operating systems, targets, etc.
- **Existing AI-augmented phishing corpora:** groups such as APWG, PhishTank, and various academic security labs have published adversarial phishing datasets (including LLM-generated lures) specifically for detector benchmarking. Using an existing, responsibly curated dataset sidesteps the generation problem entirely.
- **Red-teaming via a security research partnership:** for defensive model evaluation, some organizations gain access to adversarial datasets through academic collaborations or industry security consortia (with explicit ethical review and containment), which is a more appropriate channel than open-ended generation.
  - **To do:** check whether this is available through UVic.

---

## 3. Emerging Research Direction

### 3.1 Initial Framing (Work in Progress)

A possible framing:

> We've built a model to identify phishing links, but can easily generate new URLs using AI to beat our model. Combined with industry data *X* (showing phishing is on the rise / persistent) and economic implications *A*, *B*, and *C*, it is rational for organizations to focus security efforts on **education over identification**, due to *Z*% better ROI on education capex vs. identification capex.

This is not a novel contribution on its own, but reflects current thinking; more polished ideas to follow for the team meeting.

### 3.2 Refined Ideas

The following were developed in a brainstorming session: <https://claude.ai/share/707abc61-6c7d-43f5-a0ed-55856622c2bc>

#### Idea A: Shift the dependent variable from "does it evade?" to "does it fool a human?"

- Run a small human-subjects arm: take AI-generated URLs that evade our model and show them (alongside real phishing and real benign URLs) to a small sample of people, measuring detection rate.
- The real finding becomes: **are model-evading URLs also harder for humans, or is there a divergence?** (URLs that evade the ML detector but a human would flag instantly, or vice versa.)
- That divergence is the novel contribution and directly informs the capex-vs-training argument with actual data instead of an assumed conclusion.
- **Hypothesis:** model-trained URLs might beat our detector but still lose to a human, because they pick up odd artifacts in order to beat the model.

#### Idea B: Cost-model the arms race explicitly

- Quantify the economics rather than assuming them. Estimate:
  - compute/token cost per generated evading URL,
  - cost of retraining the detector, and
  - estimated cost of a training program per employee per successful catch.
- This turns "training has better ROI" from an assumed conclusion into a number derived from our own experimental cost data.
- Most phishing-detection papers don't attach dollar values to either side, which makes this a unique spin.

#### Combining Ideas A and B

Combining the two is the strongest version of the project and a genuinely useful finding rather than a foregone conclusion.

**Mechanism:** the detector is a *proxy* for "phishing-ness." Once URLs are optimized against the proxy instead of the real target (human judgment), they drift off-distribution in ways the proxy doesn't penalize but a human would immediately catch. This is a classic adversarial-example failure mode (essentially Goodhart's law), and demonstrating it concretely in the phishing-URL domain would be a solid contribution.

### 3.3 Study Design Considerations

**1. Predict artifact types before seeing them, then check.** Hypothesize up front which "tells" AI-generated URLs will pick up, such as:

- Overlong or structurally bizarre subdomains that satisfy a lexical feature the model weights heavily but look obviously wrong to a human
- Unnatural keyword stuffing (brand name + urgency word + random token) that reads as "off" linguistically, even if it nudges character-level features
- Homoglyphs or encoding tricks that fool string-matching features but render visibly strange
- TLD/path combinations that are statistically rare in the benign class (so the model underweights them) but look nothing like real corporate infrastructure

Then check which actually appear. Either outcome is interesting:

- If the AI finds bypasses that are humanly obvious, that is the headline result.
- If the bypasses are also convincing to humans, that is an arguably scarier and equally publishable finding.

**2. Use three groups in the human evaluation, not two.**

| Group | Purpose |
|-------|---------|
| Real phishing | Baseline for how humans judge genuine phishing |
| Real benign | Baseline for legitimate URLs |
| AI-generated, model-evading | The treatment group |

With only two groups (real vs. AI-evading), we couldn't tell whether humans are just pattern-matching "weird = phishing." The real-phishing baseline shows whether AI-evading URLs are flagged **more** than genuine phishing (they're extra suspicious) or **less** (the human evaluation validates the bypass).

**3. Measure confidence, not just accuracy.** Capture how confident humans are when flagging (e.g., a simple 1–5 scale). Humans might correctly flag AI-evading URLs but with lower confidence than genuine phishing, suggesting the model found something "off" without hitting a recognizable phishing pattern. This adds nuance to the capex argument: training works, but the friction/hesitation cost differs from a simple "human vs. model" comparison.

### 3.4 Resulting Argument

This addresses the novelty concern with the capex conclusion. Instead of assuming "training beats filtering" as a premise, the argument becomes:

> **The failure mode of automated detection is specifically legible to humans in identifiable ways X, Y, Z.**

This is a mechanism-level claim, not just a policy recommendation.

---

## 4. Next Steps

- Access the IEEE ChatGPT phishing paper through UVic.
- Check whether adversarial phishing datasets or a research partnership are available through UVic.
- Sketch the human-evaluation design (sample size, recruitment/administration, what counts as a "flag").
- Work out a realistic detector architecture and feature set for the adversarial loop.
