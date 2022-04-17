---
layout: post
title: HyperRecon - interactive and controllable image reconstruction using hypernetworks
description: "Deep learning-based method for interactive and controllable image reconstruction using hypernetworks."
---

Image reconstruction for deep learning is typically performed by training a neural network to map from noisy to clean images by minimizing a loss or a sum of losses.
For a sum of losses, a hyperparameter needs to be used to weight the contribution of the losses.
At deployment, these models will only produce a single clean image based on the loss it was trained on - this is suboptimal because different losses produce different visual reconstructions. 
How can we eliminate this dependence on the loss we select at training time (i.e. be *agnostic* to the loss function) and also get *multiple reconstructions* for a single noisy input at test-time?

HyperRecon addresses this by using a *hypernetwork* to produce multiple reconstructions corresponding to different loss functions.
This means that at test-time, a user can sweep over a continuous range of hyperparameter values and get a dense set of recontructions at test time!