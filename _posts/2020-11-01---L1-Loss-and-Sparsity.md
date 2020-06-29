---
title: L1 Loss and Sparse Solutions
date: "2020-01-11T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/l1-loss-and-sparse-solutions/"
category: "signal processing"
tags:
  - "Signal Processing"
description: "An overview of overdetermined and underdetermined systems, the role of regularization, and applications to compressed sensing."
---
## Regularization 
In a [previous post](https://alanqrwang.github.io/posts-underdetermined-systems-and-regularization/), I discussed the concept of regularization in the underdetermined, least squares model:

$$
x^* = \text{arg}\min_x ||\Psi x - y||_2^2 + \lambda \mathcal{R}(x). 
$$

Here are two common regularization functions which are so common that they have special names:

+ If we penalize solutions with high L2 norm, then we arrive at ridge regression:

$$
\hat{x} = \text{arg}\min_x ||\Psi x - y||_2^2 
$$

$$
\text{such that   } ||x||_2^2 < \epsilon.
$$

The nice thing about ridge regression is that it has closed-form solution

$$
\hat{x} = (\Psi^T\Psi + \lambda I)^{-1}\Psi^Hy.
$$

Interestingly, it can be seen that the ridge regression solution makes the otherwise rank-deficient matrix $(\Psi^T \Psi)^{-1}$ invertible by adding a constant $\lambda$ to the diagonal entries.

+ Alternatively, if we penalize solutions with high L1 norm, then we arrive at LASSO:

$$
\hat{x} = \text{arg}\min_x ||\Psi x-y||_2^2 .
$$

$$
\text{such that   } ||x||_1 < \epsilon.
$$

Specifically, it can be proved that LASSO arrives at the sparse solution with high probability. Amazingly, we can reformulate a highly intractable combinatorial problem as a continuous convex optimization problem and arrive at the same result.

Why does using the L1 norm lead us to a sparse solution? In a graphical sense, the optimal $$\theta^*$$ which solves the contrained optimization problem in $(1)$ is the $$\theta^*$$ that has the smallest norm and that also intersects the subspace formed by the constraints. Or, put another way, the optimal $$\theta^*$$ is the minimum element in the intersection between the set of norms and the set of solutions of the constraints.
![Sparse representations]({{ site.baseurl }}/images/sparsity_lp.PNG)
