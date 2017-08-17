# Deep Crossing: Web-Scale Modeling without Manually Crafted Combinatorial Features

Source: [paper](http://www.kdd.org/kdd2016/papers/files/adf0975-shanA.pdf)

This paper proposes the `Deep Crossing` model which is a deep neural network that automatically combines features to produce superior models.

## Model Architecture

TODO: image here

The model has four types of layers including
 * the Embedding Layer
 * the Stacking Layer
 * the Residual Unit
 * the Scoring Layer

### Embedding and Stacking Layers

Embedding is applied per individual feature to transform the input feautures. The embedding layer consists of a single layer of a neural network with ReLU activation.

The output features are stacked (concatenated) into one vector as the input to the next layer.

For the click prediction experiments embedding dimensionality is set to 256. Features with dimensionality lower than 256 are stacked without embedding.

### Residual Layers

The residual layers are constructed from the Residual Unit. The unique property of Residual Unit is to add back the original input feature ter passing it through two layers of ReLU transformations.

TODO: image here

### Early Crossing vs. Late Crossing

It is worthwhile to compare Deep Crossing with DSSM. DSSM has the distinct property of delaying the feature interaction (or crossing) to the late stage of the forward computation. Before reaching the Cosing Distance node, the input features are fully embedded through multiple layers of transformations on two separate routes. In contrast, Deep Crossing adopts at most one layer of single-feature embedding, and starts feature interaction at a much earlier stage of the forward computation.
