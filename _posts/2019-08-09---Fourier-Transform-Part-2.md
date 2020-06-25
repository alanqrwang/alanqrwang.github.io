---
title: The Fourier Transform as a Projection Onto Subspaces
date: "2019-08-08T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/fourier-transform-part-2/"
category: "Signal Processing"
tags:
  - "Signal Processing"
  - "Math"
description: "Part 2 of a two-part series on Fourier transforms. In this post, as a projection onto a space of eigenfunctions."
---
In [Part 1](https://alanqrwang.github.io/posts-fourier-transform-part-1/), I introduced the Fourier Transform as the "limit" of the Fourier series. We found that extending the period of a periodic function in the time domain resulted in a narrowing of the distance between consecutive Fourier coefficients in the frequency domain. By extending the period to infinity (thus rendering the function aperiodic), the distance between Fourier coefficients became so small that we eventually arrived at a continuous function in the frequency domain, this function being the Fourier transform.

In this post, I will introduce the Fourier Transform as a projection onto the subspace of complex exponentials. It can be shown that the Fourier series is, in fact, a projection onto vectors of the form $e^{jn \omega_0 t}$, where $n \in \mathbb{Z}$. The Fourier transform has a similar reasoning but in a continuous sense, with basis $e^{j \omega t}$, where $\omega \in \mathbb{R}$. 

First, I will cover background on vectors and subspaces that we will need. Then, I will motivate use of complex exponentials as an alternative basis, before introducing the main idea of the post. Incidentally, the motivation of the basis applies to concepts in Part 1, although it becomes much clearer when introduced here.

## Background: Vectors and Subspaces

Vectorizing signals provides an alternative lens by which to view the Fourier Transform. You may be familiar with the 3-dimensional Euclidian space $\mathbb{R}^3$, composed of the orthogonal basis functions $i$, $j$, and $k$. The term "orthogonal" is defined in terms of the inner product of this space, which is the familiar dot product:

$$
\langle x, y \rangle = x \cdot y = x_1y_1 + x_2y_2 + x_3y_3.
$$

In general, two vectors are orthogonal if the inner product between the two vectors is $0$,

$$
\langle x, y \rangle = 0
$$

The orthogonality of these basis functions is important; it's what allows us to express any vector that lives in this space as a weighted linear sum of these basis functions:

$$
v = v_1 i + v_2 j + v_3 k.
$$

If two vectors are orthogonal, you may think intuitively that there is no information of one vector in the "direction" of the other vector. Furthermore, decomposing a space into its basis functions completely "characterizes" the degrees of freedom inherent in that space. That is, the basis functions are the most elemental vectors within that space by which all other vectors can be built. 

If an inner product of $0$ between two vectors implies no mutual information in the direction of the vectors, you may surmise that a non-zero value of the inner product leads to some notion of "overlap" or "projection" between the two vectors. Indeed, we may find the projection of one vector $x$ in the direction of the other vector $y$, $proj_y : \mathbb{R}^n \rightarrow \mathbb{R}$, as proportional to the inner product of the two vectors:

$$
proj_y(x) = \frac{\langle x, y \rangle}{||y||},
$$

where the denominator $$\|\|y\|\|$$ is a normalizing factor. 

## Aside: Some properties of the vector space of functions
Now, I will outline some properties of the vector space of functions that we will need later.
Let $S$ be defined as the space of continuous-time functions. Then,
+ S has the inner product

$$
\langle x, y \rangle = \int_{-\infty}^\infty x(t) y^*(t) dt,
$$

where $^*$ denotes the complex conjugate.
+ The space of complex exponentials $\{e^{j \omega t}\}_{\omega=-\infty}^\infty$ is an orthogonal basis for $S$. In particular, for $\omega_1, \omega_2 \in \mathbb{R}$,

$$
\langle e^{j\omega_1 t}, e^{j\omega_2 t} \rangle =  \int_{-\infty}^\infty e^{j\omega_1 t} e^{-j\omega_2 t}dt = 2\pi \delta(\omega_1 - \omega_2)
$$  

I won't prove this, but [this link](https://math.stackexchange.com/questions/2340094/why-frac12-pi-int-infty-inftyeiwt-xdw-is-the-dirac-delta-func) explains it well. Regardless, it suffices to understand that the space of complex exponentials is a valid orthogonal basis over the space of continuous functions, but that also encodes frequency information within it. Analyzing a function over a basis of encoded frequency information is very useful to us, as we will see.

You may think of the time domain representation of signals as having basis $$\{\delta(t)\}_{t=-\infty}^\infty$$. That is, every signal is simply a linear sum of deltas shifted across all continuous time, weighted to match the signal's amplitude at each time. However, if we instead change the basis to $\{e^{j \omega t}\}_{\omega=-\infty}^\infty$, then we can look at our signal from an entirely different lens. The function that represents the projections onto this basis is, in fact, the Fourier transform. 

## Why do we use $e^{j\omega t}$ as the basis?
By itself, changing the basis of a signal is, at best, a fun mathematical exercise and, at worst, pretty meaningless and needlessly complex. However, it turns out that defining a signal in terms of complex exponentials has a remarkable property that allows an alternative, and often times much simpler, way of analyzing LTI systems.

Consider an LTI system characterized by an impulse response $h(t)$. A curious property of LTI systems is that complex exponential inputs are eigenfunctions of the system. That is, if the system is defined as $\mathcal{S}\{x(t)\}$, then for input $x(t) = e^{j\omega t}$, 

$$
\mathcal{S}\left\{e^{j\omega t}\right\} = \int_{-\infty}^\infty e^{j\omega (t-\tau)} h(\tau) d\tau = \underbrace{e^{j\omega t}}_{x(t)}\underbrace{\int_{-\infty}^\infty e^{j\omega \tau} h(-\tau) d\tau}_{H(\omega)} = H(\omega) x(t).
$$

What this shows is that the output of an LTI system with a complex exponential input is, in fact, the same input multiplied by a scaling factor $H(\omega)$, which incidentally is the Fourier transform of the impulse response $h(t)$.

Let's take this even further. Since all LTI systems are linear by definition, it follows that an input into a system which is a linear combination of scaled and shifted complex exponentials will have each term multiplied by the $H(\omega)$ from before. In particular,

$$
\mathcal{S}\left\{\frac{1}{\sqrt{2\pi}} \int_{-\infty}^\infty X(\omega) e^{j\omega t} d\omega \right\} = \frac{1}{\sqrt{2\pi}} \int_{-\infty}^\infty H(\omega)X(\omega) e^{j\omega t} d\omega
$$

The bottom line is this: If we can express our signal as a linear combination of scaled and shifted complex exponentials, then the output of the system will have a Fourier representation given by $H(\omega)X(\omega)$. It just so happens that that's what our Fourier transform is!

So, because we represented our signal in this new basis, we can now simply analyze outputs of systems using multiplication instead of convolution. This property of LTI systems is really the sole reason why Fourier transforms are so fundamentally useful.

## Putting it all together
Here's the main point:
> The Fourier transform $X(\omega)$ is the scalar projection onto the subspace generated by a complex exponential of frequency $\omega$, for all $\omega \in \mathbb{R}$.

In other words, instead of looking at a signal as some quantity (e.g. voltage, current, etc.) that is changing over time, we will look at it as some quantity that is changing over frequency. For each particular frequency $\omega$, we will project our signal onto the subspace generated by that frequency, and the function that represents all these projections over any frequency $\omega$ is the Fourier transform, $F(\omega)$!

By projecting our original signal $x(t)$ onto this a "vector" $e^{j\omega t}$, we are intuitively getting all the "information" that is in the "direction" of this complex exponential.

More formally, the scalar projection of $x(t)$ in the direction of a complex exponential $e^{j\omega t}$ using our previous notation is

$$
proj_{e^{j\omega t}}(x(t)) = \frac{1}{||e^{j\omega t}||}\langle x(t), e^{j\omega t}\rangle = \frac{1}{\sqrt{2\pi}}\int_{-\infty}^\infty x(t) e^{-j\omega t} dt.
$$

If this final expression looks familiar, that's because this is precisely the equation for the Fourier transform, $X(\omega)$! If we replace $proj_y(x(t))$ with $X(\omega)$, we arrive at the familiar expression

$$
X(\omega) = \frac{1}{\sqrt{2\pi}}\int_{-\infty}^\infty x(t) e^{-j\omega t} dt.
$$
