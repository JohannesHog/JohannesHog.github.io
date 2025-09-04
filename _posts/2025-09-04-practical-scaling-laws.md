---
title: "Practical Overview"
author_profile: false
categories:
  - training-dynamics
tags:
  - scaling-laws
---

- Scaling Laws are widely used. Guiding optimal way to scale up trainign of large models like LLMs and VLMs
- There are multiple design choices and pitfalls which are often not addresed even in major papers
- Give an overview, contrast, and compare the three major ways to fit scaling laws 
- Give practical recommendations to generate the data and create the fit.

# Introduction
- What are scaling laws?
	- How to increase Scaling Parameters in an optimal fashion
	- Power Law relationship
- What are scaling laws used for
  - Extrapolation
    - Figuring out what bigger model we want to train
    - Showing algorithm differences across scales
  - Determining scaling factors when increasing training dimension
  - Coming up with model families
- Problems with Scaling Laws
  - Starting with Hoffman scaling laws applied to LLMs
  - Chinchilla, a follow-up that came up with a different fit
  - Multiple works on how to explain the difference and reproduce results
  - Generally not well reported and different approaches (see the misfitting paper)
- This text aims to clear the confusion. 
  - Contrast the standard approaches and when to use them
  - Highlighting best practices


# Getting to the Scaling Laws
- There are mutliple choices to be made and a few pitfalls
- How to generate data, hyperparameters, functional form of scaling law, fitting algorithm and loss
- Introduce 3 methods to generate the data that needs to be fit on and compare them
  - How much training data needed
  - How error prone
- Additionally impact of hyperparameters and how to fit the laws.

## Method 1: Pareto front
- Train different sized models for different amounts of data
- Compare the smoothed training curves at logarithmically spaced FLOP values through interpolation (see left - grey points best)
- At each of these points pick the best model size (middle) and training data (right)
- Fit a power law on the data (details in different section?) -> red lines

![Figure 1.]({{ site.url }}{{ site.baseurl }}/assets/images/Hoffmann(2022)-Figure2.png)

**Pros**
- Not that strict in terms of what to train
- Can extend to more scaling parameters
- Good interpretation / visualization
- Generates a lot of points to fit on
  
**Cons**
- Easy to misfit by including points that should not be on the pareto front. 
	- Beginning for smallest model with smallest compute
  - Learning rate schedule dependence
    - Can't pick midway points because no meaning
    - Have to use checkpoints and partly constant lr schedules to save compute
  - Possible fix: Convex Hull

## Method 2: Isoflops
- For a set of flops train multiple different model sizes
- Smooth the loss and interpolate with parabola to pick the optimal parameters and training data
- Fit it following parameters fit thingy section


![Figure 1.]({{ site.url }}{{ site.baseurl }}/assets/images/Hoffmann(2022)-Figure3.png)

**Pros**
- Straight forward what to fit on -> Can't really screw this up
- Extendable to more scaling parameters (might be expensive and scale badly?)

**Cons**
- Pain in the ass to implement the flop total run thing
- Fewer points for the runs

## Method 3: Parametric Fit
- Generate a lot of datapoints in areas of interest.
- Pick and fit a parametric model

![Figure 1.]({{ site.url }}{{ site.baseurl }}/assets/images/Hoffmann(2022)-Figure4.png)

**Pros**
- Models interaction effects outside of the compute optimal -> In practice, we often overtrain
- Can't really screw the data generation

**Cons**
- Difficult to expand to other scaling parameters. There are lots of functional forms that can be fit.
- Needs compute approximation to transform into singular scaling relationships
  

## HPO
- for every datapoint
- learning curve - one of the reason for the difference between Hoffmann and Kaplan

## Fitting the points
- Huber Loss
- LBFGS/BFGS - randomly initialized

# Evaluation
- Extrapolation
- Uncertainty
- wellness of fit metrics

# Conclusion
- Some sort of flow chart?


# Open Questions for Me
- Early stopping?
- How to use checkpoint magic 