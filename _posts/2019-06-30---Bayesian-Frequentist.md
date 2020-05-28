---
title: Bayesian or Frequentist?
date: "2019-06-30T22:40:32.169Z"
template: "post"
draft: true
slug: "/posts/bayesian-or-frequentist/"
category: "Probability"
tags:
  - "Probability"
  - "Math"
description: "My musings on the topic of the physical basis of probability that cuts at the heart of our physical world and philosophy itself."
---
Let's say I am flipping a fair coin. What is the probability that it will land heads? We all know the answer is $1/2$, but have you ever thought why that is? An argument might go like this: "Events that are absolutely certain have probability $1$. There are only two possible results, one of which is absolutely certain to occur. Thus, the probability of the two separate events must be equal, i.e. each must have probability $1/2$." 

To this argument I ask, why are the two probabilities necessarily equal? What evidence suggests that the universe would have any inclination to weight these two events equally, or otherwise enable either of these two events to have equal likelihood of occuring? 

As scientists, we like our statements to be rooted in some kind of physical observation. An apple falling on Newton's head and the laws of gravity, for example. Or, examining differing beaks of birds and the theory of evolution. Or, seeing fire burn and the laws of combustion and chemistry. But when it comes to probability, what physical basis do we have to draw our conclusions? Certainly, no magical genie pops out everytime we toss a coin and displays a number $1/2$.  

## Frequentists
One argument is that if I flip a coin a large number of times, the number of times I get heads divided by the total number of tosses approaches $1/2$. Or more generally, given an experiment and an event $E$ of that experiment,
$$
P(E) = \frac{\text{number of times } E \text{ is realized}}{\text{total number of times the experiment is performed}}.
$$

This is quite intuitive. If my experiment is the flip of a coin and the event $E$ is getting heads, then performing many coin flips and counting the number of times heads is realized tends to $1/2$. 

As another example, consider the following scenario: You are sitting in a coffee shop looking out the window at a busy intersection. This intersection has stoplights and walk signs for each direction that change in a specific order. However, this order is unknown to you (maybe the order of events at this intersection is quite convoluted and you are new to the city). What is the probability of seeing a walk sign?

![intersection.png](/media/intersection.png)

We can model this problem by defining $E$ as the event that the walk sign appears and $H$ as the history of all past events (this includes all stoplight changes and walk sign changes). We are searching for 
$$
P(E|H).
$$ 

A frequentist would sit in the coffee shop and stare at the intersection, carefully tracking every stoplight and walk sign change, and observing at what point the walk sign of interest appears. The frequentist will ideally do this for infinite time, but in practice will sit for a while (maybe 5 minutes or so) until he has observed many cycles. At the end, he tak
## Bayesian
