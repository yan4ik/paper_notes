# Related Pins at Pinterest: The Evolution of a Real-World Recommender System

[//]: # (Image References)

[image1]: ./img/related_pins_sys.png
[image2]: ./img/related_pins_pin2vec.png

Source: [Pinterest Labs](https://labs.pinterest.com/user/themes/pinlabs/assets/paper/p2p-www17.pdf)

Related Pins leverages human-curated content to provide personalized recommendations of pins based on a given query pin.

**Key metric:** `Related Pins Save Propensity`, which is defined as the number of users who have saved a Related Pins recommended pin divided by the number of users who have seen a Related Pins recommended pin.

## Related Pins System Overview

The Related Pins system comprises three major components. The components were introduced to the system over time, and they have each evolved dramatically in their own right.

![alt text][image1]

**Candidate Generation.** We first narrow the candidate set - the set of pins eligible for Related Pins recommendations - from billions to roughly 1,000 pins that are likely related to the query pin.

**Memboost.** A portion of our system memorizes past engagement on specific query and result pairs.

**Ranking.** A machine-learned ranking model is applied to the pins, ordering them to maximize our target engagement metric.

## Evolution of Candidates

### Board co-occurrence

#### Heuristic candidates.

The original Related Pins were computed in an offline Hadoop Map/Reduce job: we mapped over the set of boards and output pairs of pins that occured on the same board.

There are too many pairs of possible pins, so `pairs are randomly sampled` to produce approximately the same number of candidates per query pin.

We further added a `heuristic relevance score`, based on rough text and category matching. The score was hand-tuned by inspecting example results.

#### Online Random Walk

Shortcomings of previous approches:
 * heuristic score did not attempt to maximize board co-occurence.
 * rare pins did not have many candidates.
 
To address these limitations we moved to generating candidates at serving time through an online traversal of the board-to-pin graph.

### Session Co-occurence

Shortcomings of previous approches:
 * boards are often too broad, so any given pair of pins on a board may only be tangentially related.
 * boards may also be too narrow: for example, a whiskey and a cocktail made with that whiskey might be pinned in close succesion to different boards.
 
Both these shortcomings can be addressed by incorporating the temporal dimension of user behaviour: pins saved during the same session are typically related in some way.

We built Pin2Vec to harness these session co-occurence signals. Pin2Vec is a learned embedding of the N most popular in a d-dimensional space, with the goal of minimizing the distance between pins which are saved in the same session:
 * each training example is a pair of pins that are saved by the same user within a certain time window.
 * given one of the pins as input, an embedding matrix maps pin IDs to vectors, and a softmax layer is used to map the embeddings back into a predicted output pin ID.
 * the other pin in the pair is given as the expected output, and we train the embedding by minimizing the cross-entropy loss.
 * negative examples are sampled to make the optimization tractable.

At serving time, when the user queries one of the N pins, we generate candidate pins by looking up its nearest neighbours in the embedding space.
 
![alt text][image2]

### Supplemental Candidates

**Search-based candidates.** We generate candidates by leveraging Pinterest's text-based search, using the query pin's annotations (words from the web link or description) as query tokens. These search-based candidates tend to be less specifically relevant than those generated from board co-occurrence, but offer a nice trade-ff from an exploration perspective.

**Visually similar candidates.** 
 * If the query image is a near-duplicate, then we add the Related Pins recommendations for the duplicate image to the result.
 * If no near-duplicate is identified, then we use the Visual Search backend to return visually similar images, based on a nearest-neighbour lookup of the query's visual enbedding vector.

****

**Memboost.** A portion of our system memorizes past engagement on specific query and result pairs.
