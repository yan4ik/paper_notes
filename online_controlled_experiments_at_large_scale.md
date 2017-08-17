# Online Controlled Experiments at Large Scale

Source: [exp-platform](http://exp-platform.com/large-scale/)

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
