# Visual Search at eBay

[//]: # (Image References)

[image1]: ./img/ebay_vis_srch_arch.png
[image2]: ./img/ebay_vis_srch_fc.png

Source: [arxiv](https://arxiv.org/abs/1706.03154)

In this paper, we propose a novel end-to-end approach for scalable visual search infrastructure.

Challenges:
 * Volatile Inventory
 * Scale
 * Data Quality
 * Quality of Query Image
 
## Approach

![alt text][image1]

### Category Recognition

We use state-of-the-art ResNet-50 network for a good trade-off between accuracy and complexity. The network was trained from scratch based on a large set of images from diverse eBay product categories.

Each training image is resized to 256 x 256 pixels and the 227 x 227 center crop and its mirrored version are fed into the network as input. We used the standard multinomial logistic loss for classification tasks to train the network. We change learning parameters after training for several epochs, and repeat this process several times until validation accuracy saturates. We also include on-the-fly data augmentation4 during training to enrich training data, which includes random geometric transformations, image variations and lighting adjustments.

### Deep Semantic Binary Hash

Although we can directly extract and index features from some of the convolutional layers or fully-connected layer, it is far from optimal and does not scale well. To address such challenges, we represent images as binary signatures instead of real values in order to greatly reduce storage requirement and computation overhead.

We append an additional fully-connected layer to the last shared layer, and use sigmoid function as the activation function. The sigmoid activation function limits the activations to bounded values between 0 and 1. Therefore, it is a natural choice to binarize these activations by a simple threshold of 0.5 during inference.

This bottom stream of split topology in network is trained independently from the top stream, but by fixing the shared layers and learning weights for the later layers with the same classification loss that we use for category recognition.

We use 4096 bits in our binary semantic hash.

![alt text][image2]

<p align="center">
We use a single DNN to predict category as well as to extract binary semantic hash.
</p>

