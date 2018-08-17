# Unsupervised Domain Adaptation by Backpropagation

Source: [arxiv](http://arxiv.org/abs/1409.7495)

[//]: # (Image References)

[image1]: ../img/da_backprop.png
[image2]: ../img/da_backprop_formula.png

The approach promotes the emergence of “deep” features that are:
 * discriminative for the main learning task on the source domain - `minimize the classification loss`.
 * invariant with respect to the shift between the domains - `maximize the domain classification loss`. 
 
![alt text][image1]

![alt text][image2]

**The idea:**: at training time, we seek the parameters of the feature mapping that maximize the loss of
the domain classifier, while simultaneously seeking the parameters of the domain classifier that minimize the loss of the domain classifier. In addition, we seek to minimize the loss of the label predictor.

As a result:
 * `feature extractor` should be **bad at predicting domain** and **good at predicting class label**.
 * `label predictor` should be **good at predicting class label**.
 * `domain predictor` sould be **good at predicting domain**.
