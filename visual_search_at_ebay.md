# Visual Search at eBay

[//]: # (Image References)

[image1]: ./img/ebay_vis_srch_arch.png
[image2]: ./img/ebay_vis_srch_fc.png
[image3]: ./img/ebay_vis_srch_sore.png

Source: [arxiv](https://arxiv.org/abs/1706.03154)

In this paper, we propose a novel end-to-end approach for scalable visual search infrastructure.

__Challenges:__
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

### Aspect Prediction

Aspects: color, brand, style, etc.

Our aspect classifiers are built on top of the shared category recognition network. Specifically, in order to save computation time and storage all aspect models share the parameters with the main DNN model, up to the final pool layer. Next, we create a separate branch for each aspect type.

Some aspects are specific to certain categories. Therefore we embed the image representation from pool5 layer with one-hot encoded vector representation of leaf category.

We use XGBoost to train the aspect models.

### Aspect-based Image Re-ranking

Suppose our model generates n aspects for a query image q. Each matched item in the inventory has a set of m aspects and values as poppulated by the seller during listing. We check whether the predicted aspects match such ground-truth aspects and assign a "reward point" w<sub>i</sub> to each predicted aspect that has an exact match. The final score S<sub>aspect</sub> is obtained by accumulating all scores of matched aspects.

For simplicity, we assigned hard-coded reward points for all aspects, although they can be learned from data. In our system, we assign larger points to the aspects size, brand and price while equal importance for all other aspects.

After calculating the aspect matching score, we blend it with the visual apperance score S<sub>appereance</sub> (normalized Hamming distance) from image ranking to obtain the final visual search score.

![alt text][image3]

Linear combination allows fast computation without performance degradation. The combination weight is fixed (0.75) in our current solution, but is also configurable dynamically to adapt to changes over time.
