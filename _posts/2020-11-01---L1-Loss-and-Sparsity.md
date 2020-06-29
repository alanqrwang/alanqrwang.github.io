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

In a [previous post](https://alanqrwang.github.io/posts-underdetermined-systems-and-regularization/), I discussed the concept of regularization in the underdetermined, least squares model:

$$
\text{arg}\min_x ||\Psi \theta - y||_2^2,
$$

$$
\text{such that   } \mathcal{R}(\theta) < \epsilon.
$$

In this formulation, the regularization function $$\mathcal{R}(\theta)$$ is designed such that it takes high values for undesirable solutions $\theta$ and low values for desirable solutions $\theta$. The optimal $$\hat{\theta}$$ should minimize the least-squares expression while also keeping the regularization function as low as possible, which can be encoded in the constraint mathematically as being less than some arbitrary positive value $$\epsilon \in [0, \infty)$$. 

As examples, here are two common regularization functions which are so common that they have special names.

### $$\mathcal{R(\theta)} = ||\theta||_2^2$$
If we penalize solutions with high $$\ell_2$$ norm, then we arrive at ridge regression:

$$
\text{arg}\min_\theta ||\Psi \theta - y||_2^2,
$$

$$
\text{such that   } ||\theta||_2^2 < \epsilon.
$$

The nice thing about ridge regression is that it has closed-form solution

$$
\hat{\theta} = (\Psi^T\Psi + \lambda I)^{-1}\Psi^Ty.
$$

Interestingly, it can be seen that the ridge regression solution makes the otherwise rank-deficient matrix $(\Psi^T \Psi)^{-1}$ invertible by adding a constant $\lambda$ to the diagonal entries.

### $$\mathcal{R(\theta)} = ||\theta||_1$$
Alternatively, if we penalize solutions with high $$\ell_1$$ norm, then we arrive at LASSO:

$$
\text{arg}\min_x ||\Psi \theta-y||_2^2,
$$

$$
\text{such that   } ||\theta||_1 < \epsilon.
$$

Specifically, it can be proved that LASSO arrives at the sparse solution with high probability. Amazingly, we can reformulate a highly intractable combinatorial problem as a continuous convex optimization problem and arrive at the same result.

Why does using the L1 norm lead us to a sparse solution? The optimal $$\hat{theta}$$ is the vector which intersects the solution space of the least-squares condition and the solution space of the regularization constraint.
![Sparse representations]({{ site.baseurl }}/images/sparsity_lp.PNG)

The image above shows an example in two dimensions. The line indicates the subspace which satisfies the condition $$\Psi \theta = y$$. The diamond/circle indicates the subspace which satisfies the condition $$||\theta||_p < \epsilon$$. This is commonly referred to as the $$\ell_p$$-norm ball of radius $$\epsilon$$; it follows that larger $$\epsilon$$ would correspond to larger areas (and therefore a larger solution space) of the ball.

We see that for $$p=2$$, the $$\ell_p$$-norm ball takes a circular shape. 
