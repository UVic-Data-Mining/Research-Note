# Looking for Trouble: Analyzing Classifier Behavior via Pattern Divergence

## 0. Key Findings

1. Overall classifier performance can hide substantially different behavior across specific data subgroups.
2. Divergence can be used to quantify how classifier behavior on a subgroup differs from its behavior on the overall dataset.
3. Frequent pattern mining can automatically identify relevant subgroups without requiring users to specify attributes or attribute values in advance.
4. DivExplorer is model-agnostic and can analyze classifiers as black boxes.
5. Shapley values can quantify the contribution of individual attribute values to the divergence of a specific subgroup.
6. Global item divergence can identify attributes that contribute to divergent behavior through interactions with other attributes, even when their individual divergence is small.
7. Some attribute values can act as corrective items, reducing divergence when added to an existing pattern.
8. Because divergence is non-monotonic, pruning subgroup searches based on the divergence of simpler patterns can cause important subgroups to be missed.
9. A minimum support threshold helps focus the analysis on sufficiently represented subgroups and reduces the effect of statistical fluctuations.
10. DivExplorer efficiently identified divergent subgroups across multiple real-world datasets, with most experiments completing in less than 20 seconds even at a minimum support of 0.01.

---

## 1. Objective

The paper aims to:

- Identify data subgroups in which a classifier behaves differently from its overall behavior.
- Introduce **divergence** as a measure of differences in classification behavior across subgroups.
- Automatically discover relevant subgroups using frequent pattern mining.
- Quantify the contribution of individual attribute values to subgroup divergence.
- Introduce a global measure of attribute contribution to divergence.
- Identify attribute values that reduce divergence.
- Evaluate the statistical significance of observed subgroup divergence.
- Develop an efficient algorithm for exploring divergent subgroups.

The proposed approach is implemented in a tool called **DivExplorer**.

---

## 2. Motivation

Classifier evaluation commonly focuses on aggregate performance over the entire dataset.

However, good overall performance does not guarantee that the classifier behaves similarly across all subsets of the data.

A classifier may have:

- Low overall error
- High error for a particular subgroup
- Different false-positive or false-negative rates for particular combinations of attribute values

The paper therefore focuses on automatically identifying subgroups where classifier behavior differs from the overall population.

---

## 3. Items and Itemsets

The paper represents data subgroups using **itemsets**.

### Item

An **item** is an attribute-value condition.

Examples:

- `sex = Male`
- `race = African-American`
- `#prior > 3`

### Itemset

An **itemset** is a combination of items referring to different attributes.

Example:

```text
{age = 25–45, #prior > 3, race = African-American, sex = Male}
```

An instance belongs to an itemset if it satisfies all conditions contained in that itemset.

---

## 4. Support

The **support** of an itemset is the proportion of dataset instances satisfying that itemset:

$$
sup(I) = \frac{|D(I)|}{|D|}
$$

where:

- $I$ is an itemset
- $D(I)$ is the set of instances satisfying $I$
- $D$ is the entire dataset

DivExplorer considers only **frequent itemsets**, whose support is above a user-specified threshold.

The support threshold is used because:

- Very small subgroups are more affected by statistical fluctuations.
- Patterns affecting very few instances may be less relevant.
- Restricting the analysis to frequent itemsets makes exhaustive exploration more practical.

---

## 5. Itemset Divergence

The central concept of the paper is **itemset divergence**.

For a statistic $f$, the divergence of itemset $I$ is:

$$
\Delta_f(I) = f(I) - f(D)
$$

where:

- $f(I)$ is the statistic measured on the subgroup represented by $I$
- $f(D)$ is the same statistic measured on the entire dataset

DivExplorer supports several classification metrics, including:

- Accuracy
- Misclassification error
- True positive rate
- True negative rate
- False positive rate
- False negative rate
- Positive predictive value
- False discovery rate
- False omission rate

### Example

Suppose:

```text
Overall FPR = 0.10
Subgroup FPR = 0.30
```

Then:

$$
\Delta_{FPR}(I) = 0.30 - 0.10 = 0.20
$$

The subgroup therefore has a false-positive rate that is **20 percentage points higher** than the overall dataset.

A positive or negative divergence indicates that the classifier behaves differently on that subgroup compared with the entire dataset.

---

## 6. Statistical Significance

A subgroup may show high divergence simply because it contains a relatively small number of instances.

The paper therefore introduces a Bayesian approach for evaluating statistical significance.

For a Boolean classifier outcome, the outcome is modeled as a Bernoulli random variable.

With a uniform prior, the posterior distribution of the positive outcome rate is:

$$
Beta(k^+ + 1, k^- + 1)
$$

where:

- $k^+$ is the number of positive outcomes
- $k^-$ is the number of negative outcomes

The subgroup outcome rate is then compared with the overall dataset rate using a Welch-style t-statistic.

This analysis helps determine whether an observed divergence is likely to reflect meaningful classifier behavior rather than statistical fluctuation.

---

## 7. Item Contribution to Divergence

After identifying a divergent itemset, the authors analyze which individual items contribute most to its divergence.

The paper uses **Shapley values**, originally developed in cooperative game theory, to quantify these contributions.

The contribution of an item is determined by considering how much the divergence changes when that item is added to different subsets of the itemset.

### COMPAS Example

One of the itemsets with high false-positive divergence is:

```text
age = 25–45
#prior > 3
race = African-American
sex = Male
```

The contribution analysis showed that:

- `#prior > 3` had the greatest contribution.
- `race = African-American` had the second-largest contribution.
- `sex = Male` had only a minor contribution.

Therefore, the method not only identifies a divergent subgroup but also characterizes the relative contribution of the items forming that subgroup.

---

## 8. Corrective Items

The paper introduces the concept of **corrective items**.

An item $\alpha$ is considered corrective for itemset $I$ when adding it reduces the absolute divergence:

$$
|\Delta(I \cup \alpha)| < |\Delta(I)|
$$

The corrective factor can be expressed as:

$$
|\Delta(I)| - |\Delta(I \cup \alpha)|
$$

A larger corrective factor indicates a greater reduction in divergence.

### COMPAS Example

For the pattern:

```text
race = African-American
sex = Male
```

the FPR divergence was:

```text
0.062
```

After adding:

```text
#prior = 0
```

the divergence decreased to:

```text
0.009
```

Therefore, `#prior = 0` acts as a corrective item for this pattern.

---

## 9. Global Item Divergence

Individual divergence measures how an item behaves when considered by itself.

However, an item may have little individual divergence while contributing strongly to divergent behavior when combined with other items.

The paper therefore introduces **global item divergence**.

Global item divergence is based on a generalized Shapley-value formulation and measures the contribution of an item across different contexts and itemsets.

This makes it possible to identify items whose importance mainly comes from their interactions with other attributes.

### Artificial Dataset Example

The artificial dataset was specifically designed to contain interaction effects.

The experiments showed that some attributes had nearly zero individual divergence but substantial global divergence.

This demonstrates that examining individual attributes alone may fail to identify important interactions associated with classifier behavior.

---

## 10. DivExplorer Algorithm

DivExplorer combines divergence analysis with **frequent pattern mining**.

The general process is:

1. Discretize continuous attributes.
2. Compute classifier outcomes.
3. Extract frequent itemsets above the minimum support threshold.
4. Compute the outcome rate for each frequent itemset.
5. Calculate divergence for each itemset.
6. Analyze and rank the extracted patterns.

The experiments use **FP-growth** for frequent itemset extraction.

### Soundness and Completeness

The proposed exploration is described as sound and complete with respect to the specified minimum support threshold.

**Soundness:**

Every reported itemset corresponds to an actual sufficiently supported subgroup and its computed divergence.

**Completeness:**

All itemsets whose support is above the specified threshold are explored.

This is important because highly divergent specific patterns may exist even when their simpler parent patterns do not have high divergence.

---

## 11. Continuous Attributes and Discretization

Frequent pattern mining requires categorical attribute values.

Therefore, continuous attributes must be discretized before itemset extraction.

For example:

```text
age = 32
```

may be converted into:

```text
age = 25–45
```

The classifier itself does not need to use discretized data. Discretization is required only for the subgroup exploration performed by DivExplorer.

The paper also shows that using a finer discretization does not necessarily hide existing divergence.

When a value range is divided into finer partitions, at least one of the resulting partitions has an absolute divergence at least as large as that of the original coarser partition.

---

## 12. Experimental Datasets

The evaluation uses six datasets:

- **COMPAS** — recidivism risk prediction
- **Adult** — income classification
- **German Credit** — credit risk prediction
- **Bank Marketing** — bank marketing response prediction
- **Heart** — heart disease prediction
- **Artificial dataset** — synthetic data designed to test interactions among attributes

For datasets without existing classifier outputs, the authors used a **Random Forest classifier with default parameters**.

DivExplorer was implemented in Python and used **FP-growth** for frequent itemset extraction.

---

## 13. COMPAS Results

The COMPAS dataset is used as one of the main examples throughout the paper.

The overall classifier performance included:

```text
Overall FPR = 0.088
Overall FNR = 0.698
```

However, some subgroups had substantially different error rates.

### High-FPR Example

The subgroup:

```text
age = 25–45
#prior > 3
race = African-American
sex = Male
```

had:

```text
FPR = 0.308
```

Compared with the overall FPR of `0.088`, its divergence was:

$$
0.308 - 0.088 = 0.220
$$

At a minimum support threshold of 0.1, this was one of the patterns with the largest FPR divergence.

### High-FNR Example

The subgroup:

```text
age > 45
race = Caucasian
```

had:

```text
FNR = 0.929
```

This example demonstrates how aggregate classifier statistics can hide substantially different behavior across specific subgroups.

---

## 14. Adult Dataset Results

The Adult dataset is used to demonstrate the identification of highly divergent patterns and the contribution of individual items.

### High-FPR Pattern

One of the patterns with the largest FPR divergence was:

```text
gain = 0
status = Married
occupation = Professional
race = White
```

with:

```text
Support = 0.05
FPR divergence = 0.469
```

### High-FNR Pattern

One of the highly divergent FNR patterns was:

```text
age <= 28
gain = 0
hours/week <= 40
status = Unmarried
```

with:

```text
Support = 0.17
FNR divergence = 0.61
```

The Adult experiments also demonstrate how Shapley-based contribution analysis can be used to determine which items contribute most strongly to the divergence of a pattern.

---

## 15. Performance Results

The execution time of DivExplorer depends primarily on the number of frequent itemsets that must be extracted.

The main performance findings include:

- Higher minimum support thresholds reduce the number of patterns and execution time.
- For all datasets except German Credit, execution time remained below **20 seconds**, even with a minimum support of 0.01.
- The worst-case execution time for German Credit remained below **150 seconds**.
- Computing divergence and statistical significance accounted for less than **7%** of the total execution time.
- Most of the computational cost came from frequent itemset extraction.

Lower support thresholds allow the method to examine smaller subgroups but substantially increase the number of itemsets that must be explored.

---

## 16. Comparison with Slice Finder

**Slice Finder** is one of the closest existing approaches to DivExplorer.

Both methods search for subgroups in which classifier behavior differs from its overall behavior.

However, Slice Finder uses pruning strategies during subgroup exploration.

The paper argues that this can miss important patterns because divergence is **non-monotonic**.

Suppose:

$$
I \subset J
$$

Knowing the divergence of $I$ does not determine the divergence of the more specific itemset $J$.

The divergence of $J$ may be:

- Greater than the divergence of $I$
- Smaller than the divergence of $I$
- Similar to the divergence of $I$

Therefore, a low-divergence simpler pattern does not imply that all of its more specific patterns will also have low divergence.

DivExplorer instead performs complete exploration of all itemsets satisfying the specified minimum support threshold.

---

## 17. Comparison with Individual Prediction Explanations

The paper distinguishes DivExplorer from methods such as:

- LIME
- SHAP
- Anchor

These methods primarily explain **individual predictions**.

DivExplorer instead analyzes the **statistical behavior of a classifier across data subgroups**.

Although both SHAP and DivExplorer use concepts based on Shapley values, their purposes are different.

```text
SHAP
→ Explains how feature values contribute to an individual prediction

DivExplorer
→ Explains how attribute values contribute to subgroup divergence
```

DivExplorer further extends this idea through global item divergence, which measures an item's contribution across different subgroup contexts.

---

## 18. Pattern Redundancy

Exhaustive frequent pattern mining may produce many similar or redundant patterns.

The paper therefore discusses post-exploration pruning to reduce redundant results.

A pattern can be considered redundant when one of its items contributes very little to its divergence.

If removing an item produces only a very small change in divergence, that item may not provide meaningful additional information.

A user-defined threshold $\epsilon$ can be used to determine whether the contribution is sufficiently small for the pattern to be pruned.

This pruning is performed **after exploration**, rather than during frequent itemset extraction, so that potentially important more specific patterns are not missed.

---

## 19. Main Contributions

The paper's main contributions are:

1. **Divergence and Local Item Contribution**  
   The paper defines divergence to quantify differences in classifier behavior across subgroups and uses Shapley values to measure the contribution of individual items.

2. **Global Item Divergence**  
   A generalized Shapley-based measure captures the contribution of an item to divergence across different contexts.

3. **Corrective Attribute Values**  
   The method identifies attribute values that reduce divergence when added to existing patterns.

4. **Bayesian Statistical Significance**  
   A Bayesian approach is introduced to evaluate the statistical significance of observed divergence.

5. **Efficient Automatic Exploration**  
   The paper presents an algorithm for efficiently computing divergence over all sufficiently supported subgroups.

---

## 20. Conclusion

The paper introduces **pattern divergence** as a method for analyzing differences in classifier behavior across data subgroups.

The proposed **DivExplorer** framework combines:

- Frequent pattern mining
- Subgroup performance analysis
- Divergence measures
- Shapley-based contribution analysis
- Corrective-item analysis
- Statistical significance testing

The experiments show that the method can identify subgroups whose classifier behavior differs substantially from overall behavior and can characterize which attribute values contribute to those differences.

The approach is model-agnostic because it relies on classifier outcomes rather than internal model structure.

The authors also suggest that divergence analysis could be extended beyond classification to other data science tasks, such as data preprocessing.