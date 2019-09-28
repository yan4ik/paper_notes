# Modeling Task Relationships in Multi-task Learning with Multi-gate Mixture-of-Experts

Source: [KDD 2018](https://www.kdd.org/kdd2018/accepted-papers/view/modeling-task-relationships-in-multi-task-learning-with-multi-gate-mixture-)

[//]: # (Image References)

[image1]: ../../img/mmoe.png

In this work, we propose a novel multi-task learning approach, `Multi-gate Mixture-of-Experts` (MMoE), which explicitly learns to model task relationships from data.

## Model Architecture

### The Setup

We have k tasks (e.g. predicting clicks, likes).

We have n "experts" - separate differentiable functions each outputing an embedding.

![alt text][image1]

### Pooling

**The Problem:** we want to combine embeddings from all experts.

**The Solution:** learn the weights for each expert (this is called `gating`).

This is depicted as (b) in the picture.

### Multi-task Pooling

**The Problem:** different tasks require different combine strategies.

**The Solution:** learn the weights for each expert separately for each task.

This is depicted as (c) in the picture.

There are k gating networks - one per task.

### Gating Details

The gating networks are simply linear transformations of the input with a softmax layer.
