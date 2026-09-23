# Formal Problem Definition
## Interpretable analysis of non-detection on AI-generated phishing-like URLs

Table of content:
1. [Problem and scope](#1-problem-and-scope)
2. [Input space and fixed classifier](#2-input-space-and-fixed-classifier)
3. [Diagnostic outcomes](#3-diagnostic-outcomes)
4. [Learning objective and required output](#4-learning-objective-and-required-output)
5. [Validation and interpretation](#5-validation-and-interpretation)
6. [References](#6-references)

## 1. Problem and scope
This project investigates which URL characteristics are associated with a phishing classifier not flagging AI-generated phishing-like examples. Given a collection of generated URL strings and the responses of a fixed classifier, the task is to learn an interpretable diagnostic function that characterizes the distinction between flagged and unflagged examples. The objective is not merely to count missed examples, but to identify recurring associations and assess whether they persist on held-out observations.

We formulate this as a learning problem by specifying the available observations, the target function, and the criteria for evaluating the learned characterization, without prescribing a particular algorithm or feature set [1, Secs. 1.1–1.2]. The definitions below specify the proposed study rather than assert experimental findings.

## 2. Input space and fixed classifier
Let $\mathcal{U}$ denote the space of syntactically valid URL strings within the study’s documented input scope, and let

$$S = \lbrace u_1, u_2, \ldots, u_n \rbrace \subseteq \mathcal{U}, \quad n > 0,$$

be a collection of AI-generated phishing-like examples. Each $u_i$ carries an intended-positive annotation $y_i = 1$, indicating its inclusion as a synthetic phishing test example. This annotation records the intended class, not independently verified malicious activity. Inclusion criteria and generation provenance are documented, and acceptance into $S$ is not determined by whether the evaluated classifier flags an example.

Let

$$f : \mathcal{U} \to \lbrace 0, 1 \rbrace$$

denote a URL-based phishing classifier, where $f(u) = 1$ means that the classifier flags $u$ as phishing and $f(u) = 0$ means that it predicts $u$ to be non-phishing. The latter prediction does not establish that the associated resource is safe.

For each diagnostic analysis, $f$ is trained or selected using recorded real-world URL observations and then held fixed, including its preprocessing, parameters, and decision threshold. The synthetic collection $S$ is excluded from that detector’s training and model selection. This separation implements the distinction between model development and evaluation emphasized in security-ML methodology [2, Sec. 2.2]. Here, “recorded real-world” describes the source of the observations; it does not assume that their URLs were manually authored.

## 3. Diagnostic outcomes
For each $u_i \in S$, define the observed diagnostic target as

$$z_i = 1 - f(u_i).$$

Thus, $z_i = 1$ means that the example is not flagged, whereas $z_i = 0$ means that it is flagged. The collection is partitioned into the unflagged and flagged groups, respectively:

$$S_f^{\mathrm{unflagged}} = \lbrace u_i \in S : z_i = 1 \rbrace,$$

$$S_f^{\mathrm{flagged}} = \lbrace u_i \in S : z_i = 0 \rbrace.$$

Both groups contain intended-positive synthetic examples; they differ in the classifier’s response. The diagnostic label $z_i$ must therefore be distinguished from the intended annotation $y_i$ and from the phishing prediction $f(u_i)$.

An unflagged synthetic example is termed a case of synthetic non-detection, not automatically a verified real-world false negative. A generated string alone does not establish an operational phishing webpage. DeepPhish likewise distinguishes bypassing a detector from successfully stealing credentials [3, Sec. IV-A]. Accordingly, this study measures classifier behaviour, not successful phishing attacks. Its intended-positive synthetic collection cannot, by itself, estimate false-positive rates on real non-phishing observations.

## 4. Learning objective and required output
The diagnostic observations are

$$D_f = \lbrace (u_i, z_i) \rbrace_{i=1}^{n}.$$

From a discovery portion of these observations, the task is to learn an interpretable function

$$h_f : \mathcal{U} \to \lbrace 0, 1 \rbrace,$$

where $h_f(u) = 1$ predicts non-detection by the fixed classifier and $h_f(u) = 0$ predicts that the classifier flags the example. Its intended approximation, within the evaluated generated-URL setting, is

$$h_f(u) \approx 1 - f(u).$$

The target outcomes are directly observable by evaluating $f$; they are not unknown phishing-status labels. The research task is to learn a useful, interpretable description of their relationship to URL information. This is a diagnostic approximation of model behaviour, consistent with the distinction between a model and an interpretable approximation of its predictions in model-explanation research [4]. It is not a replacement phishing detector or a new claim about the true malicious status of an input.

The required output comprises the learned diagnostic function and an explicit characterization of the URL properties, or combinations of properties, associated with the fixed classifier’s flagged and unflagged groups. Each reported relationship must describe which observations it applies to, the direction of the association, its coverage, and the observed non-detection frequency relative to a stated comparison group. Supporting uncertainty must accompany quantitative comparisons.

Here, an interpretable characterization means that the reported relationships can be expressed in understandable terms linked to properties of the URL string, rather than only as an opaque score or a list of individual URLs. The particular representation is left open. URL-derived information is the explanatory input; the detector’s prediction and score are not supplied as input variables to $h_f$. Predictions from $f$ are used only to define and assess diagnostic outcomes.

High agreement with $1 - f$ is necessary evidence of a useful approximation, but is not sufficient to meet the explanation-focused objective. In particular, a characteristic that predicts non-detection need not be a feature directly used by $f$: it may be correlated with other information. Reported associations must therefore be checked against observed detector outcomes, rather than treated as an exact account of the detector’s internal mechanism.

## 5. Validation and interpretation
The learned function and discovered relationships are assessed on a protected confirmation portion of the generated collection that is not used to select the diagnostic representation or reported patterns. Known groups of related generated examples are kept together when defining discovery and confirmation partitions. Detector development, diagnostic discovery, and diagnostic confirmation thus have distinct data roles. This is a proposed safeguard against the information leakage and biased evaluation discussed by Arp et al. [2].

Evaluation considers diagnostic agreement separately for flagged and unflagged examples, as well as subgroup coverage, non-detection frequencies, and the persistence of reported associations. Overall agreement alone can conceal failure on the smaller outcome group. If one group is absent or too small to support a comparison, that limitation is reported instead of asserting a reliable distinction.

Conclusions are conditional on the classifier, its fixed decision rule, the source data, and the documented generation setting. Where several classifiers or generation groups are examined, associations are evaluated separately before shared patterns are claimed. Evidence that phishing-feature contributions vary across datasets motivates this caution, but does not establish what will occur in the proposed experiment [5, Secs. 3.5–4]. Generalization to real-world phishing errors requires separate evidence from held-out observations with recorded phishing labels.

The study identifies associations, not causal effects. Predictive explanations do not, on their own, establish that changing a characteristic will change an outcome [6]. The comparison also does not establish that AI authorship causes non-detection. No performance decline, universal weakness, or stable distinguishing pattern is assumed in advance; a finding that apparent associations do not persist is a valid research outcome.

## 6. References
[1] T. M. Mitchell, *Machine Learning*. New York, NY, USA: McGraw-Hill, 1997, ch. 1.

[2] D. Arp *et al.*, “Dos and don’ts of machine learning in computer security,” in *Proc. 31st USENIX Secur. Symp.*, Boston, MA, USA, 2022, pp. 3971–3988. [Online]. Available: <https://www.usenix.org/conference/usenixsecurity22/presentation/arp>

[3] A. Correa Bahnsen, I. Torroledo, L. D. Camacho, and S. Villegas, “DeepPhish: Simulating malicious AI,” unpublished manuscript, 2018. [Online]. Available: <https://albahnsen.wordpress.com/wp-content/uploads/2018/05/deepphish-simulating-malicious-ai_submitted.pdf>

[4] O. Bastani, C. Kim, and H. Bastani, “Interpreting blackbox models via model extraction,” 2017, *arXiv:1705.08504v6*. [Online]. Available: <https://arxiv.org/abs/1705.08504v6>

[5] M. Mia, D. Derakhshan, and M. M. A. Pritom, “Can features for phishing URL detection be trusted across diverse datasets? A case study with explainable AI,” in *Proc. 11th Int. Conf. Netw., Syst., Secur. (NSysS ’24)*, Khulna, Bangladesh, 2024, pp. 137–145, doi: [10.1145/3704522.3704532](https://doi.org/10.1145/3704522.3704532).

[6] E. Dillon, J. LaRiviere, S. Lundberg, J. Roth, and V. Syrgkanis, “Be careful when interpreting predictive models in search of causal insights,” *SHAP Documentation*. Accessed: Sep. 23, 2026. [Online]. Available: <https://shap.readthedocs.io/en/latest/example_notebooks/overviews/Be%20careful%20when%20interpreting%20predictive%20models%20in%20search%20of%20causal%20insights.html>
