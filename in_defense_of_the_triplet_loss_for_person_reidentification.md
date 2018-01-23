# In Defense of the Triplet Loss for Person Re-Identification

Source: [arxiv](https://arxiv.org/abs/1703.07737)

[//]: # (Image References)

[image1]: ./img/defence_triplet_hard_batch.png

We show that, for models trained from scratch as well as pretrained ones, using a variant of the triplet loss to perform end-to-end deep metric learning outperforms most other published methods by a large margin.

# Learning Metric Embeddings, the Triplet Loss, and the Importance of Mining

An essential part of learning using the triplet loss is the mining of hard triplets, as otherwise training will quickly stagnate.

We propose an organizational modification to the classic way of using the triplet loss: the core idea is to form batches by randomly sampling P classes (person identities), and then randomly sampling K images of each class (person), thus resulting in a batch of PK images. Now, for each sample a in the batch, we can select the hardest positive and the hardest negative samples within the batch when forming the triplets for computing the loss, which we call `Batch Hard`:

![alt text][image1]
