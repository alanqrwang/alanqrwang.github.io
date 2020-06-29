---
title: Underdetermined Systems and Regularization
date: "2020-01-10T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/underdetermined-systems-and-regularization/"
category: "Signal processing"
tags:
  - "Signal Processing"
description: "An overview of overdetermined and underdetermined systems, the role of regularization, and applications to compressed sensing."
---

## Underdetermined vs. Overdetermined Systems
In classical least squares, we have a set of observation data $y \in \mathbb{R}^M$ and we wish to fit a linear model of the form

$$
y = A x + \epsilon,
$$ 

where $A \in \mathbb{R}^{M \times N}$ and $x \in \mathbb{R}^N$. $\epsilon$ represents any unmodeled effects, including noise.

The optimization we want to solve is 

$$
x^* = \text{arg}\min_x ||Ax - y||_2^2. \tag{1}
$$

In certain cases, $M \gg N$; i.e. $A$ is a tall matrix and we have much more observations than parameters to tune (i.e. an overdetermined system). This is commonly the case in machine learning: for example, $x$ might have a set of $N$ features representing characteristics of a house (e.g., number of bedrooms, number of bathrooms, location of property, etc.), and $y$ represents the price of the house. Typically, we will train on a very large dataset of size $M$, which contains labeled pairs $(x, y)$. In this case, the linear model we are fitting would be overdetermined. In particular, $A^HA$ will be full-rank such that an inverse exists.

Overdetermined systems are so-called because there is more information then we need to find a solution. However, intuitively speaking, there should be one solution that fits all the information best. Because we have so much information, we can be confident that that solution is the "best" amongst the infinite number of solutions that are possible. The problem is still ill-posed in the sense that there is no unique solution, but we can still find "the best" solution. 

On the other hand, underdetermined systems do not enjoy such characteristics. In the underdetermined case, we have less information then we need to find a solution. In these cases, we need to add further structure or constraints ourselves in order to produce a solution at all. 

## Regularization 
Regularization is what enables the adding of constraints, and makes finding solutions to underdetermined systems possible. Let's augment our optimization problem from $(1)$ by adding an additional regularization term:

$$
x^* = \text{arg}\min_x ||Ax - y||_2^2 + \lambda \mathcal{R}(x). 
$$

How does adding this term affect our solutions? One way to view it is as adding an additional cost term. Values of $x$ that cause high values of $\mathcal{R}(x)$ will be penalized, and therefore won't be feasible solutions in this setting. For example, if we define $$\mathcal{R}(x) = \|x\|_2^2$$, then we will be penalizing answers with high $\ell_2$ norms. So, in a sense, a regularization allows us to refine the space of feasible solutions. There is an "art" to designing these regularization functions, because one must have a sense a priori of what to penalize; that is, it is necessary to have an idea of what your solutions *should* look like, even before one obtains the solution!

This ties in nicely to another common interpretation of regularization, and that is as a prior distribution in a Bayesian setting. It can be seen that our model is actually a MAP estimate:

$$
\begin{eqnarray}
\text{arg}\min_x ||Ax - y||_2^2 + \lambda \mathcal{R}(x) 
&=& \text{arg}\min_x e^{||Ax - y||_2^2 + \lambda \mathcal{R}(x)} \\
&=& \text{arg}\max_x -e^{||Ax - y||_2^2} e^{\lambda \mathcal{R}(x)} \\
&\propto& \text{arg}max_x p(y|x)p(x) 
\end{eqnarray}
$$

Viewed this way, our regularization function represents a distribution from which we assume that our solution $x$ is sampled.

## Example of Underdetermined System: Compressed Sensing MRI
In magnetic resonance imaging (MRI), the observations $y \in \mathbb{R}^M$ are assumed to be the output of the forward model

$$
y = \mathcal{F}x + \epsilon,
$$

where $\mathcal{F}$ is the Fourier operator and $x \in \mathbb{R}^N$ is the true image. Notice in this classical case, $M=N$.

In compressed sensing MRI, we replace $\mathcal{F}$ with an undersampled Fourier operator $\mathcal{F}_u \in \mathbb{C}^{M \times N}$, $M<N$, which only performs the Fourier transform on a subset of the domain (i.e., $\mathcal{F}_u$ is $\mathcal{F}$ with some rows removed).

$$
y = \mathcal{F}_u x + \epsilon.
$$

In this model, we can no longer perfectly recover our original image because our Fourier operator is undersampled. That is, the problem now becomes one of recovering $x$ from undersampled measurements $y$, which are related by an underdetermined system $\mathcal{F}_u$, with an additive quantity $\epsilon$ that represents any unmodeled effects (for example, additive noise or measurement noise). This type of model is called an *inverse problem*, in which we would like to obtain the underlying ground truth before it was transformed.

In this case, our matrix $\mathcal{F}_u$ is fat, so $\mathcal{F}_u^H\mathcal{F}_u$ is no longer full-rank. How do we circumvent this issue?

The compressed sensing theory requires that our true image $x$ be sparse in some transform domain. If we assume this to be true, then we can refine our space of feasible solutions by encouraging the solutions we obtain to exhibit transform sparsity.

The way to encode this in our optimization function is to represent the problem as

$$
\text{arg}\min_x ||\Psi x||_0, \tag{1}
$$ 

$$
\text{   subject to   } y = \mathcal{F}_u x.
$$

$\Psi$ is the sparse transform operator and $$\|\cdot\|_0$$ denotes the $\ell_0$ quasi-norm, which represents the number of non-zero elements. Common choices for $\Psi$ include the wavelet transform, contourlet transform, finite-differences, etc.

A way to view the optimization problem in $(1)$ is to rewrite it in a Lagrangian setup:

$$
\text{arg}\min_x ||\mathcal{F}_u x - y||_2^2 + \lambda||\Psi x||_0,
$$

where $\lambda \in \mathbb{R}^+$.
