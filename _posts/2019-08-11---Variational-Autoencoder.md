---
title: Variational Autoencoders
date: "2019-08-11T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/variational-autoencoder/"
category: "Machine Learning"
tags:
  - "Machine Learning"
  - "Math"
description: "An in-depth look at how VAEs solve the questions inherent to latent variable models."
published: false
---

# Background: Latent Variable Models
VAEs are a type of latent variable model. Latent variable models assume the existence of an underlying, lower-dimensional set of data that "explains" or "produces" the data we may observe, but that is not observable itself.[^1]

<figure style="width: 500px">
	<img src="{{ site.baseurl }}/images/mnist_tsne.PNG" alt="Gutenberg">
	<figcaption>The MNIST Dataset in two-dimensional plane. Without latent knowledge, the dataset is messy and patterns are difficult to uncover.</figcaption>
</figure>

Let's model this underlying set of data by a random variable $z$, which is commonly called the latent variable. Latent variable models assume that underneath the veritable mess of high-dimensional data $x$ that we observe, there is a set of data $z$ of lower dimension that is "causing" or "producing" the set of data $x$. Without knowing $z$, $x$ is very messy and patterns are difficult to discern. However, if we know the $z$ that caused $x$ to occur, we can much more easily characterize that datum.

<figure style="width: 500px">
	<img src="{{ site.baseurl }}/images/mnist_tsne_clustered.PNG" alt="Gutenberg">
	<figcaption>The MNIST dataset in two-dimensional plane, color-coded by corresponding label. The clusters within the dataset become clearer when the existence of the latent space is known.</figcaption>
</figure>

Especially in the context of images, the latent variable model is very powerful. For example, $z$ could represent the integers $0,1,2,..., 9$, and $x$ could be images of handwritten digits. In this example, knowing the latent space $z$ helps us make sense of the data tremendously.

<figure style="width: 500px">
	<img src="{{ site.baseurl }}/images/mnist_tsne_clustered_labeled.PNG" alt="Gutenberg">
	<figcaption>The MNIST dataset in two-dimensional plane, color-coded and labeled with corresponding digit.</figcaption>
</figure>

As is par for the course in most generative models, we would like to learn the distribution $p(x)$ over our training data, such that we can simply sample from this learned distribution to "generate" new examples that are similar to the data on which we trained. Specific to latent variable models is the idea that we can leverage our knowledge of the existence of the latent variable $z$, such that we can learn more accurate and robust models. 

Formally, if we let $x$ and $z$ be defined over the same probability space, then we may further expand $p(x)$ as
$$
p(x) = \int p(x,z)dz = \int p(x|z)p(z)dz. \tag{1}
$$

Dissecting this expression:
+ Mathematically, this follows from the law of total probability, where $z$ forms a partition over the sample space $\Omega = \mathbb{R}$. [^2].
+ $p(z)$ is called the *prior* and is a distribution over the latent space. Usually this distribution is quite simple or is forced to be simple by design.
+ $p(x|z)$ is called the *likelihood* and represents a distribution over $x$ given a specific realization of $z$. We can see that $x$ is "dependent" on $z$. One way to think of this distribution is as a function $f(z)$, where if we sample $z \sim p(z)$, the output of $f(z)$ is very close to training data $x$. [^3]

By representing the model in this fashion, the generation of a sample can be thought of as a process, whereby we first sample $z\sim p(z)$ and then pass that $z$ into a "function" of the form $p(x|z)$. This process must be done and integrated over all possible $z$'s in the latent space. You might notice that integrating over this entire space is a very time-consuming ordeal.

There are several questions that we might have at this point:
### 1. What is $p(z)$ and $p(x|z)$?
In order to do any type of generation, we have to specify what the components in Equation $(1)$ are. As we will see, VAEs solve this with the help of neural networks.

### 2. How do we avoid having to integrate over the entire latent space?
As alluded to above, having to somehow integrate over the entire space of $z$'s is a very daunting task; indeed, there are practically an infinite number of configurations of $z$ that we have to go through. VAEs are smarter about which $z$'s we actually pick to integrate over, which mitigates the time necessary to perform inference. 

### 3. How can we leverage neural networks and training data to learn this model optimally?
Principally, VAEs leverage the power of neural networks and training data to learn optimal encodings and decodings of the observed data and latent space. We will see the objective function that VAEs optimize and how we arrive at such a formulation.

In the rest of this post, I will discuss how VAEs address each of these questions.

# 1. What is $p(z)$ and $p(x|z)$?
VAEs force $p(z) = \mathcal{N}(0, I)$; i.e., the latent variables are distributed by a standard Gaussian. How could a space as complicated as the latent space be modeled by something as simple as a standard Gaussian? The reason is that the $z$ that is sampled from $\mathcal{N}(0, I)$ will be passed into $p(x|z)$, which is modeled as a neural network. The idea is that if a neural network learns a mapping from the latent space to the observed data space, then some layer in the network will learn a mapping from a standard normal to a sufficiently complicated function. That is the power of neural networks that VAEs leverage.

From here on out, I will use the notation $p_\theta(x|z)$ to enforce the idea that $p(x|z)$ is a neural network with learnable parameters $\theta$.

# 2. How do we avoid having to integrate over the entire latent space?
Now that we have defined how we are mapping the information, how do we go about solving Equation $(1)$? We could just sample a bunch of $z$'s from a standard normal and approximate $p(x)$ as:
$$
p(x) \approx \frac{1}{N}\sum_{i=1}^N p_\theta(x|z_i), \text{   where   } z_i \sim \mathcal{N}(0, I).\tag{2}
$$
The problem with this method is that it takes a really long time to sample enough $z_i$'s to get a good approximation of $p(x)$. In fact, most $z_i$'s will result in $p_\theta(x|z_i)$ having low value, and thus contribute little to our approximation. This makes sense intuitively; typically, only a very small subset of settings of $z_i$ will actually map to $x$. For example, given an image of a handwritten digit, the digit it represents can only be one number out of ten. With this method, we will be wasting time calculating conditional probabilities given all ten settings of $z_i$, when in reality only one setting of $z_i$ will actually have high probability!

<figure style="width: 700px">
	<img src="{{ site.baseurl }}/images/vae-decoder-half.PNG" alt="Gutenberg">
	<figcaption>An inefficient way of performing inference, where it is necessary to integrate over all configurations of the latent space.</figcaption>
</figure>

Here's an idea: just as we are using a "function" $p_\theta(x|z)$ to map observed data to a given latent representation, why don't we similarly use another "function" $p(z|x)$ to give us the latent representation that maps to the observed data of interest? The space of latent variables that are likely under this function $q$ would definitely be smaller than the entire latent space, so it would make the calculations a lot more tractable. In this way, we could just sample $z\sim p(z|x)$ to get $z$'s that are most likely to arise from our given $x$, and then use our known $p_\theta(x|z)$ to generate new samples just as before:
$$
p(x) \approx \frac{1}{N}\sum_{i=1}^N p_\theta(x|z_i), 
$$
$$
\text{   where   } z_i \sim p(z|x).
$$

This, however, brings up several different issues. Firstly, what is $p(z|x)$? Bayes' rule says
$$
p(z|x) = \frac{p(x|z) p(z)}{p(x)} = \frac{p(x|z) p(z)}{\int p(x|z)p(z) dz}.
$$ 
We already know or will be solving for $p(z)$ and $p(x|z)$, but $p(x)$ requires integrating over all configurations of $z$, which is the exact problem we are trying to avoid in the first place!

The solution here is to introduce another function, denoted $q_\phi(z|x)$, that we will learn and encourage to be close to the true $p(z|x)$. We will define a similarity metric between $q_\phi(z|x)$ and $p(z|x)$ that we will attempt to minimize, effectively reformulating inference as an optimization problem. Learning can be done with gradient descent, and is done simultaneously with the learning of $p_\theta(x|z)$ from before.

We can now talk about why variational autoencoders are named as they are: 
1. Notice that we have effectively replaced the posterior distribution over the latent space with a family of distributions $q_\phi$, the parameters $\phi$ of which we will optimize. This is traditionally done in a class of statistical methods called variational methods, and is the reason for the "variational" part of VAEs. 
2. Additionally, our plan of attack is now to first pass our observed data $x$ through a "function" $q_\phi(z|x)$, which will give us a latent representation $z$ of the data, and then immediately pass this $z$ through another "function" $p_\theta(x|z)$ which we hope will learn to map that $z$ back to the original data $x$. Since we are simultaneously learning both $q_\phi$ and $p_\theta$ end-to-end, this structure is very similar to a traditional autoencoder!

![VAE network]({{ site.baseurl }}/images/vae-whole.jpg)

There are still key differences between VAEs and traditional autoencoders. Autoencoders by themselves are not generative; they do not model probability distributions and are not driven to encode latent information from a Bayesian standpoint. While autoencoders do use neural networks to represent an encoder-decoder pair which is trained to encourage input-output reconstruction, the codes that an autoencoder learns are discrete in the latent space. That is, every code has a one-to-one mapping to its corresponding datum, and there is no guarantee that the autoencoder will produce something remotely plausible given a code that it has not been trained on.

On the other hand, we want to be able to generate new samples by sampling from a standard normal in the latent space and decoding into the observed space. As such, the objective function we will need is richer than just a reconstruction loss. In the final section, we will show how using Bayesian modeling can help us derive an objective function that augments the traditional loss of autoencoders, and that allows VAEs to be generative.

# 3. How can we learn the model?
## The Objective Function
As we said before, the key to the VAE is making the approximated posterior $q_\phi(z|x)$ to be similar to the true posterior $p(z|x)$. To do this, we will need a metric that characterizes the similarity between two probability distributions. We will see that minimizing this metric will not only allow for good posterior approximation, but also lead us to a tractable objective function of the entire autoencoder network with the help of Bayes' rule.

For starters, the similarity metric we will be using is the Kullback-Liebler Divergence, which measures similarity between two probability distributions. Let's start with the KL-divergence between $q_\phi(z|x)$ and $p(z|x)$:
$$
D_{KL}(q_\phi(z|x) || p(z|x)) = E_{z\sim q_\phi(z|x)}[\log q_\phi(z|x) - \log p(z|x)].
$$
If we use Bayes' rule on $p(z|x)$, we can effectively get $p(z)$, $p_\theta(x|z)$, and $p(x)$ into our objective function as well:
$$
D_{KL}(q_\phi(z|x) || p(z|x)) = E_{z\sim q_\phi(z|x)}[\log q_\phi(z|x) - \log p_\theta(x|z) - \log p(z)] + \log p(x).
$$
Now, let's rearrange and simplify slightly:
$$
\begin{aligned}
\log p(x) - D_{KL}(q_\phi(z|x) || p(z|x)) &= -E_{z\sim q_\phi(z|x)}[\log q_\phi(z|x) - \log p_\theta(x|z) - \log p(z)] \\
&= E_{q_\phi(z|x)}[\log p_\theta(x|z)] - E_{z\sim q_\phi(z|x)}[\log q_\phi(z|x)-\log p(z)] \\
&= E_{q_\phi(z|x)}[\log p_\theta(x|z)] - D_{KL}(q_\phi(z|x) || p(z)). \tag{3} \\
&:= \mathcal{L}_{\theta, \phi}(x)
\end{aligned}
$$
Equation $(3)$ is a very compelling equation; we have introduced a newly-defined $\mathcal{L}$ (sometimes referred to as the empirical lower bound, as we will see) function which is the sum of the value we originally wanted to *maximize*, $p(x)$, and an "error" term $-D_{KL}(q_\phi(z|x) || p(z|x))$ that we originally wanted to *minimize*. One important thing to note is that KL-divergences are always non-negative. Thus, there are two common ways to make sense of the equation, both of which lead to the same conclusion:

1. Minimizing the KL-divergence between $q_\phi(z|x)$ and $p(z|x)$  $\implies$ Maximizing $\mathcal{L}$. Because the KL-divergence is non-negative, we can force our approximated posterior to be similar to the true posterior by maximizing this function.
2. Maximizing the log-likelihood $p(x)$ $\implies$ Maximizing $\mathcal{L}$. The $\mathcal{L}$ is effectively a lower bound [^4] on $p(x)$:
$$
\log p(x) \geq \mathcal{L}_{\theta, \phi}(x).
$$
Why do we put so much emphasis on the $\mathcal{L}$ function? The reason is that unlike $\log p(x)$, $\mathcal{L}_{\theta, \phi}(x)$ is actually tractable to optimize since it does not contain a $p(x)$ term! Whichever way you choose to look at it, we now have a tractable objective function that gives us a means to optimize our VAE network:
$$
\argmax_{\theta, \phi}\mathcal{L}_{\theta, \phi}(x) = E_{z \sim q_\phi(z|x)}[\log p_\theta(x|z)] - D_{KL}(q_\phi(z|x) || p(z)). \tag{4}
$$

The two terms in this objective function merit closer examination.

1. Although it may not look like it, $E_{z \sim q_\phi(z|x)}[\log p_\theta(x|z)]$ is effectively a reconstruction loss. This is because in order to solve for this term, we must sample from $q$ to get a particular $z$ (equivalently, take an $x$ from our training set and encode it), and then run this $z$ through $\log p$ (equivalently, decode the $z$). This is equivalent to doing a forward pass through the entire autoencoder network! In addition, if $q$ does a good job of mapping $x$'s to $z$'s, and $p$ does a good job of transforming $z$'s back to $x$'s, then $p(x|z)$ will be close to $1$ and thus $\log p(x|z)$ will be close to $0$. Alternatively, if $p(x|z)$ is relatively low, then $\log p(x|z)$ will be a relatively large negative value. So, this expectation term will indeed be maximized if the encoder-decoder pair can reconstruct inputs effectively.

2. On the other hand, $D_{KL}(q_\phi(z|x) || p(z))$ can be thought of as a regularizer, which enforces a penalty if the $z$ that the encoder outputs is not "like" a standard normal. You can imagine that without this penalty, the encoder could just spread different versions of the same code in completely different regions of latent space. By forcing the encoder to generate codes that look like they are sampled from $p(z)$, we essentially enforce structure into the prior distribution that prevents codes from being too spread apart.

## Implementation Details
In practice, we make several further design choices to make gradient descent possible on the objective function shown in Equation $(4)$.

Let's define $q_\phi$ as being Gaussian distributed:
$$
q_\phi(z|x) = \mathcal{N}(z | \mu_\phi(x), \Sigma_\phi(x)).
$$
Note that $\mu_\phi(x)$ and $\Sigma_\phi(x)$ are deterministic functions of $x$. That is, we are assuming that the $z$ that maps to an observed $x$ is distributed normally with a mean and variance transformed through a function with input $x$. This function is the encoder neural network.

This form is convenient for us because there are only two parameters to learn that characterize the entire distribution, and these parameters can be learned through a neural network. In addition, the KL-divergence of two normal random variables has a closed form:
$$
D(\mathcal{N}(z | \mu_\phi(x), \Sigma_\phi(x)) || \mathcal{N}(0, I)) = \frac{1}{2}\left(tr(\Sigma(x)) + (\mu(x))^T (\mu(x)) - k - \log \det (\Sigma(x))\right),
$$
where $k$ is the dimensionality of distribution.

The other summation term $E_{q(z|x)}[\log p(x|z)]$ is found through sampling from $q$, i.e.
$$
E_{z \sim q(z|x)}[\log p(x|z)] \approx \frac{1}{N}\sum_{i=1}^N \log p(x|z_i), 
$$
$$
\text{   where   } z_i \sim q_\phi(z|x) = \mathcal{N}(z | \mu_\phi(x), \Sigma_\phi(x)).
$$

Additionally, note that this is the same strategy we were going to take originally in Equation $(2)$, but now we are sampling from $z$'s defined over $q(z|x)$; i.e., we are being much smarter and more tractable about which $z$'s we are using to approximate $p(x|z)$.

To find an estimate over many different $x$'s, we additionally add an expectation term over $p(x)$, so that the final objective function is
$$
\argmax E_{x\sim p(x)}\big[E_{z \sim q_\phi(z|x)}[\log p_\theta(x|z)] - D_{KL}(q_\phi(z|x) || p(z))\big]
$$

## Gradient and Reparameterization Trick
In this form, taking the gradient is simple because we can push it into the expectations and simply take the gradient of the inside expression:
$$
\nabla_{\theta, \phi}\big(\log p_\theta(x|z) - D_{KL}(q_\phi(z|x) || p(z)\big)
$$
You may have noticed that this expression no longer has a dependence on $\phi$ in the reconstruction term, which we need in order to properly learn. In practice, in order to learn the parameters $\mu$ and $\Sigma$ of the encoder $q_\phi$, we apply a reparameterization trick that injects these learnable terms into the objective function: 
$$
\argmax E_{x\sim p(x)}\big[E_{z \sim \epsilon \sim \mathcal{N}(0, I)}[\log p_\theta(x|z = \mu(x) + \epsilon\sqrt{\sigma(x)})] - D_{KL}(q_\phi(z|x) || p(z))\big].
$$

# Conclusion
Let's take a step back and examine what we have done. We want to be able to approximate $p(x)$ as a sampling of many $p(x|z_i)$. To be smart about which $z_i$'s to pick so as to ensure that the $z_i$ maps to the corresponding $x_i$ with high probability, we also simultaneously learn a function $q(z|x)$ that maps $x$ to its corresponding latent variable $z$. If we sample from this $q(z|x)$ instead of picking any $z$ from the latent space, we can get better $z$'s that lead to good $p(x|z)$, and which in turn leads to estimating $p(x)$ more efficiently.



[^1]: This is referred to as the "manifold hypothesis".

[^2]: Something that I was always confused about was what defines the sample space in cases like this. Clearly, $z$ is a vector whose elements can take any real value. So $z_i$ is a random variable with some distribution $p(z_i)$. In this setting, there is no underlying experiment with abstract events that must be explicitly mapped to real numbers. Because the random variable taking some value is the experiment in of itself, we can just think of all the outcomes in $\Omega$ as being the act of $z_i$ taking a value on the real line. So in essence, we may say $$\Omega = \{\omega : z_i = \omega, \omega \in \mathbb{R}\}.$$ Or even simpler, we can think of this setting as disposing of $\Omega$ entirely and placing the measure on $\mathbb{R}$, where the entire real line is of measure $1$.

[^3]: Actually, $p(x|z)$ will output high values for settings of $x$ which arise most probably from the given $z$, but as a way of thinking, viewing it as a function is sufficient.

[^4]: Alternatively, we can derive the lower bound from Jensen's inequality: $$\log p(x) = \log E_{z\sim q(z|x)} \left[\frac{p(x, z)}{q(z|x)}\right] \geq E_{z\sim q(z|x)} \left[\log \frac{p(x, z)}{q(z|x)}\right] = \mathcal{L}(x)$$
