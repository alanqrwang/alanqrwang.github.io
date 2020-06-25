---
title: The Fourier Transform as a Limit of Fourier Coefficients
date: "2019-06-30T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/fourier-transform-part-1/"
category: "Signal Processing"
tags:
  - "Signal Processing"
  - "Math"
description: "Part 1 of a two-part series on Fourier transforms. In this post, as a \"limit\" of the Fourier series."
---

The importance and application of the Fourier Transform can hardly be understated. But, it has a complicated formula that tells very little of the intuition behind it:

$$
X(\omega) = \int_{-\infty}^\infty x(t) e^{-j\omega t} dt \longleftrightarrow x(t) = \frac{1}{2\pi}\int_{-\infty}^\infty X(\omega) e^{j \omega t} d\omega.
$$

This post is the first of a two-part series detailing two separate intuitions for the Fourier Transform. The first post approaches the Fourier transform as the aperiodic "limit" of the Fourier series, while the [second part](https://alanqrwang.github.io/posts-fourier-transform-part-1/) approaches the Fourier transform from a more abstract algebra perspective. In both parts, we will restrict our attention to continuous-time signals only, although most concepts have a discrete analogue. 

## The Quick and Dirty on the Fourier Series
Any periodic function can be expressed as a sum of harmonically-related complex exponentials. This is the central idea surrounding the Fourier Series. Formally, we can write (the proper word is *synthesize*) any periodic function $\tilde{x}(t)$ as a linear combination of the form

$$
\tilde{x}(t) = \sum_{n = -\infty}^\infty X_n e^{j n \omega_0 t} \tag{1}
$$

Some key observations from this expression:
+ $\tilde{x}$ denotes that our function is periodic. That is, $\tilde{x}(t) = \tilde{x}(t - nT)$, where $T$ is the period of the function and $n \in \mathbb{Z}$.
+ The formula presents a sum of an infinite number of complex exponentials $e^{j n \omega_0 t}$, which each have period $\frac{2\pi}{n \omega_0}$. [^1]
+ Each complex exponential has frequency $n\omega_0$. That is, every complex exponential that makes up $\tilde{x}(t)$ is an integer multiple of a *fundamental frequency* $\omega_0$. We say that $\tilde{x}(t)$ is composed of a set of *harmonically-related* complex exponentials.
+ We have a set of Fourier coefficients $\{X_n\}$, each of which represent the magnitude of its corresponding complex exponential.
+ We have an independent variable $t$ representing time that, when indexed, gives us a specific value of our signal at that time.

The key idea is if I gave you a set of Fourier coefficients $\{X_n\}$ and we agreed upon which complex exponential corresponds to which $X_n$, then having $X_n$ is, for all intents and purposes, equivalent to having $\tilde{x}(t)$ by itself! That is, (if you really wanted to), you could just solve for $\tilde{x}(t)$ using the formula, just from knowing $\{X_n\}$. The two representations are essentially different lenses to view the same signal.

That's all well and good, but how do we find these $X_n$'s ourselves, just from $\tilde{x}(t)$? Let's start with our expression from before, but multiply both sides by $e^{-jm\omega_0 t}$, integrate both sides over one period $T_0$, and switch the order of integration and summation:

$$
\begin{eqnarray}
\int_{T_0}\tilde{x}(t)e^{-jm\omega_0 t} dt &=& \int_{T_0}\sum_{n = -\infty}^\infty X_n e^{j n \omega_0 t}e^{-jm\omega_0 t} dt\\
 &=& \sum_{n = -\infty}^\infty X_n \int_{T_0} e^{j n \omega_0 t}e^{-jm\omega_0 t} dt
\end{eqnarray}
$$

Now, we will use something called the [orthogonality property](https://en.wikipedia.org/wiki/Orthogonal_functions) of harmonically-related complex exponentials, which states that for $n, m \in \mathbb{Z}$,

$$
\int_{T_0} e^{j n \omega_0 t}e^{-jm\omega_0 t} dt = \begin{cases} T_0, & n = m \\ 0, & n \neq m \end{cases}
$$

This implies that in our above sum, all the terms will drop out except for one which is equal to $T_0$. If we move that over to the left-hand side, we arrive at the final relation:

$$
X_n = \frac{1}{T_0} \int_{T_0}\tilde{x}(t)e^{-jn\omega_0 t} dt. \tag{2}
$$

The two equations (1) and (2) give us a means to examine a periodic function in two different lenses: one as a continuous-time signal $\tilde{x}(t)$, and another as a discrete set of finite coefficients $\{X_n\}$. Each representation contains equivalent information, as evidenced by the fact that we can easily transition from one representation to another. [^2]


## The Fourier Transform is the "Limit" of the Fourier Series
The Fourier Transform generalizes the Fourier Series to any signal, not just periodic signals. Since any aperiodic signal $\tilde{x}(t)$ is equivalent to a periodic signal $x(t)$ *with infinite period*, the key idea is:
> The Fourier transform of an aperiodic signal is the Fourier Series representation of a corresponding periodic signal over one period, where that period has been extended to be of infinite length.

If you believe me on the validity of the Fourier series for periodic signals, then let's start with those formulas and extend to the more general case of aperiodic signals.

We will be taking the limit as $T_0 \rightarrow \infty$, or equivalently as $\omega_0 \rightarrow 0$ (because $T_0 = \frac{2\pi}{\omega_0}$).

Take a look at $(1)$ and $(2)$ again. Remember, they are the Fourier series pair of equations that allow us to go between our original signal $\tilde{x}(t)$ and a discrete set of Fourier coefficients $\{X_n\}$.

$$
\tilde{x}(t) = \sum_{n = -\infty}^\infty X_n e^{j n \omega_0 t} \tag{1}
$$

$$
X_n = \frac{1}{T_0} \int_{-T_0/2}^{T_0/2}\tilde{x}(t)e^{-jn\omega_0 t} dt = \frac{\omega_0}{2\pi} \int_{-\pi/\omega_0}^{\pi/\omega_0}\tilde{x}(t)e^{-jn\omega_0 t} dt. \tag{2}
$$

Let's write this pair of equations in a slightly different way:

$$
\tilde{x}(t) = \frac{1}{2\pi}\sum_{n = -\infty}^\infty X(n \omega_0) e^{j n \omega_0 t} \omega_0 \tag{3}
$$

$$
X(\omega) = \int_{-\pi/\omega_0}^{\pi/\omega_0} \tilde{x}(t)e^{-j\omega t} dt \tag{4}
$$

I'll explicitly state the changes I made here:
+ I moved the constant factor $\frac{\omega_0}{2\pi}$ to the analysis equation. I'm allowed to move any constant constant factor to either equation as I like, because they are transform pairs.
+ I replaced $X_n$ with $X(n \omega_0)$, where $X(\omega)$ is a continuous function defined in $(4)$. The reason for doing this will become clear soon, but if you plug $n\omega_0$ into $X(\omega)$, you will see that we will arrive at precisely the same form as $(2)$.

Recall the definition of an integral. Specifically, for a continuous function $f(x)$, Riemann integration is the sum of an infinite number of rectangles with infinitesimally-small width:

$$
\int_{-\infty}^\infty f(x) dx = \lim_{\Delta x \rightarrow 0} \sum_{n = -\infty}^\infty f(n \Delta x) \Delta x.
$$

The right hand side is precisely what we have in $(3)$! By taking the limit as $\omega_0 \rightarrow 0$, we arrive at

$$
\begin{eqnarray}
x(t) &=& \lim_{\omega_0 \rightarrow 0} \frac{1}{2\pi} \sum_{k = -\infty}^\infty X(n \omega_0) e^{j n \omega_0 t} \omega_0 \\
 &=& \frac{1}{2\pi}\int_{-\infty}^\infty X(\omega) e^{j \omega t} d\omega. \tag{5}
\end{eqnarray}
$$

Similarly, $(4)$ becomes

$$
\begin{eqnarray}
X(\omega) &=& \lim_{\omega_0 \rightarrow 0} \int_{-\pi/\omega_0}^{\pi/\omega_0} x(t)e^{-j\omega t} dt \\
 &=& \int_{-\infty}^{\infty} x(t)e^{-j\omega t} dt. \tag{6}
\end{eqnarray}
$$

Just like that, we have derived the Fourier Transform and its inverse Fourier Transform, simply by extending the Fourier Series to infinite period!

What is the graphical intuition behind this? Consider a periodic rectangular pulse function $x(t)$ with period $T_0$ and its Fourier coefficients $X(nF_0)$, defined over $F$ [^3]:

![fourier1.png](/media/fourier1.png)

We can see that the space between two consecutive Fourier coefficients is proportional to the inverse period of the original signal $x(t)$. Why is that? Quite simply, we are indexing into our coefficient function at integer multiples of our fundamental frequency, i.e. $X(n F_0)$. Thus,

$$
X_{n+1} = X\left((n+1)F_0\right) = X(nF_0 + F_0) = X\left(nF_0 + \frac{1}{T_0} \right).
$$

This means that increasing $n$ by $1$ corresponds to a shift by $\frac{1}{T_0}$ in the graph of the $X(nF_0)$. Thus, increasing the period has the effect of decreasing the space in between consecutive Fourier coefficients.

Now, let's double the period and see what happens with the Fourier coefficients:

![fourier2.png](/media/fourier2.png)

As we suspect, the space between coefficients decreases by a factor of $2$. You may foresee that at the limit, this discrete function becomes continuous over all real values of $F$.

![fourier3.png](/media/fourier3.png)

This continuous function $X(F)$ is what we call the Fourier transform. Whereas with the Fourier series we viewed a periodic function as an infinite sum of complex exponentials consisting of harmonically-related frequencies, the Fourier transform describes an aperiodic function as an infinite integral of complex exponentials consisting *of all frequencies*.



[^1]: You can think of the word "period" as being from Euler's formula, which says that $e^{jx} = \cos x + j \sin x$.
[^2]: Technically, there are a bunch of conditions that need to be satisfied in order for the Fourier series representation to exist. I skip these technicalities here.
[^3]: Remember that $F \propto \omega$ by a scaled factor $\frac{1}{2\pi}$, so examining the graph this way is equivalent to examining it over $\omega$.
