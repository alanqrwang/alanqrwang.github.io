---
layout: post
title: What Does Probability "Measure" Mean?
description: In many probability courses, you may run into phrases like "probability measure" or "measurable function" or "measurable space". But what does this word "measure" really mean and why is it necessary to have this notion in the first place?
---
In many probability courses, you may run into phrases like "probability measure" or "measurable function" or "measurable space". But what does this word "measure" really mean and why is it necessary to have this notion in the first place?

## Let's start with rulers
Suppose we would like to measure the length of a wooden table. How would we do so? Let's imagine we are rather archaic and we choose to use a meter stick. In this case, the meter stick is our "length measure": in a sense, we are summarizing the length of a table into a quantitative value (e.g., 5 feet, 12 meters, etc.). You may choose to think of our length measure as a mapping from a space of objects to a space of concrete values.

Accordingly, our table is what we would call "measurable". That is, the table is an object that is capable of being summarized or mapped to a quantitative value. You may choose to think of the domain of our mapping function to be the space of all measurable objects. 

Let's step back and look at what our length measure has provided us. We now have a means to make judgments about our wooden table and make comparisons relative to other measurable objects. We can now answer questions like, "How much space will my table take up in my bedroom?" or "Will my lamp and book fit side-by-side on my table?" or "Can FedEx ship my table to my new apartment for under $50?" Without our measure, we would have been lost in the dark about how to say anything meaningful about our object. 

Although we take this idea for granted, the idea of measures is central to our thinking because it allows us to categorize, summarize, and quantify objects that would be difficult to describe in any other way. And in math, we frequently want to apply this kind of thinking to more abstract objects.

## Onto abstract objects...
Suppose we want to be able to quantify or summarize any discrete, finite set $S$. This way, we would be able to summarize the "size" of a set and distinguish which sets are larger than others. From our previous discussion, it seems we need to create a measure over the space of discrete, finite sets $\mathcal{S}$. More specifically, we want to define a measure that maps discrete, finite sets to natural numbers, where the corresponding number represents the cardinality of our input set. Clearly, such a mapping $f:\mathcal{S} \rightarrow \mathbb{N}$ would be 

$$
f(S) = |S|
$$

for an input $S \in \mathcal{S}$. 

And just like that, we have defined a measure over the set $\mathcal{S}$! We can make judgments about any set $S \in \mathcal{S}$ by looking at its measure, and we can also make comparisons between two or more different sets in $\mathcal{S}$ from their respective measures. Essentially, our measure $f$ is able to provide us a summary of any object over which the measure is defined, which is very useful to us (especially if those objects are abstract entities like sets). 

## Back to probability
Probability theory defines the notion of a measure space: 

$$
(\Omega, \mathcal{F}, P).
$$

Defined this way, $P$ is called our "probability measure", and it is analogous to our "length measure" or "cardinality measure" from before. That is, $P$ "quantifies" *the degree of uncertainty* of its input in a standardized manner, just as a ruler "quantifies" a notion of length to some standardized unit. This probability measure is crucial to the study of probability theory precisely because we wish to summarize or quantify the uncertainty that is inherent in our world. 

Let's think carefully about how we want our probability measure to behave. Given an event (like the toss of coin or the roll of a die), we want $P$ to spit out a standardized number that quantifies the certainty of that event occurring (and that can be compared relative to other events). Luckily for us, we already have a set $\mathcal{F}$ that contains all the events of a particular experiment, so our measure should be defined over this set. As for the number we spit out, an obvious choice is a number from the set $[0, 1]$, although this choice is relatively arbitrary.

Great! We have decided that our probability measure should be a mapping 
$$
P : \mathcal{F} \rightarrow [0,1].
$$ 
For the output of the measure to have any sort of meaning, events that are more likely to occur should have higher measures and events that are less likely to occur should have lower measures. In particular, events that are absolutely certain to occur should have measure $1$. Or, said more rigorously, the event that represents the realization of any element in the sample space (i.e. the event $\Omega \in \mathcal{F}$) should have measure $1$:
$$
P(\Omega) = 1.
$$
Correspondingly, events that are impossible or will never occur should have measure $0$, i.e. $P(\emptyset) = 0$. 

Of course, any event with degrees of uncertainty in between these two extremes should take a value between $0$ and $1$. [Kolmogorov's axioms of probability](https://en.wikipedia.org/wiki/Probability_axioms) gives specifics as to how the measure is defined for all other events in $\mathcal{F}$, which I skip to spare you of the gritty details. 

The main takeaway is that the measure $P$ is a magical function that encapsulates the degree of randomness and uncertainty inherent in an event into a single number between $0$ and $1$. So now given two events defined in the same probability space, we can now ask, say, "Which event is more probable to happen?" or "How likely is it that event A happens before event B" or "What is the average number of times this event will happen in this period of time?" Without $P$, we would have had a lot harder time to make concrete conclusions of uncertain events!

## The Bigger Picture
The motivation of a probability measure as an important concept in the setting of probability theory is fairly intuitive, and hopefully this post sheds some light on that intuition. Of course, the specifics are deeply rooted in the rigor of set theory, which those who are interested can explore more in depth.

While what is presented in this post is merely a motivation for the notion of measures as it applies to probability, the idea of [measure theory](https://en.wikipedia.org/wiki/Measure_(mathematics)) is an incredibly deep and fleshed-out branch of mathematics that reaches far beyond the limits of probability. Different types of measures (e.g. Gibbs, Hausdorff, Dirac) have uses in a wide scope of fields like physics, statistical mechanics, and topology.  
