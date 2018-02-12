# Related Pins at Pinterest: The Evolution of a Real-World Recommender System

[//]: # (Image References)

Source: [Pinterest Labs](https://labs.pinterest.com/user/themes/pinlabs/assets/paper/p2p-www17.pdf)

Related Pins leverages human-curated content to provide personalized recommendations of pins based on a given query pin.

**Key metric:** `Related Pins Save Propensity`, which is defined as the number of users who have saved a Related Pins recommended pin divided by the number of users who have seen a Related Pins recommended pin.

## Related Pins System Overview

The Related Pins system comprises three major components. The components were introduced to the system over time, and they have each evolved dramatically in their own right.

**Candidate Generation.** We first narrow the candidate set - the set of pins eligible for Related Pins recommendations - from billions to roughly 1,000 pins that are likely related to the query pin.

**Memboost.** A portion of our system memorizes past engagement on specific query and result pairs.

**Ranking.** A machine-learned ranking model is applied to the pins, ordering them to maximize our target engagement metric.
