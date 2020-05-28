---
title: Implemention of Magnetic Resonance Imaging
date: "2019-11-04T22:40:32.169Z"
template: "post"
draft: true
slug: "/posts/mri-implementation/"
category: "Signal Processing"
tags:
  - "Signal Processing"
  - "Math"
description: "Connecting the physics of nuclear magnetic resonance with MRI."
---

## Collecting $\textbf{k}$-space samples
In my previous post on the [foundations of MRI](https://www.alanqwang.com/posts/mr-imaging/), we discussed the idea of $\textbf{k}$-space and how it arises naturally from linear phase encoding using gradient fields. In particular, the quantity $\textbf{k}$ is a function of time and the value of the gradient field:
$$
\textbf{k}(t) = \frac{\gamma}{2\pi} \textbf{G}t.
$$

In this post, I would like to build a picture of how to implement MRI in practice. Previously, we discussed how with the use of a strong magnetic field $B_0$, it is possible to cause magnetic moment precession of hydrogen atoms in the body, which can induce an electromotive force on a nearby conducting loop. The signal through this conducting loop can be detected and localized to specific areas in the field of view with the help of linearly-varying gradient fields. We showed that MR images can be reconstructed, with introduction of $\textbf{k}$-space, using an inverse Fourier transform.


## Radio-Frequency (RF) Excitation
With a base magnetic field $B_0$ and a gradient field $\textbf{G}$, we can detect magnetic moment precession and perform localization for image reconstruction. However, 

