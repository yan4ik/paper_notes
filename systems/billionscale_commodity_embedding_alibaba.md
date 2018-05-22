# Billion-scale Commodity Embedding for E-commerce Recommendation in Alibaba

[//]: # (Image References)
[image1]: ../img/billionscale_alibaba_ege.png

Source: [arxiv](https://arxiv.org/abs/1803.02349)

## Framework

We propose to construct an item graph from users behaviour history and then apply graph embedding methods to learn the embedding of each item.

### Construction of Item Graph from Users Behaviours

Two items will be connected by a directed edge if they occur consecutively in one user session.

We assign a weight to each edge e<sub>ij</sub> based on the total number of occurrences of the two connected items in all users behaviours.

We filter invalid data and abnormal behaviour.

### Base Graph Embedding

We first generate node sequence based on `random walk`.

Then we run the `Skip-Gram` algorithm (with negative sampling) on the sequences.

### Graph Embedding with Side Information

**Problem:** it is challenging to learn accuarate embeddings for `cold-start` items.

**Solution:** enchance Base Graph Embedding using side information attached.

We concatenate n + 1 embedding vectors (one vector with item embedding and n vectors with side information embedding) and add a layer with average-pooling operation to aggregate all of the embeddings.

![alt text][image1]

In this way, we incorporate side information in such a way that items with similar side information will be closer in the mebedding space.
