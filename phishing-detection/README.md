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

We formulate this as a learning problem by specifying the available observations, the target function, and the criteria for evaluating the learned characterization, without prescribing a particular algorithm or feature set [1, §§1.1–1.2]. The definitions below specify the proposed study rather than assert experimental findings.

## 6. References
[1] Tom M. Mitchell. 1997. *Machine Learning*. McGraw-Hill. Chapter 1, especially §§1.1–1.2. ISBN 978-0-07-042807-2. [Author’s textbook page](https://www.cs.cmu.edu/~tom/mlbook.html).
