---
title: A General Framework for Classification Learning
date: "2019-08-19T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/general-framework-for-supervised-learning/"
category: "Machine Learning"
tags:
  - "Machine Learning"
  - "Math"
description: "An expression encompassing all supervised classification learning models."
published: false
---

Full credit to Alexander Schwing and Matus Telgarsky, from Spring 2017 UIUC CS 446 lectures.

In this post, I will show that the following objective is general for many supervised classification learning models, including linear regression, logistic regression, support vector machines (SVMs), multiclass classification, and neural networks. 

# The Claim
Given a labeled dataset $\mathcal{D} = \{(x_i, y_i)\}_{i=1}^N$, predicted label $\hat{y}$, and temperature $\epsilon \in \mathbb{R}^+$, I claim that
$$
\argmin_\theta \sum_{i = 1}^{|\mathcal{D}|} \epsilon \log \sum_{\hat{y}} \exp \frac{L(y_i, \hat{y}) + F_\theta(x_i, \hat{y})}{\epsilon} - F_\theta(x_i, y_i) \tag{1}
$$
is general for any supervised learning objective.

# Linear Regression
## Binary Classification
Assume that $y_i \in \{-1, 1\}$. The objective function for linear regression can be rewritten as:
$$
\begin{aligned}
\mathcal{L} &= \sum_{i = 1}^{|\mathcal{D}|}\frac{1}{2} (y_i - w^T x_i)^2 \\
 &=\sum_{i = 1}^{|\mathcal{D}|} \frac{1}{2} (1 - y_i w^T x_i)^2
\end{aligned}
$$
This is a specific case of Equation $(1)$ 

# Logistic Regression
$$
\begin{aligned}
&p_w(y = 1 | x) = \frac{1}{1 + \exp (-w^T x)} \\
&p_w(y = -1 | x) = 1 - p(y = 1 | x) = \frac{1}{1 + \exp (w^T x)}
\end{aligned}
$$

Knowing that $y$ can only take value $\pm 1$, we can actually combine these two expressions like so:
$$
p_w(y|x) = \frac{1}{1+\exp(-y w^T x)}
$$

Now just as before, we can maximize the likelihood of the distribution with respect to the parameters $w$:
$$
\begin{aligned}
\argmax_w p_w(y_1, ..., y_{N}|x_1, ..., x_{N}) &= \argmax_w \prod_{i=1}^{N} p_w(y_i | x_i) \\
&= \argmin_w -\sum_{i=1}^{N} \log p_w(y_i | x_i) \\
&= \argmin_w \sum_{i=1}^N \log(1+\exp(-y_i w^T x_i))
\end{aligned}
$$

# Support Vector Machines
