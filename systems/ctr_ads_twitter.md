# Click-through Prediction for Advertising in Twitter Timeline

[//]: # (Image References)

[image1]: ../img/ctr_ads_twitter_pointwise.png

Source: [paper](www-personal.umich.edu/~qmei/pub/kdd2015-click.pdf)

In this paper, we present the problem of `click-through prediction for advertising in Twitter timeline`.  

**Given:**
 * A user’s timeline.
 * The session pushed to this user at a particular time.
 * A set of ads.
 
**Predict:** the probability that a particular ad will be clicked on if it is displayed at a particular position of this user’s timeline.

## Methods

### Pointwise Approach

We use `logistic regression with SGD`:

![alt text][image1]
