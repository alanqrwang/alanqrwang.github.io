---
title: Probabilistic Models for Binary and Multiclass Classification 
date: "2019-09-02T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/probabilistic-models-for-binary-and-multiclass-classification/"
category: "Machine Learning"
tags:
  - "Machine Learning"
  - "Math"
description: "Notes on binary and multiclass classification from a probabilistic formulation."
---
How do we approach classification from a probabilistic standpoint?

Suppose we have a dataset $\mathcal{D} = \{(x_i, y_i)\}_{i=1}^N$, where $x_i \in \mathbb{R}^d$ represents the features and $y_i \in \mathbb{Z}$ represents the class that $x_i$ is in. Invariably, we will want to define a distribution for $p(y_i | x_i)$, figure out the proper parameters that we can tune for $p(y_i|x_i)$, and perform maximum likelihood estimation on those parameters. How do we define the distribution and its parameters for both the binary and multiclass case?

# Binary Classification
I will present the case of binary classification by defining $y_i \in\{0, 1\}$, but note that there are other definitions that lead to different distributions (see my post on [general supervised learning](alanqwang.com/posts/general-framework-for-supervised-learning/#logistic-regression) for another example).

In this setting, it is very natural to model the conditional as a Bernoulli distribution, which has a single tunable parameter $p$. Formally, if a random variable $z$ has a Bernoulli distribution, then $z\sim \text{Ber}(p)$ and its pmf is
$$
p(z) = \begin{cases}p , &z = 1 \\ 1-p, &z = 0.\end{cases}
$$
If we allow $p(y_i|x_i) = \text{Ber}(p)$ where we choose to learn $p$, we run into the issue where we cannot incorporate $x_i$ into the distribution. Clearly, we would like our distribution to be dependent on $x_i$ so that we can make predictions of the label $y_i$ based on the given realization of $x_i$. In order to include $x_i$ into the distribution, we will take the approach given by [generalized linear models](https://en.wikipedia.org/wiki/Generalized_linear_model) and define 
$$p = \sigma(w^T x_i),$$
so that our conditional distribution is now
$$
p_w(y_i | x_i) = \begin{cases}\sigma(w^Tx_i) , &y_i = 1 \\ 1-\sigma(w^Tx_i), &y_i = 0\end{cases}
$$
or more concisely,
$$
p_w(y_i | x_i) = \sigma(w^T x_i)^{y_i} (1-\sigma(w^T x_i))^{1-y_i}.
$$

Essentially, what we have done is to reparameterize our distribution to introduce a larger parameter set $w$, which now weights all of the elements in our feature $x_i$ (similar to how is normally done in linear regression). However, to ensure that the value is still a valid probability, we "link" the linear intermediary with the sigmoid function $\sigma$ to squash the values back into the range $[0, 1]$.

To find the optimal parameters, we can maximize the likelihood of the distribution with respect to the parameters $w$:
$$
\begin{aligned}
\argmax_w p_w(y_1, ..., y_{N}|x_1, ..., x_{N}) &= \argmax_w \prod_{i=1}^{N} p_w(y_i | x_i) \\
&= \argmin_w -\sum_{i=1}^{N} \log p_w(y_i | x_i) \\
&= \argmin_w \sum_{i=1}^N \log\sigma(w^T x)^{y_i} (1-\sigma(w^T x))^{1-y_i} \\
&= \argmin_w \sum_{i=1}^N \big[ y_i \log\sigma(w^T x) + (1-y_i)\log(1-\sigma(w^T x))^{1-y_i}) \big]
\end{aligned}
$$
From this, we see that the cross-entropy loss is actually a direct result of the Bernoulli distribution.

# Multiclass Classification
How do we extend this formulation to cases where we have more than two classes from which to choose? In this case, we define $y_i \in \{1, 2, ..., C\}$, where $C$ is the total number of classes. There are three problems we must deal with:

## 1. We can't use a Bernoulli distribution anymore.
Clearly, we can no longer use a Bernoulli distribution to model $p(y_i|x_i)$ because $y_i$ is no longer binary. Instead, we can generalize the Bernoulli distribution to the *categorical* distribution, denoted $\text{Cat}(\pi)$ where $\pi \in [0,1]^C$ and $\pi_k$ represents the probability of class $k$. Formally, if we suppose $z \sim \text{Cat}(\pi)$, then its pmf is
$$
p(z) = \begin{cases} \pi_1, &z = 1 \\ \pi_2, &z=2 \\ ... \\ \pi_C, &z=C.\end{cases}
$$

## 2. We can't use $\sigma$ anymore.
A second problem that arises is the fact that we cannot use $\sigma$ as our fitting function anymore, because simply applying a sigmoid to a vector of $C$ values does not provide a probability distribution. Whereas before we could apply $\sigma$ to one outcome and then define the other outcome to be $1-\sigma$, now we need a new function that squashes all values between $0$ and $1$, but that also forces all values to sum to $1$. This new function will be the softmax function, defined as
$$
\text{softmax}(z) := \begin{bmatrix}
\frac{\exp(z_1)}{\sum_{j=1}^C \exp(z_j)} \\ 
\frac{\exp(z_2)}{\sum_{j=1}^C \exp(z_j)} \\ 
... \\
\frac{\exp(z_C)}{\sum_{j=1}^C \exp(z_j)} \\ 
\end{bmatrix},
$$
where $z$ is a vector of real values.

## 3. We can't use a single vector of parameters $w$ anymore.
A third and final problem is that we can no longer use the same vector of weights $w$ to predict across all $C$ classes. Whereas in the binary case, we could get away with one vector of weights to weight each element of the feature $x_i$, now we need to augment our set of parameters so that we have $C$ different weight vectors $w$. For a specific realization $y_i = k$,  we can model $y$ as follows:
$$
p(y_i = k|x_i) = \text{softmax}(w_k^T x_i) = \frac{\exp(w_k^Tx_i)}{\sum_{j=1}^C \exp(w_j^Tx_i)},
$$

For a more concise notation, we can define a weight matrix that contains all $C$ weight vectors in its rows:
$$
W = \begin{bmatrix}
\text{---} & w_1^T & \text{---}\\
\text{---} & w_2^T & \text{---}\\
&...& \\
\text{---}& w_C^T & \text{---}\\
\end{bmatrix}
$$

## The Final Solution
Armed with the above three solutions, we can reparameterize our original parameters of the categorical distribution, $\pi$, by the previously-defined matrix of weights $W$ as 
$$
\pi = \text{softmax}(Wx_i)
$$
and then define $y_i$ as a categorical distribution
$$
y_i \sim \text{Cat}(\text{softmax}(Wx_i)).
$$
Now, $p(y_i | x_i)$ can be written
$$
p_W(y_i | x_i) = 
\begin{cases}
    \text{softmax}(w_1^Tx_i), &y_i = 1 \\
    \text{softmax}(w_2^Tx_i), &y_i = 2 \\
    ... \\
    \text{softmax}(w_{C}^Tx_i), &y_i = C 
\end{cases}
$$
or even more concisely,
$$
p_W(y_i|x_i) = \prod_{k=1}^C \text{softmax}(w_k^T x_i) ^ {\mathbb{1}_{\{y_i=k\}}}
$$
where $\mathbb{1}_{\{y_i=k\}}$ is $1$ if $y_i=k$ and $0$ otherwise.

Maximizing the log-likelihood leads us to
$$
\begin{aligned}
\argmax_W p_w(y_1, ..., y_{N}|x_1, ..., x_{N}) &= \argmax_W \prod_{i=1}^{N} p_W(y_i | x_i) \\
&= \argmin_W -\sum_{i=1}^{N} \log p_W(y_i | x_i) \\
&= \argmin_W -\sum_{i=1}^{N} \log \prod_{k=1}^C \text{softmax}(w_k^T x_i) ^ {\mathbb{1}_{\{y_i=k\}}} \\
&= \argmin_W -\sum_{i=1}^{N} \sum_{k=1}^C \log \text{softmax}(w_k^T x_i) ^ {\mathbb{1}_{\{y_i=k\}}} \\
&= \argmin_W -\sum_{i=1}^{N} \sum_{k=1}^C {\mathbb{1}_{\{y_i=k\}}} \log \text{softmax}(w_k^T x_i) \\
\end{aligned}
$$