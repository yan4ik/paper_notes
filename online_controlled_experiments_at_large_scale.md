# Online Controlled Experiments at Large Scale

Source: [exp-platform](http://exp-platform.com/large-scale/)

[//]: # (Image References)

[image1]: ./img/exp_scale_arch.png

The problem that the Bing Experimentation System addresses is how to guide product development and allow the organization to assess the ROI of projects, leading to a healthy focus on key ideas that move metrics of interest.

## Tenets

__Tenet 1:__ The Organization wants to make data-driven decisions and has formalized the Overall Evaluation Criterion.

__Tenet 2:__ Controlled experiments can be run and their results are trustworthy.

__Tenet 3:__ We are poor at assessing the value of ideas.

## Cultural And Organizational Lessons

### Why Controlled Experiments?

Why not measure the metric of interest, ship the feature, and then llok at the delta? Our experience is that external variations overwhelm the effects we are trying to detect. 

<p align="center">
"Any claim coming from an observational study is most likely to be wrong."
</p>

### Cost vs. Benefit and the Ideas Funnel

Organizations should consider a large number of initial ideas and have an efficient and reliable mechanism to narrow them down to a much smaller number of ideas that are ultimately implemented and released to users in online controlled experiments. For this funnel of ideas to be efficient, low cost methods such as pitching ideas and reviewing mockups are needed to evaluate and narrow down the large number of ideas at the top of the funnel. Controlled experiments are typically not suited to evaluate ideas at the top of the funnel because they require each idea to be implemented sufficiently well to deploy and run on real users. Hence, at the top of the funnel more ideas are evaluated using low-cost techniques, but with lower fidelity. Conversely, at the bottom of the funnel there are fewer ideas to evaluate and the organization should use more reliable methods to evaluate them, with controlled experiments being the most reliable and preffered method.

One reason for using other methods in these cases is to gain qualitative feedback (e.g., through surveys and usability lab studies); however these other methods should be used to complement controlled experiments and not to replace them since the quantitative information they provide is inferior.

### Test Everything in Experiments

Platform changes, code refactoring and bug fixes should also be tested with controlled experiments: run the new component in an A/B test and see that you get no significant differences (or even better, some improvements).

For a large organization, there are many small fixes that go in every day, and it would be unreasonable to run contolled experiments for each one. We recommend that small fixes get bundled into packages so that if one is egregiously bad, the package will test negatively and it will be identified.

### Negative Experiments

Over time, we achieved agreement that knowingly hurting users in short-term (e.g. a 2-week experiment) can let us understand fundamental issues and thereby improve the experience long-term. Hippocrates' "Do no harm" should really be "Do no long-term harm."

### Beware of Twyman's Law and Stories in the Wild

Twyman wrote that "Any figure that looks interesting or different is usually wrong." Most amazing results turn out to be false when reviewed carefully, so they need to be replicated with high statisticak power and deeply analyzed before we believe them.

### Innovation vs. Incrementalism

As with any funnel of ideas, one must evaluate the total benefit of several small incremental bets vs. some big bold risky bets. 

The question of "fail fast" vs. "persevere" always comes up. There is no magic bullet here: it is about running some experiments to get a sense of the "terrain" and being open to both options.

### Multivariate Tests

In the online world, agility and continuous availability of users makes MVT's less appealing.

In our experience, interactions are relatively rare and more often represent bugs than true statistical interactions. Hen we do suspect interactions, or when they are detected, we run small MVT's, but these are relatively rare.

## Engineering Lessons

### Architecture

![alt text][image1]

* `Online Infrastructure` - As a request is recieved from a browser, Bing's frontend servers assign each request to multiple flights running on a set of number lines. To ensure the assignment is consistent, a pseudo random hash of an anonymous user id is used. All systems in Bing are driven from configuration and an experiment is implemented as a change to the default configuration for one or more components. Each layer in the system logs information, including the requests flight assignments, to system logs that are then processed and used for offline analysis.

* `Experiment Management` - Experimenters use a system, called Control Tower, to manage their experiments. To support greater automation and scale, all tools, including Control Tower, run on top of APIs for defining and executing experiments and experiment analysis.

* `Offline Analysis` - An experiment summary is called a scorecard, and is generated by an offline experiment analysis pipeline that must manage a large scale analysis workload - both in data and volume of experiments. Using the logs, the system manages and optimizes the execution of multiple analysis workflows used for experiment scorecards, monitoring and alerting, as well as deep dive analytics.

### Impact of the Experimentation System

Experimentation System performance impact%
 * experiment assignment adds a small delay (less than a millisecond).
 * increasing the number of experiments assigned to each request results in increasing cache fragmentation, lowering cache hit rates and increasing latency.
 
We holdout 10% of our total users from any experimentation. The problem of understanding the impact of Experimentation System itself becomes another A/B test.
