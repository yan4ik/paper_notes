# SSD: Single Shot Multibox Detector

Source: [arxiv](http://arxiv.org/abs/1512.02325)

[//]: # (Image References)

[image1]: ../img/ssd_ssd.png
[image2]: ../img/ssd_prior.png
[image3]: ../img/ssd_grid.png
[image4]: ../img/ssd_loc_loss.png

We present a method for detecting objects in images using a single deep neural network.

Our approach discretizes the output space of bounding boxes into a set of default boxes over different aspect ratios and scales per feature map location.

At prediction time, the network generates scores for the presence of each object category in each default box and produces adjustments to the box to better match the object shape.

Additionally, the network combines predictions from multiple feature maps with different resolutions to naturally handle objects of various sizes.

## Model

![alt text][image1]

The SSD approach is based on a feed-forward convolutional network.

The early network layers are based on a standard architecture used for high quality image classification (truncated before any classification layers), which we will call the `base network`.

We then add auxiliary structure to the network to produce detections.

**Multi-scale feature maps for detection.**  We add convolutional feature layers to the end of the truncated base network. These layers decrease in size progressively and allow predictions of detections at multiple scales.

**Convolutional predictors for detection.** Each added feature layer (or optionally an existing feature layer from the base network) can produce a fixed set of detection predictions using a set of convolutional filters.

```python
nn.Conv2d(source_input_channels,
          bbox_count * 4, 
          kernel_size=3, 
          padding=1)]

nn.Conv2d(source_input_channels,
          bbox_count * num_classes, 
          kernel_size=3, 
          padding=1)]
```

**Default boxes** We associate a set of default bounding boxes with each feature map cell. At each feature map cell, we predict the offsets relative to the default box shapes in the cell, as well as the per-class scores that indicate the presence of a class instance in each of those boxes.

![alt text][image2]

Essentially the network produces predictions for each prior box.

Helpful picture to understand what's going on:

![alt text][image3]

## Training

During training we need to determine which default boxes correspond to a ground truth detection:
 * we match eaxg ground truth to the default box with the best jaccard overlap.
 * we then match default boxes to any ground truth with jaccard overlap higher that a threshold.
 
### Training objective

The SSD training objective is a weighted sum of the localization loss (loc) and the confidnce loss (conf).

The confidence loss is the standart softmax loss.

The localization loss is a Smooth L1 loss between the predicted box (l) and the ground truth box (g) parameters:

![alt text][image4]
