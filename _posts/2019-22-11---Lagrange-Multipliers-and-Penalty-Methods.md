---
title: Lagrange Multipliers and Penalty Methods
date: "2019-11-22T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/lagrange-multipliers-and-penalty-methods/"
category: "Optimization"
tags:
  - "Optimization"
  - "Math"
description: "An intuition behind Lagrange multipliers, penalty methods, and augmented Lagrangian methods."
published: false
---

## Lagrange Multipliers
Suppose we are solving the constrained problem for $x \in \mathbb{R}^n$:

$$
x^* = \min f(x)  \tag{1}
$$

$$
\text{   such that   } c_i(x) = 0,  i = 1, 2, ..., L.
$$

The solution $\hat{x}$ for which we are solving necessarily lies in the intersection of the subspaces spanned by the function $f(x)$ and the constraints $c_i(x)$. 

Imagine that we walk along a set of points satisfying $c_i(x) = 0$. At every point $x$, we have a set of allowable directions along which we can move that enables us to stay on the constraints; after all, the optimal solution we seek must lie somewhere on these constraints.

Note that the gradients of $f(x_0)$ and $c_i(x_0)$ must be pointing in the same direction at the point $x_0$ in order for $x_0 = x^*$. Thus, we introduce a set of scalars $\{\lambda_i\}_{i=1}^L$ such that

$$
\nabla f(x) = \sum_{i=1}^L \lambda_i \nabla c_i(x).
$$

In addition to the $L$ constraints $c_i(x) = 0$, we can solve this system of $n+L$ equations for $n+L$ unknowns.

This system of equations can be written concisely if we introduce an auxiliary function

$$
\mathcal{L}(x, \lambda_1, ..., \lambda_L) = f(x) - \sum_{i=1}^L \lambda_i c_i(x)
$$

and solve for

$$
\nabla_{x, \lambda_1, ..., \lambda_L} \mathcal{L}(x, \lambda_1, ..., \lambda_L) = 0 \iff \begin{cases}\nabla_x f(x) = \sum_{i=1}^L \lambda_i \nabla_x c_i(x) \\ c_1(x) = 0 \\ ... \\ c_L(x) = 0. \end{cases}
$$

## Penalty Methods
An alternative way to solve Equation $(1)$ is to reformulate the problem as a series of unconstrained minimization problems:

$$
    \min \Phi_k(x) = f(x) + \frac{\mu_k}{2} \sum_{i \in I} c_i(x)^2.
$$

We can see that large values of $c_i(x)$ is penalized by the second term.

In each iteration $k$, we increase the penalty coefficient $\mu_k$ (in order to get more accurate solutions), solve the unconstrained problem, and use the solution as the initial guess for the next iteration (this applies if we are using some kind of step minimization algorithm to solve the unconstrained problem). Solutions will eventually converge to the solution of the original constrained problem.

## Augmented Lagrangian Method
Augmented Lagrangian methods are similar to penalty methods in that they replace a constrained optimization problem by a series of unconstrained problems and add a penalty term to the objective. The difference is that the augmented Lagrangian methods adds another term that is designed to mimic a Lagrange multiplier.

Solving the optimization problem given in Equation $(1)$, this method formulates it as an unconstrained problem of the following form:

$$
\min \Phi_k(x) = f(x) + \frac{\mu_k}{2}\sum_{i\in I} c_i(x)^2 + \sum_{i\in I} \lambda_i c_i(x)
$$

In addition to updating $\mu_k$ as before, $\lambda$ is also updated as

$$
\lambda_i \leftarrow \lambda_i + \mu_k c_i(x_k).
$$

The main advantage of this method over traditional penalty methods is that it is no longer requires to take $\mu_k \rightarrow \infty$ because of the presence of the Lagrange multiplier term.
