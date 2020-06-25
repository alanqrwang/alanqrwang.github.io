---
title: Linear Regression in Three Ways
date: "2019-04-28T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/linear-regression-three-ways/"
category: "Machine Learning"
tags:
  - "Math"
  - "Probability"
  - "Machine Learning"
description: "An analysis and comparison of linear regression through a risk minimization, probabilistic, and a least-squares lens."
---

![lin_reg.jpg](/media/lin_reg.jpg)

Linear regression (traditionally referred to as least squares) has found many useful applications from all sorts of subdisciplines including machine learning and statistics. Its popularity and ubiquity can be attributed to the fact that many phenomena have mutually linear dependencies and correlations, and the fact that fitting linear models requires a relatively small number of parameters. 

The topic of linear regression is typically taught through the lens of least squares. In this post, I will offer two separate approaches to solving linear regression and argue about its intuition.

## Set Up and Notation
Let $N$ be the number of observations we have and $d$ be the dimension of the data over which we are predicting. We will make a (very safe) assumption that $N \gg d$. 

Let $i \in \{1, 2, ..., N\}$. Linear regression involves finding a vector of parameters $w \in \mathbb{R}^{(d+1) \times 1}$, which fits a line of the form [^1]

$$
y_i = X_i^T w + \epsilon_i
$$ 

where $y_i \in \mathbb{R}$, $X_i \in \mathbb{R}^{(d+1)\times 1}$, and $\epsilon_i \sim \mathcal{N}(0, \sigma^2)$ for some $\sigma^2$.

Typically, we vectorize everything both for ease of notation and to take advantage of cool matrix/vector manipulations (as we will see later). Define $y \in \mathbb{R}^{N \times 1}$, $X \in \mathbb{R}^{N \times (d+1)}$ such that

$$
y = \begin{bmatrix}y_1 \\ y_2 \\ \vdots \\ y_N \end{bmatrix}, X = \begin{bmatrix}X_1^T \\ X_2^T \\ \vdots \\ X_N^T \end{bmatrix}.
$$ 

In this way, we can equivalently write the problem as

$$
y = Xw + \epsilon,
$$

where $\epsilon \in \mathbb{R}^{N \times 1}$ is a vector of i.i.d $\epsilon_i$'s.

How do we find the parameters $w$ that fit the dataset most closely? This post discusses 3 approaches and attempts to draw comparisons between them.

## 1. Empirical Risk Minimization
The simplest and most intuitive formulation is one of minimizing the error [^2] between the observations and our fitted line. This error is precisely

$$
\epsilon = y - Xw.
$$ 

Intuitively, the line that minimizes the error over all the points in the dataset should be the line of best fit. An alternative way of thinking of this is that we are finding a set of parameters $w$ that minimizes the value of the unmodeled effects $\epsilon$.

![residuals.png](/media/residuals.png)
*Linear regression fit for $N=10$ and $d=2$. The vertical line between each point and the fitted line represents the error or residual.*

To this end, let's formulate the solution as an optimization problem with the program:

$$
\text{arg}\min_{w} ||y-Xw||^2 
= \text{arg}\min_w \sum_{i=1}^N ||y_i - X_i^T w||^2 \tag{1}
$$

To solve this, we take the derivative of the objective function with respect to $w$

$$
\begin{eqnarray}
\frac{d}{dw} \Big(||y-Xw||^2\Big) &=& \frac{d}{dw} \Big((y-Xw)^T(y-Xw)\Big) \\
&=& \frac{d}{dw} \Big(y^Ty - 2(Xw)^Ty + w^TX^TXw\Big) \\
&=& 2X^TXw-2X^Ty,
\end{eqnarray}
$$

and set it equal to $0$ to get the expression: 

$$
X^TXw = X^Ty.
$$

If $X^TX$ is full rank (which it usually is), then we obtain the final answer:

$$
w = (X^TX)^{-1}X^Ty.
$$

## 2. Probabilistic
Let's return to our original model for linear regression and think in a more probabilistic lens:

$$
y_i = X_i^Tw + \epsilon.
$$

This model assumes that our observations $y_i$ are noisy versions of a *true* underlying process $X_i^T w$, where the noise is modeled by a Gaussian random variable $\epsilon$ with $0$ mean. In essence, $y_i$ is also a random variable distributed as

$$
y_i \sim \mathcal{N}(w^T X_i, \sigma^2).
$$

We want to find the parameters $w$ that maximize the likelihood $p(y | X;w)$ [^3]. For a given $i \in \{1, 2, ..., N\}$, 

$$
p(y_i | X_i; w) = \frac{1}{\sqrt{2\pi \sigma^2}} \exp \Big\{-\frac{(y_i - X_i^Tw)^2}{2\sigma^2} \Big\}
$$

Crucially, we can leverage the fact that $y_i$ is only dependent on $X_i$ (clearly, an observation should only be dependent on its corresponding underlying factors that caused it).

![gaussian-linreg.png](/media/gaussian-linreg.png)
*Linear regression with $y_i$'s modeled as Gaussian random variables conditioned on $X_i$'s. Every datapoint is thus some realization of a normal random variable centered at the line and with some non-zero variance.*

Let's use this fact when calculating the negative log-likelihood:

$$
\begin{eqnarray}
-\log p(y | X; w) &=& -\log \prod_{i=1}^N p(y_i | X_i; w) = -\sum_{i=1}^N \log p(y_i | X_i; w)\\
 &=& -\sum_{i = 1}^{N} \log\Bigg(\frac{1}{\sqrt{2\pi \sigma^2}} \exp \Big\{-\frac{(y_i - X_i^T w)^2}{2\sigma^2} \Big\}\Bigg) \\
 &=& -\sum_{i = 1}^{N} \Bigg( \log\frac{1}{\sqrt{2\pi \sigma^2}} -\frac{(y_i - X_i^T w)^2}{2\sigma^2}\Bigg) \\
 &=&  -\sum_{i = 1}^{N} \Big( \log\frac{1}{\sqrt{2\pi \sigma^2}}\Big) + \frac{1}{2\sigma^2}\sum_{i=1}^N (y_i - X_i^T w)^2 \\
\end{eqnarray}
$$

Notice that the first summation is not a function of $w$, so it drops out when taking the derivative. In addition, the factor $\frac{1}{2\sigma^2}$ is a constant and doesn't affect the minimization. Thus, when we minimize the negative log-likelihood, we can equivalently write

$$
\text{arg}\min_w\Bigg(-\sum_{i = 1}^{N} \Big( \log\frac{1}{\sqrt{2\pi \sigma^2}}\Big) + \frac{1}{2\sigma^2}\sum_{i=1}^N (y_i - w^T X_i)^2\Bigg) \\
= \text{arg}\min_w \sum_{i=1}^N (y_i - w^T X_i)^2.
$$

So, in the end, the expression we are minimizing is identical to $(1)$ in the previous section!

## 3. Linear Algebra
Let's go back to our original relation in matrix form:

$$y = Xw.$$ 

Since $N \gg d$ (we have far more observations then the dimension of those observations themselves), $X$ is a tall matrix, i.e. it has more rows than columns. That is, the system is *overdetermined*, because we have more equations than unknowns for which to solve. In general, such a system is inconsistent (does not have a solution), so we need to find some approximation that is optimal and allows for a unique solution.

When a system is overdetermined, the natural thing to do is to find the least squares solution. That is, instead of finding $w$, we find $\hat{w}$ such that

$$
X\hat{w} = \hat{y},
$$ 

where $\hat{y}$ is the projection of $y$ onto the column space of $X$, $Col(X)$.

![projection.png](/media/projection.png)

Intuitively, $\hat{w}$ is the best approximation for this overdetermined system because $\hat{w}$ is the vector that is closest to $w$ that is in the span of $X$, which allows the system to be consistent. We will see that this approximation has precisely the same solution as the other two formulations. 

Notice that the vector $X\hat{w} - y$ is orthogonal to $Col(X)$. By the [fundamental theorem of linear algebra](https://en.wikipedia.org/wiki/Fundamental_theorem_of_linear_algebra), this means that $X\hat{w} - y \in Nul(X^T)$. By definition of null space, we equivalently can write

$$
X^T(X\hat{w} - y) = 0 
$$

$$
\implies X^TX\hat{w} = X^Ty
$$

which is exactly the same as our solutions from before!

## Further Discussion
This post presents three (seemingly) different approaches to solving the linear regression. Approach 1 is commonly referred to as empirical risk minimization, which in general involves designing some loss function $\ell$ that is to be minimized. This is a common machine learning method that attempts to minimize the "cost" or "penalty" that the loss function defines. 

Approach 2 at its core involves the concept of maximum likelihood, which is a common technique to solve probabilistic machine learning models. In contrast to Approach 1, we model our predicted variables (in this case $y$) by some probability distribution with parameters $\theta$. We then solve for the optimal $\theta$ that "best explains" the data we observe. In some sense, maximum likelihood can be thought of as fitting a probability distribution over the observed data by tuning parameters, such that the distribution we fit best reflects the data we see. 

Approach 3 is a rather classic approach to solving any overdetermined system, and is typically the method that is taught alongside linear regression. Under the hood, this approach is actually identical to Approach 1, except we leverage the notion of orthogonality of subspaces to find the best approximation instead of taking derivatives directly.

[^1]: This form generalizes to affine regressions (i.e. $X_i^Tw + b_i$ for $b_i \in \mathbb{R})$ by simply appending $1$ to the end of $X \in \mathbb{R}^{d \times 1}$. This is why $d+1$ shows up everywhere.

[^2]: Also commonly referred to as *residuals*.

[^3]: The notation here is subtle and is important to understand. "|" denotes conditional independence as usual, and the variable that follows it is always a random variable. The variable that follows "$;$" denotes parameters of the distribution and is never a random variable, and its what makes this a "likelihood" and not a "probability". Later on, we will be maximizing the distribution with respect to its parameters, so this notation makes it convenient to see what parameters are available to maximize.
