# Machine Learning Engineer Nanodegree
## Capstone Proposal
Pedro Rosanes
2017

## Proposal
<!-- _(approx. 2-3 pages)_ -->

[Kaggle's](https://www.kaggle.com/c/walmart-recruiting-trip-type-classification) Walmart Recruiting: Trip Type Classification

### Domain Background
<!-- _(approx. 1-2 paragraphs)_

In this section, provide brief details on the background information of the domain from which the project is proposed. Historical information relevant to the project should be included. It should be clear how or why a problem in the domain can or should be solved. Related academic research should be appropriately cited in this section, including why that research is relevant. Additionally, a discussion of your personal motivation for investigating a particular problem in the domain is encouraged but not required. -->

Walmart uses both art and science to continually make progress on their core mission of better understanding and serving their customers. One way Walmart is able to improve customers' shopping experiences is by segmenting their store visits into different trip types. 

Whether they're on a last minute run for new puppy supplies or leisurely making their way through a weekly grocery list, classifying trip types enables Walmart to create the best shopping experience for every customer.

Currently, Walmart's trip types are created from a combination of existing customer insights ("art") and purchase history data ("science"). In this recruiting competition, Walmart is challenging Kagglers to focus on the (data) science and classify customer trips using only a transactional dataset of the items they've purchased. Improving the science behind trip type classification will help Walmart refine their segmentation process.

### Problem Statement
<!-- _(approx. 1 paragraph)_

In this section, clearly describe the problem that is to be solved. The problem described should be well defined and should have at least one relevant potential solution. Additionally, describe the problem thoroughly such that it is clear that the problem is quantifiable (the problem can be expressed in mathematical or logical terms) , measurable (the problem can be measured by some metric and clearly observed), and replicable (the problem can be reproduced and occurs more than once). -->

The problem is to classify a shopping trip types based on the items that customers purchased. To give a few hypothetical examples of trip types: a customer may make a small daily dinner trip, a weekly large grocery trip, a trip to buy gifts for an upcoming holiday, or a seasonal trip to buy clothes.

Walmart has categorized the trips contained in this data into 38 distinct types using a proprietary method applied to an extended set of data. The challange is to recreate this categorization/clustering with a more limited set of features. This could provide new and more robust ways to categorize trips.

### Datasets and Inputs
<!-- _(approx. 2-3 paragraphs)_

In this section, the dataset(s) and/or input(s) being considered for the project should be thoroughly described, such as how they relate to the problem and why they should be used. Information such as how the dataset or input is (was) obtained, and the characteristics of the dataset or input, should be included with relevant references and citations as necessary It should be clear how the dataset(s) or input(s) will be used in the project and whether their use is appropriate given the context of the problem. -->

The dataset is provided by Walmart in the [Kaggle competition](https://www.kaggle.com/c/walmart-recruiting-trip-type-classification/data). It contains a Trip information, including information about the day it happened and information about the bought products. Bellow you can find the detailed description of each information:
- *TripType* - a categorical id representing the type of shopping trip the customer made. This is the ground truth that you are predicting. TripType_999 is an "other" category.
- *VisitNumber* - an id corresponding to a single trip by a single customer
- *Weekday* - the weekday of the trip
- *Upc* - the UPC number of the product purchased
- *ScanCount* - the number of the given item that was purchased. A negative value indicates a product return.
- *DepartmentDescription* - a high-level description of the item's department
- *FinelineNumber* - a more refined category for each of the products, created by Walmart

### Solution Statement
<!-- _(approx. 1 paragraph)_

In this section, clearly describe a solution to the problem. The solution should be applicable to the project domain and appropriate for the dataset(s) or input(s) given. Additionally, describe the solution thoroughly such that it is clear that the solution is quantifiable (the solution can be expressed in mathematical or logical terms) , measurable (the solution can be measured by some metric and clearly observed), and replicable (the solution can be reproduced and occurs more than once). -->

The proposed solution to the problem is the highest probability output of a probabilistic Multi-class SVM Classifier.

### Evaluation Metrics
<!-- _(approx. 1-2 paragraphs)_

In this section, propose at least one evaluation metric that can be used to quantify the performance of both the benchmark model and the solution model. The evaluation metric(s) you propose should be appropriate given the context of the data, the problem statement, and the intended solution. Describe how the evaluation metric(s) are derived and provide an example of their mathematical representations (if applicable). Complex evaluation metrics should be clearly defined and quantifiable (can be expressed in mathematical or logical terms). -->

A multi-class logarithmic loss function will be used to evaluate the model. The formula is:

$$-\frac{1}{N}\sum_{i=1}^N\sum_{j=1}^My_{ij}\log(p_{ij}),$$

where N is the number of visits in the test set, M is the number of trip types, log is the natural logarithm, $$y_{ij}$$ is 1 if observation i is of class j and 0 otherwise, and $$p_{ij}$$ is the predicted probability that observation i belongs to class j.

In order to avoid the extremes of the log function, predicted probabilities are replaced with max(min(p,1-10^{-15}),10^{-15}).

### Benchmark Model
<!--
_(approximately 1-2 paragraphs)_

In this section, provide the details for a benchmark model or result that relates to the domain, problem statement, and intended solution. Ideally, the benchmark model or result contextualizes existing methods or known information in the domain and problem given, which could then be objectively compared to the solution. Describe how the benchmark model or result is measurable (can be measured by some metric and clearly observed) with thorough detail. -->

For the benchmark, I will use a random forest that has as feature only the amount of item of each Department Description. And compare the multi-class logarithmic loss value of this model with the proposed solution model.

### Project Design
<!-- _(approx. 1 page)_

In this final section, summarize a theoretical workflow for approaching a solution given the problem. Provide thorough discussion for what strategies you may consider employing, what analysis of the data might be required before being used, or which algorithms will be considered for your implementation. The workflow and discussion that you provide should align with the qualities of the previous sections. Additionally, you are encouraged to include small visualizations, pseudocode, or diagrams to aid in describing the project design, but it is not required. The discussion should clearly outline your intended workflow of the capstone project. -->

The first step of the project is to understand the data quantitatively. For that I will count how many different classes, department descriptions and fineline numbers and products (UPCs) there are in the train set. Also I'll describe the distribution of scan count. 

The input files must also be parsed to better fit the problem description. That is, each line of the file should be merged into a single data structure that represent a trip. That way, I can easily query informations about a trip. During that step, lines with missing data can also be handled, and new features created. For example, suppose a line of the input file misses values for Department, Fineline Number and Upc, that line can still be used to count the total amount of products for the trip. 

After the data is structured by trip and not by product purchase, I will be able to describe the distribution of department description and fineline numbers by trip, and check the correlation between each department and each fineline number.

For the next step, I will execute some unsupervised learning algorithms such as Gaussian Mixture Model and KMeans. K can be varied from the number of trip types defined in the train data to higher number, since there is a trip type with name "Others". Suggesting that in reallity there are more trip types then the ones provided by Walmart. Dimensionality reduction might also be used to better visualize the results.

After all the analyzes have been done, I'll train a Support Vector Machine with Stochastic Gradient Descent given the size of the data: 647.054 examples.

<!--

**Before submitting your proposal, ask yourself. . .**

- Does the proposal you have written follow a well-organized structure similar to that of the project template?
- Is each section (particularly **Solution Statement** and **Project Design**) written in a clear, concise and specific fashion? Are there any ambiguous terms or phrases that need clarification?
- Would the intended audience of your project be able to understand your proposal?
- Have you properly proofread your proposal to assure there are minimal grammatical and spelling mistakes?
- Are all the resources used for this project correctly cited and referenced?
-->
