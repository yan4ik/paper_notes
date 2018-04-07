# Deep Interest Network for Click-Through Rate Prediction

Source: [arxiv](https://arxiv.org/abs/1706.06978)

[//]: # (Image References)

[image1]: ../img/deep_interest_system.png
[image2]: ../img/deep_interest_gauc.png
[image3]: ../img/deep_interest_architecture.png
[image4]: ../img/deep_interest_attention.png
[image5]: ../img/deep_interest_dice.png

In this paper, we introduce a new proposed model for CTR prediction, named `Deep Interest Network`, which is developed and deployed in the display advertising system in Alibaba.

# System Overview

![alt text][image1]

When a user visits the e-commerce site, system
 * checks his historical behaviour data.
 * generates candidate ads by matching module.
 * predicts the click probability of each ad and selects appropriate ads.
 * logs the user reactions given the displayed ads.
 
This turns to be a closed-loop consumption and generation of user behaviour data.

# Metric

We design a new metric named `GAUC`, which is the generalization of AUC. 

GAUC is a weighted average of AUC calculated in the subset of samples group by each user. 

The weight can be impression or clicks. An impression based GAUC is calculated as follows:

![alt text][image2]

# Model Architecture

![alt text][image3]

## Base Model

Our base model is composed with two steps:
 * transfer each sparse id feature into a embedded vector space.
 * apply MLPs to fit the ouput.
 
Notice that the input contains user behaviour sequence ids, of which the length can be various. Thus we add a pooling layer (e.g. sum operation) to summarize the sequence and get a fixed size vector.

**Problem:** going deep into the pooling operation, we will find that much information is lost, that is, it destroys the inner structure of user behaviour data.

## Deep Interest Network Design

Mathematically, the embedding vector V<sub>u</sub> of user U turns to be a function of the embedding vector V<sub>a</sub> of ad A, that is:

![alt text][image4]

Where, V<sub>i</sub> is the embedding of behavior id i, such as good id, shop id, etc.

w<sub>i</sub> is the attention score that the behavior id i contributes to the overall user interest embedding vector V<sub>u</sub> with respect to the candidate ad A.

## Data Dependent Activation Function.

We consider and design a novel data dependent activation function, which we name `Dice`:

![alt text][image5]

Mean and Variance in training step are calculate directly from each mini batch data, meanwhile we adopt the momentum method to estimate the running mean and variance, which are used in the test step.

Dice can be viewed as a soft rectifier with two channel: a<sub>i</sub>y<sub>i</sub> and y<sub>i</sub> based on p<sub>i</sub>.

p<sub>i</sub> is close to 1 when y<sub>i</sub> is close to the mean, and close to 0 otherwise.
