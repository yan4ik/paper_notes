# Unsupervised Domain Adaptation by Backpropagation

Source: [arxiv](http://arxiv.org/abs/1409.7495)

[//]: # (Image References)

[image1]: ../img/da_backprop.png

The approach promotes the emergence of “deep” features that are:
 * discriminative for the main learning task on the source domain - `minimize the classification loss`.
 * invariant with respect to the shift between the domains - `maximize the domain classification loss`. 
 
![alt text][image1]
