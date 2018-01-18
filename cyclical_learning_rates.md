# Cycical Learning Rates for Training

Source: [arxiv](https://arxiv.org/abs/1506.01186)

[//]: # (Image References)

[image1]: ./img/cyclical_lr.png

This paper describes a new method for setting the learning rate, named `cyclical learning rates`.

Instead of monotonically decreasing the learning rate, this method lets the learning rate cyclically vary between reasonable boundary values.

Training with cyclical learning rates instead of fixed values achieves improved classification accuracy without a need to tune and often in fewer iterations.

## Optimal Learning Rates

### Cyclical Learning Rates

The essence of this learning rate policy comes from the observation that increasing the learning rate might have a short term negative effect and yet achieve a longer term beneficial effect.

One sets minimum and maximum boundaries and the learning rate cyclically varies between these bounds.

![alt text][image1]

### Good Value for the Cycle Length

Experiments show that it often is good to set stepsize equal to 2 − 10 times the number of iterations in an epoch.

Also, it is best to stop training at the end of a cycle, which is when the learning rate is at the minimum value and the accuracy peaks.

### Reasonable Minimum and Maximum Boundary Values

`LR range test` - run your model for several epochs while letting the learning rate increase linearly between low and high LR values.
