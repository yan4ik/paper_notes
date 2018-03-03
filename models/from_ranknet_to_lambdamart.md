# From RankNet to LambdaRank to LambdaMART: An Overview

Source: [paper](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.180.634&rep=rep1&type=pdf)

LambdaMART is the boosted tree version of LambdaRank, which is based on RankNet.

## RankNet

RankNet maps an input feature vector x to a number f(x).

For a given query:
 * Each pair of urls U<sub>i</sub> and U<sub>j</sub> with differing labels is chosen. 
 * Each such pair (with feature vectors x<sub>i</sub> and x<sub>j</sub>) is presented to the model.
 * The model computes the scores s<sub>i</sub> = f(x<sub>i</sub>) and s<sub>j</sub> = f(x<sub>j</sub>).
