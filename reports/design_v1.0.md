# Design

## Product Design

### Background
*Set the scene for what we're trying to do through a **user-centric approach***

- **users**: profile/persona of our users
- **goals**: our users' main goals
- **pains**: obstacles preventing our users from achieving their goals

example:

- **users**: machine learning developers and researchers.
- **goals**: stay up-to-date on ML content for work, knowledge, etc.
- **pains**: too much unlabeled content scattered around the internet.

### Value proposition
*Propose the value we can create through a **product-centric approach***

- **product**: what needs to be built to help our users reach their goals?
- **alleviates**: how will the product reduce pains?
- **advantages**: how will the product create gains?

example:

We will build a platform that helps machine learning developers and researchers stay up-to-date on ML content. We'll do this by discovering and categorizing content from popular sources (Reddit, Twitter, etc.) and displaying it on our platform. For simplicity, assume that we already have a pipeline that delivers ML content from popular sources to our platform. We will just focus on developing the ML service that can correctly categorize the content.

- **product**: a service that discovers and categorizes ML content from popular sources.
- **alleviates**: display categorized content for users to discover.
- **advantages**: when users visit our platform to stay up-to-date on ML content, they don't waste time searching for that content themselves in the noisy internet.

### Objectives

*Breakdown the product into key objectives that we want to focus on.*

Example

- Discover ML content from trusted sources to bring into our platform.
- Classify incoming content for our users to easily discover. [OUR FOCUS]
- Display categorized content on our platform (recent, popular, recommended, etc.)

### Solution

*Describe the solution required to meet our objectives, including its:*

- **core features**: key features that will be developed.
- **integration**: how the product will integrate with other services.
- **alternatives**: alternative solutions that we should considered.
- **constraints**: limitations that we need to be aware of.
- **out-of-scope**: features that we will not be developing for now.

Example

Develop a model that can classify the content so that it can be organized by category (tag) on our platform.

**Core features:**

- predict the correct tag for a given content. [OUR FOCUS]
- user feedback process for incorrectly classified content.
- workflows to categorize ML content that our model is incorrect / unsure about.
  
**Integrations**:

- ML content from reliable sources will be sent to our service for classification.

**Alternatives**:

- allow users to add content manually and classify them (noisy, cold start, etc.)

**Constraints**:

- maintain low latency (>100ms) when classifying incoming content. [Latency]
- only recommend tags from our list of approved tags. [Security]
- avoid duplicate content from being added to the platform. [UI/UX]

**Out-of-scope**:

- identify relevant tags beyond our approved list of tags (natural-language-processing, computer-vision, mlops and other).
- using full-text HTML from content links to aid in classification.

### Feasibility

*How feasible is our solution and do we have the required resources to deliver it (data, $, team, etc.)?*

Example:

We have a dataset with ML content that has been labeled. We'll need to assess if it has the necessary signals to meet our objectives.

Sample data point:

```json
{
    "id": 443,
    "created_on": "2020-04-10 17:51:39",
    "title": "AllenNLP Interpret",
    "description": "A Framework for Explaining Predictions of NLP Models",
    "tag": "natural-language-processing"
}
```
## System design

### Data

*Describe the training and production (batches/streams) sources of data.*

Example:

- training:
    - access to training data and testing (holdout) data.
    - was there sampling of any kind applied to create this dataset?
    - are we introducing any data leaks?
- production:
    - access to batches or real-time streams of ML content from various sources
    - how can we trust that this stream only has data that is consistent with what we have historically seen?

#### Labeling

*Describe the labeling process (ingestions, QA, etc.) and how we decided on the features and labels.*

Example

**Labels**: categories of machine learning (for simplification, we've restricted the label space to the following tags: natural-language-processing, computer-vision, mlops and other).

**Features**: text features (title and description) that describe the content.

### Metrics

*One of the hardest challenges with ML systems is tying our core objectives, many of which may be qualitative, with quantitative metrics that our model can optimize towards.*

Example

For our task, we want to have both high precision and recall, so we'll optimize for f1 score (weighted combination of precision and recall). We'll determine these metrics for the overall dataset, as well as specific classes or slices of data.

- True positives (TP): we correctly predicted class X.
- False positives (FP): we incorrectly predicted class X but it was another class.
- True negatives (TN): we correctly predicted that it's wasn't the class X.
- False negatives (FN): we incorrectly predicted that it wasn't the class X but it was.

$$\text{precision} = \frac{TP}{TP + FP}$$

$$\text{recall} = \frac{TP}{TP + FN}$$

$$\text{f1} = \frac{2 * precision * recall}{precision + recall}$$

![](https://madewithml.com/static/images/mlops/evaluation/metrics.png)

### Evaluation

*Once we have our metrics defined, we need to think about when and how we'll evaluate our model.*

**Offline evaluation**

Offline evaluation requires a gold standard holdout dataset that we can use to benchmark all of our models.

Example:

We'll be using this holdout dataset for offline evaluation. We'll also be creating slices of data that we want to evaluate in isolation.

**Online evaluation**

Online evaluation ensures that our model continues to perform well in production and can be performed using labels or, in the event we don't readily have labels, proxy signals.

Example:

- manually label a subset of incoming data to evaluate periodically.
- asking the initial set of users viewing a newly categorized content if it's correctly classified.
- allow users to report misclassified content by our model.

### Modeling

*While the specific methodology we employ can differ based on the problem, there are core principles we always want to follow:*

- **End-to-end utility**: the end result from every iteration should deliver minimum end-to-end utility so that we can benchmark iterations against each other and plug-and-play with the system.
- **Manual before ML**: try to see how well a simple rule-based system performs before moving onto more complex ones.
-** Augment vs. automate**: allow the system to supplement the decision making process as opposed to making the actual decision.
- **Internal vs. external**: not all early releases have to be end-user facing. We can use early versions for internal validation, feedback, data collection, etc.
- **Thorough**: every approach needs to be well tested (code, data + models) and evaluated, so we can objectively benchmark different approaches.

### Inference

Once we have a model we're satisfied with, we need to think about whether we want to perform batch (offline) or real-time (online) inference.

### Feedback

How do we receive feedback on our system and incorporate it into the next iteration? This can involve both human-in-the-loop feedback as well as automatic feedback via monitoring, etc.