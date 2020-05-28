---
title: Sampling, Reconstruction, and the Nyquist Rate
date: "2019-09-09T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/sampling-reconstruction-and-the-nyquist-rate/"
category: "Signal Processing"
tags:
  - "Signal Processing"
  - "Math"
description: "Basic overview of the sampling theorem and the Nyquist Rate."
---

## Sampling in the Time Domain
Let $x_a(t)$ represent a continuous-time signal. We can sample the signal at a period $T$ to obtain a discrete-time signal of the following form:
$$
x[n] := x_a(nT)
$$   

For example, given $x_a(t) = e^{j\Omega_0 t}$, the result of sampling would be
$$
x[n] = e^{j\Omega_0 Tn} = e^{j\omega_0 n},
$$
where we defined a new frequency variable $\omega_0 := \Omega_0 T$.

## Sampling in the Frequency Domain
Suppose $x_a(t)$ has Fourier transform $X_a(\Omega)$ and $x[n] = x_a(nT)$ has DTFT $X_d(\omega)$. The transforms are then related by the following equation:
$$
X_d(\omega) = \frac{1}{T}\sum_{k=-\infty}^\infty X_a\left(\frac{\omega - 2\pi k}{T}\right) \tag{1}
$$
We can prove this by expressing $x[n]$ in the form of a DTFT:
$$
\begin{aligned}
x[n] &= x(nT) \\
 &= \frac{1}{2\pi} \int_\mathbb{R} X_a(\Omega) e^{j\Omega (nT)} d\Omega \\ 
 &= \frac{1}{2\pi} \int_\mathbb{R} X_a\left(\frac{\omega}{T}\right) e^{j\frac{\omega}{T} (nT)} d\frac{\omega}{T} \\ 
 &= \frac{1}{2\pi T} \int_\mathbb{R} X_a\left(\frac{\omega}{T}\right) e^{j\omega n} d\omega \\ 
 &= \frac{1}{2\pi T} \sum_{k=-\infty}^\infty \int_0^{2\pi} X_a\left(\frac{\omega-2\pi k}{T}\right) e^{j\omega n} d\omega \\ 
 &= \frac{1}{2\pi} \int_0^{2\pi} \left[ \frac{1}{T}\sum_{k=-\infty}^\infty X_a\left(\frac{\omega-2\pi k}{T}\right) \right] e^{j\omega n} d\omega \\ 
 &:= \frac{1}{2\pi} \int_0^{2\pi} X_d(\omega) e^{j\omega n} d\omega \\ 
\end{aligned}
$$

What $(1)$ tells us is that the DTFT of the sampled signal has the same shape as the CTFT of the original, analog signal, scaled by a factor $\frac{1}{T}$ and *replicated* along the $\omega$ axis at intervals of $2\pi$. The replication arises from the fact that some $\omega$ value will result in a value of $X_a\left(\frac{\omega-2\pi k }{T}\right)$ having some non-zero value. Since we are summing over all $k$, the resulting signal will be the superposition of all replicas which repeat every $2\pi$.

Why does the act of sampling in the time domain result in periodicity in the frequency domain? In a simplified sense, it is the price we pay for the continuous information that we are losing during the sampling process. I like to think of this as analogous to the reverse case: for continuous periodic signals, we know that the frequency domain consists of discrete Fourier coefficients. In that setting, discretization in one domain corresponds to periodicity in the other domain. By the same token, by discretizing the time domain, periodicity arises in the frequency domain (the DFT is another example of discretization in the *frequency* domain causing periodicity and discretization in the *time* domain).  

## Reconstruction and Aliasing
If the DTFT $X_d(\omega)$ is bandlimited such that $X_d(\omega)$ takes non-zero value only over $\omega \in [-\pi, \pi]$, then it is possible to reconstruct the original signal from its samples. Since the shape of the CTFT is retained in the DTFT, reconstructing the original, analog signal is simple: apply a low-pass filter with cutoff $\omega_L = \omega_{max}$, where $\omega_{max}$ is the maximum frequency of the discrete signal, and additionally rescale by a factor $T$. This ideal digital-to-analog filter $G(\Omega)$ would look like the following in the frequency domain:
$$
G(\Omega) = \begin{cases}T, & |\Omega| \leq \frac{\pi}{T} \\ 0, &\text{otherwise.}\end{cases}
$$

If the DTFT $X_d(\omega)$ is not bandlimited, then perfect reconstruction is no longer possible. That is, a major problem arises due to the fact that the DTFT of the sampled signal $x[n]$ is $2\pi$-periodic. If the domain over which an individual replica takes non-zero value exceeds a length of $2\pi$, then the replica will interfere with the neighboring replica when summed together. To be more explicit, if the maximum frequency $\omega_{max}$ exceeds $\pi$, then aliasing will occur. Equivalently, aliasing will occur if
$$
\omega_{max} > \pi \implies \Omega_{max} T > \pi \implies T > \frac{\pi}{\Omega_{max}} \implies \frac{1}{f} > \frac{1}{2f_{max}} \implies f < 2f_{max}
$$
Thus, we arrive at the Nyquist rate, which states that perfect reconstruction of the sampled signal is possible if sampling is done at twice the maximum frequency of the original signal:
$$
f > 2f_{max}.
$$