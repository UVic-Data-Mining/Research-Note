# Related Work Notes: Phishing URL Detection

> **Disclaimer:** These are AI-edited notes. They were reorganized and reworded from the team's raw research notes with the help of an AI assistant, and they are not the raw notes themselves. Passages copied from papers in the raw notes have been paraphrased here, so check the original sources before quoting or citing anything.

## Contents

1. [Background](#background)
2. [Existing approaches](#existing-approaches)
3. [How the approaches differ](#how-the-approaches-differ)
4. [Strengths and limitations](#strengths-and-limitations)
5. [Sources](#sources)

## Background

### A short history of fraud

- The word "fraud" comes from the Latin *fraudem*, meaning deceit or injury.
- One of the earliest documented cases is the Greek shipping merchant Hegestratos, who attempted insurance fraud around 300 BC. [Trulioo]
- Ancient governments had related problems. The Egyptian government reportedly struggled to collect taxes in 525 BCE. *(Needs a source.)*
- Technology-enabled fraud is usually dated to the 1980s. Early schemes exploited loopholes in telecommunications policy, for example by conning people into calling expensive phone numbers. Later schemes used TV ads, and in one case targeted children using dial-tone signals played over the broadcast. [Trulioo]
- In the early days of the telephone, scammers would talk operators into connecting free calls. [Becker et al.]
- In the early 1990s, e-commerce opened a new front. Credit card verification was immature, and fraudsters stole card details (including those of celebrities) to buy expensive goods. Card fraud is still a major problem today. [Trulioo]
- The growth of the internet has expanded both the opportunity for fraud and its sophistication. No business or individual is exempt.

### A short history of phishing

- **1990s, AOL:** Early phishing targeted AOL users to steal credentials and get free internet access. The tool AOHell is credited with popularizing the practice and the term. [Wikipedia] [Get Cyber Safe]
- **2000, Love Bug:** The ILOVEYOU worm, which originated in the Philippines, infected roughly 45 million Windows PCs.
- **2000s, brand impersonation:** PayPal became a frequent impersonation target.
- **Today:** Cryptocurrency makes it easier for scammers to move and hide money. Phishing kits and tools such as the Social Engineer Toolkit can be obtained online, and there is a whole market around them.

Two themes run through this history:

- Social engineering is cheaper than a technical attack. Why break a firewall when you can persuade someone to let you in?
- Deception is as old as trade, so technology alone will never fully stop phishing.

## Existing approaches

Research on phishing spans server-side and browser-side defences, user education and training, evaluations of anti-phishing tools, detection schemes, and studies of why attacks succeed. [Thakur & Verma] Thakur and Verma group detection methods into four categories: blacklist matching, heuristics with machine learning, password-based schemes, and information extraction combined with information retrieval. The notes below follow that grouping and add the newer deep learning work.

### 1. Blacklists (reactive)

Services such as the Google Safe Browsing API expose a list of known malicious URLs that browsers and other tools query before loading a page. The lists are built from manual reports, honeypots, and web crawlers that look for known phishing characteristics. [DeepPhish] Network-level blocking of untrusted IP addresses works the same way and has the same dependency on a maintained list.

### 2. Heuristics with classical machine learning

Features are extracted from the URL (and sometimes the page), then passed to a classifier. One example pipeline applies Random Forest, Decision Tree, and LightGBM models to the extracted features. [ScienceDirect article]

### 3. Deep learning on URLs

Neural models learn directly from the URL string rather than from hand-built features. DeepPhish is notable for looking at the problem from the attacker's side as well, simulating how an adversary could use AI to generate URLs that evade detection. [DeepPhish]

### 4. Content analysis and information extraction

These methods inspect the page itself, for example by extracting named entities and comparing them with search results to find the brand being impersonated. [Thakur & Verma]

### 5. Password-based schemes

These try to detect or prevent credentials being entered on the wrong site. [Thakur & Verma]

## How the approaches differ

| Approach | Timing | Needs page content? | Handles unseen URLs? | Relative cost |
|---|---|---|---|---|
| Blacklists | Reactive | No | No | Low at lookup, high to maintain |
| Heuristics + classical ML | Proactive | Sometimes | Yes, within limits | Low to moderate |
| Deep learning on URLs | Proactive | No | Yes, within limits | Moderate (training) |
| Content analysis | Proactive | Yes | Yes | High |
| Password-based | Proactive | No | Limited | Low |

The main dividing lines are reactive versus proactive (whether a URL must already be known to be caught) and URL-only versus content-based (whether the page has to be fetched and analyzed).

## Strengths and limitations

### Blacklists

**Strengths**

- Very low false positive rate, since entries are human-verified. [Thakur & Verma]
- Simple and widely deployed in browsers.

**Limitations**

- A URL is only blocked after it has been reported and added, so users are exposed in the meantime. [DeepPhish]
- Zero-hour true positive rates for major blacklist toolbars have been measured at only 15 to 40 percent. [Thakur & Verma, citing Sheng et al.]
- Verification is slow and labour-intensive. PhishTank's January 2012 statistics put the median time to verify a phish at two hours. [Thakur & Verma]
- Phishing sites are short-lived. Most are active for less than a day by one account [DeepPhish], and another measured the average phishing domain lifetime at just over three days. [McGrath & Gupta] Either way, the attack is often finished before the listing appears.
- Human verification can be overwhelmed by automatically generated URLs, and AI tooling now makes mass generation fast and cheap.

### Heuristics with classical machine learning

**Strengths**

- Proactive, so it can flag URLs that have never been seen before.
- Lightweight models that are quick to train and run.

**Limitations**

- Needs a clean, labelled training corpus. [Thakur & Verma]
- Prone to overfitting that corpus. Phishing patterns keep changing, so a model must generalize beyond its dataset.
- Needs retraining as the model drifts over time. [Thakur & Verma]
- Hard to keep the true positive rate high while keeping false positives low. [Thakur & Verma]

### URL-based detection (classical or deep)

**Strengths**

- Language agnostic, because it never reads the page text.
- Lightweight, because the page does not need to be fetched or rendered.

**Limitations**

- Shares the data, overfitting, and drift problems above.
- Attackers can use AI to generate URLs designed to evade a detector. [DeepPhish]

### Content analysis and information extraction

**Strengths**

- Uses much richer evidence than the URL alone.

**Limitations**

- Computationally expensive.
- Web pages often lack parsable sentences, and automatic language processing has limits. [Thakur & Verma]
- Named-entity extraction is sensitive to casing, for example failing to recognize a lower-case "paypal" as a brand. [Thakur & Verma]

### Password-based schemes

**Limitations**

- Not robust enough for phishing detection on their own. [Thakur & Verma]

## Sources

| Short name | Source |
|---|---|
| DeepPhish | Bahnsen et al., "DeepPhish: Simulating Malicious AI" (2018). <https://albahnsen.wordpress.com/wp-content/uploads/2018/05/deepphish-simulating-malicious-ai_submitted.pdf> |
| Thakur & Verma | T. Thakur and R. Verma, "Catching Classical and Hijack-Based Phishing Attacks" (2014). <https://link.springer.com/chapter/10.1007/978-3-319-13841-1_18> |
| McGrath & Gupta | McGrath and Gupta, USENIX LEET '08. <https://www.usenix.org/legacy/event/leet08/tech/full_papers/mcgrath/mcgrath.pdf> |
| IEEE 9759382 | Cited for URL detection being language independent and lightweight. <https://ieeexplore.ieee.org/document/9759382> |
| ScienceDirect article | Cited for the Random Forest, Decision Tree, and LightGBM pipeline. <https://www.sciencedirect.com/science/article/pii/S0965997822001892> |
| Becker et al. | Telecommunications fraud history and lessons learned, *Technometrics*. <https://www.tandfonline.com/doi/pdf/10.1198/TECH.2009.08136> |
| Trulioo | "The History of Fraud." <https://www.trulioo.com/blog/fraud-prevention/history-fraud> |
| Get Cyber Safe | Government of Canada, "History of Phishing." <https://www.getcybersafe.gc.ca/en/resources/history-phishing> |
| Wikipedia | "Phishing." <https://en.wikipedia.org/wiki/Phishing> |

Collected but not yet summarized:

- History of scamming: <https://ieeexplore.ieee.org/abstract/document/10543498>
- S. Mackenzie, "Scams" (book chapter): <https://www.taylorfrancis.com/chapters/edit/10.4324/9781843929680-11/scams-simon-mackenzie>
- Government of Canada (CSE) publication: <https://publications.gc.ca/collections/collection_2021/cstc-csec/D96-69-2021-eng.pdf>
- "Defending against phishing attacks: taxonomy of methods, current issues and future directions" (history of phishing detection): <https://books.google.ca/books?id=ZjtaEQAAQBAJ&pg=PA361>
- Team notes on the history of phishing (Google Doc): <https://docs.google.com/document/d/1GHU013W1LNCo8eIdR6DQO69iVJKOFkwhHbeZ9vw9YaA/edit?tab=t.0>
