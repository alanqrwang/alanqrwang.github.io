---
title: Foundations of Magnetic Resonance Imaging
date: "2019-09-09T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/mr-imaging/"
category: "Signal Processing"
tags:
  - "Signal Processing"
  - "Math"
description: "Connecting the physics of nuclear magnetic resonance with MRI."
---

## Nuclear Magnetic Resonance (NMR)
The body is comprised of many hydrogen atoms which consist of a single proton. When subjected to a constant magnetic field $\textbf{B}_0 = B_0 \textbf{z}$ whose direction is in the longitudinal axis $\textbf{z}$, the magnetic moment of the proton precesses around the longitudinal axis at a frequency known as the *Larmor frequency*:
$$
\omega = |\gamma| B_0,
$$
where $\gamma$ is the [gyromagnetic ratio](https://en.wikipedia.org/wiki/Gyromagnetic_ratio).

This precession can be modeled as a complex exponential if we define the transverse plane on which the magnetic moment precesses as a complex plane with transverse components:
$$
\textbf{m}_\perp = m_x + im_y = m_\perp e^{-i \omega t}
$$

Since the spin generates its own magnetic field $\textbf{b}$, this sinusoidal spin induces an electromotive force in a conductor loop that is positioned near the proton due to Faraday's Law:
$$
s = -\frac{d\Phi_b}{dt} = i \omega \Phi_b
$$
The flux $\Phi_b$ depends on the coil sensitivity function $c(\textbf{r})$ and the magnetic moment $m_\perp$. Altogether, the voltage signal through the conductor loop is
$$
s = i \omega c(\textbf{r}) \textbf{m}_\perp = i \omega c(\textbf{r}) m_\perp e^{-i \omega t}.
$$

## Magnetic Resonance Imaging (MRI)
MRI is performed by measuring the voltage signal through conductor loops which are induced by the longitudinal precession of the hydrogen protons in the body. If we think about how this can be done in practice, we immediately run into several issues. How do we measure the signal from many spins simultaneously in the body? Clearly, we cannot put one conductor loop for each individual proton. And if we measure all spins simultaneously (essentially aggregating them together), how do we then spatially localize the spins to reconstruct the image? 

If there was a way that we could differentiate each proton's signal by its location in space, that would be a good starting point to being able to separate signals spatially. To that end, let's define
$$
\textbf{m}_\perp := \textbf{m}_\perp(\textbf{r}),
$$
$$
\text{     such that     } \textbf{m}_\perp(\textbf{r}) = m_\perp(\textbf{r})e^{i\omega(\textbf{r})t},
$$
At the same time, if we measure the signals from all the spins within a certain area, we would measure the sum of all spins in that area:
$$
s = \int i \omega(\textbf{r}) c(\textbf{r}) \textbf{m}_\perp(\textbf{r}) d\textbf{r}
$$

The question is, how do we resolve between different $\textbf{m}_\perp(\textbf{r})$ at different points in space, while still using a single coil to measure a sum of all spins in that space? The answer is at the core of MR imaging.

## Gradient Field
Suppose we introduce a magnetic gradient field $\textbf{G}$ that varies linearly with space so that the total magnetic field in the $x$-direction is
$$
B(\textbf{r}) = B_0 + \textbf{G} \cdot \textbf{r}.
$$

Now, the precession frequency is
$$
\omega = \gamma(B_0 +\textbf{G} \cdot \textbf{r}) = \omega_0 + \gamma\textbf{G} \cdot \textbf{r}.
$$

Let's incorporate this gradient field into our signal $s$:
$$
\begin{aligned}
s &= \int i (\omega_0 + \gamma\textbf{G} \cdot \textbf{r}) c(\textbf{r}) m_\perp(\textbf{r}) e^{-i (\omega_0 + \gamma\textbf{G} \cdot \textbf{r})t} d\textbf{r} \\
  &= ie^{-i \omega_0 t}\int (\omega_0 + \gamma\textbf{G} \cdot \textbf{r}) c(\textbf{r}) m_\perp(\textbf{r}) e^{-i \gamma\textbf{G} \cdot \textbf{r}t} d\textbf{r} \\
  &\approx ie^{-i \omega_0 t}\int \omega_0 c(\textbf{r}) m_\perp(\textbf{r}) e^{-i \gamma\textbf{G} \cdot \textbf{r}t} d\textbf{r} \\

\end{aligned}
$$

In the last step, we make a practical simplification and say that $\omega_0 + \gamma \textbf{G} \cdot \textbf{r} \approx \omega_0$ (usually, $\omega_0$ will be on the order of MHz and $\gamma \textbf{G} \cdot \textbf{r}$ will be on the order of kHz). If we define an absorbing factor $P = \omega_0 c(\textbf{r})$, then we arrive at 
$$
s = Pe^{-i \omega_0 t} \int m_\perp(\textbf{r}) e^{-i \gamma \textbf{G} \cdot \textbf{r} t} d\textbf{r}.
$$

## Introducing $\textbf{k}$-space
Let's make one final substitution: we will define a variable 
$$
\textbf{k}(t) = \frac{\gamma}{2\pi} \textbf{G} t.
$$

Now, our signal is
$$
s(t) = Pe^{-i \omega_0 t} \int m_\perp(\textbf{r}) e^{-i 2 \pi \textbf{k} \cdot \textbf{r}} d\textbf{r}.
$$

Finally, we have arrived at an expression that is composed of the Fourier transform of our signal of interest, $m_\perp$, with an arbitrary prefactor $P$ and an arbitrary modulation term $e^{-i\omega_0 t}$. Since $\omega_0$ is known beforehand, we can undo the effect of both of these quantities as follows:
$$
\frac{e^{i\omega_0 t}}{P} s(t) := s(\textbf{k}) = \int m_\perp(\textbf{r}) e^{-i 2 \pi \textbf{k} \cdot \textbf{r}} d\textbf{r} = \mathcal{F}\{m_\perp(r)\}
$$

We can see that $s(\textbf{k})$ and $m_\perp(\textbf{r})$ form a Fourier transform pair. Under Nyquist conditions, the recovery of images from samples collected in $\textbf{k}$-space is mathematically guaranteed. The bottom line is that in order to collect MRI images, we can sample points from $\textbf{k}$-space and then take the inverse Fourier transform to perform reconstruction!

It is important to realize that $\textbf{k}$-space does not describe any notion of physical space, even though we can represent its samples in a grid (by using the FFT on an image, for example). 