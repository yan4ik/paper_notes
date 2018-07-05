# Bag of Tricks for Efficient Text Classification

Source: [arxiv](https://arxiv.org/abs/1607.01759)

This paper explores a simple and efficient baseline for text classification.

**Problem with linear models:** linear classifiers `do not share parameters` among features and classes. 

## Model

The first weight matrix A is a look-up table over the words.

The word representations are then averaged into a text representation. 

This representation is in turn fed to a linear classifier.

We use the softmax function to compute the probability distribution over the predefined classes. 

### N-gram features

We use a bag of n-grams as additional features to capture some partial information about the local word order.

We maintain a fast and memory efficient mapping of the n-grams by using the `hashing trick`.
