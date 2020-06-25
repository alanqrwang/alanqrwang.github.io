---
title: Frequency and Time Domain Comparison of the DTFT and DFT
date: "2020-12-01T22:40:32.169Z"
description: A look at the relationship between the DTFT and the DFT from a frequency and time domain perspective.
---

For a discrete-time signal $x[n]$, its DTFT is given by
$$\begin{eqnarray}
X_d(\omega) = \sum_{n=-\infty}^\infty x[n] e^{-j\omega n}.
\end{eqnarray}$$
There are two main issues of the DTFT from a computational standpoint. 
1. The DTFT is calculated over an infinite number of samples $x[n]$, for $n \in \mathbb{Z}$.
2. The DTFT is defined for all continuous values of $\omega \in \mathbb{R}$.

The DFT solves both of these issues by acting on only a finite number of $N$ samples $x[n]$ for $n=0, 1, ..., N-1$, as well as being calculated for discrete frequencies $\omega_k = \frac{2\pi}{N} k$. In this post, we will discuss how both of these facts allows us to arrive at the final expression for the DFT of a discrete-time signal $x[n]$:
$$\begin{eqnarray}
X[k] = \sum_{n=0}^{N-1} x_n e^{-j\frac{2\pi}{N}kn}
\end{eqnarray}$$

## Frequency Domain: The DFT is the *sampled* DTFT of a *finite-length* signal
To solve both of the aforementioned computational issues associated with the DTFT, we must make two restrictions:
1. We must make the original signal $x[n]$ to be finite-length.
2. We must take discrete samples of the continuous space of frequencies $\omega$.

### 1. Finite-length $x[n]$
We would like to convert our infinite-length sequence $x[n]$ to a finite-length sequence $x_N[n]$ defined for $n=0, 1, ..., N-1$. These two sequences can be rigorously related as follows:
$$\begin{eqnarray}
x_N[n] := x[n]p_N[n],
\end{eqnarray}$$
where $p_N[n]$ is a rectangular pulse sequence defined as
$$\begin{eqnarray}
p_N[n] := \begin{cases}1, &0 \leq n \leq N-1 \\ 0, &\text{otherwise}\end{cases}
\end{eqnarray}$$
### 2. Discrete samples of $\omega$
Importantly, the DTFT is $2\pi$-periodic by definition. If we have $N$ samples and an indexing variable $k=0, 1, 2, ..., N-1$, then we can define the DFT from the DTFT with sampling and clever substitution:

$$\begin{eqnarray}
X[k] &:=& X_d\left(\frac{2\pi}{N}k\right) = \sum_{n=-\infty}^\infty x[n] e^{-j\frac{2\pi}{N}k n} \\ \tag{1}
 &=& \sum_{m=-\infty}^\infty \sum_{n = mN}^{mN + N - 1}x_N[n] e^{-j\frac{2\pi}{N}k n} \\
 &=& \sum_{n=0}^{N-1} \left(\sum_{m=-\infty}^\infty x[n-mN]\right) e^{-j\frac{2\pi}{N}k n} \\
 &=& \sum_{n=0}^{N-1} x_N[n] e^{-j\frac{2\pi}{N}k n},
\end{eqnarray}$$ 

where $x_N[n]$ is defined as
$$
x_N[n] := \sum_{m=-\infty}^\infty x[n-mN].
$$
Notice that $x_N[n]$ is the inverse DFT of $X[k] = X_d\left(\frac{2\pi}{N}k\right)$. In addition, $x_N[n]$ is $N$-periodic, since
$$
x_N[n + N] = \sum_{m=-\infty}^\infty x[n+N-mN] = \sum_{m=-\infty}^\infty x[n-mN] = x_N[n].
$$
So, we can see that the process of sampling the DTFT in the frequency domain results in periodicity in the time domain! 

This leads us to two cases:
+ If $x[n] = 0$ outside of the range $n = 0, 1, ..., N-1$, then we have precisely that $X[k] = X_d(\frac{2\pi}{N}k)$.
+ If $x[n]$ has length greater than $N$ or is of infinite length, then we are forced to make $x[n]$ finite (as discuessed in the previous subsection) so that $X[k] = \sum_{n=0}^{N-1} x_N[n] e^{-j \frac{2\pi}{N}kn}$.

In practice, most signals are finite; for example, all images and speech signals are finite.
Why does this periodicity arise? Similar to how [sampling in the time domain results in periodicity in the frequency domain](https://www.alanqwang.com/posts/sampling-reconstruction-and-the-nyquist-rate/), we are losing information during the sampling process. In particular, we are selecting information at discrete frequencies in the interval $[0, 2\pi]$, and increasing the space between consecutive frequencies.

## Time Domain: Linear vs. Circular Convolution
You may have heard that the DTFT corresponds to linear convolution in the time domain, while the DFT corresponds to circular convolution in the time domain. How does the circularity arise?

$$\begin{eqnarray}
X_d(\omega) = \sum_{n=-\infty}^\infty x[n] e^{-j\omega n}.
\end{eqnarray}$$
$$\begin{eqnarray}
X[k] = \sum_{n=0}^{N-1} x_n e^{-j\frac{2\pi}{N}kn}
\end{eqnarray}$$
The reason lies in the periodicity that arises due to the act of sampling in the frequency domain.
