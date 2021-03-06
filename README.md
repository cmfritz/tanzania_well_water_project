# Phase 3 Project - SyriaTel Customer Churn Dataset

## Final Project Submission

* Student name: Catherine Fritz
* Student pace: part time
* Scheduled project review date: February 19, 2021
* Instructor name: Amber Yandow

### Business Understanding
SyriaTel, a telecommunications company, has compiled data on their customers and whether or not they have stopped doing business, or churned, with SyriaTel. Since it is costly to lose customers, the goal is to use this data to identify customers that are likely to churn, and take measures to keep them from doing so.

### Data Understanding
The dataset provided information on the following features for each customer:
* US State
* Length of account
* Area code + phone numbers
* If the customer has an international plan
* If the customer has a voicemail plan
* No. of voicemail messages
* Breakdown of call minutes for day, evening, night, and international
* Breakdown of call charges for day, evening, night, and international
* Breakdown of no. of calls for day, evening, night, and international
* No. of calls to customer service
* If they have churned

#### EDA
I wanted to see if there might be a trend in the certain states churning more than others, so I plotted the no. of churns per state below.
![pic6a](./images/map.png)

#### Data Preparation
There appear some states with more churns than others, but it warrants further investigation. To be able to use the state data in the below models, a region was assigned to each state to reduce the no. of categories. The regions used are those used by the Census Bureau and are Northeast, Midwest, South, and West.

Consequently, I removed the state feature in favor of the region information. Features that were also removed were area code and phone number since area code was captured in the region, and phone number is simply a customer identifier.

### Modeling & Evaluation
Since the goal is to reduce the number of customer churns, I want to reduce the number of number of customers that leave since they were not identified as a churn risk, or simply, the number false negatives. This means that we want to prioritize recall, but not make the model so inaccurate that all customers are identified as potential churners.

#### Model 1 - Logistic Regression - SKlearn
I tried logistic regression for my first model.
##### Results:
| Metric      | LR     |
| ---------- |:--------:|
|Accuracy|86.3%|
|Recall|20.68%|
|F1|31.2%|
|AUC|45.0%|

Accuracy was not bad, but performed poorly in other metrics, especially recall. Considering logistic regression is better for data that are more continuous, I tried a different classifier.

#### Model 2 - Decision Tree Classifier
Next, I tried a Decision Tree Classifier, trying both Gini Impurity and Information Gain (entropy).
##### Results:
| Metric      | Gini     | Entropy  |
| ---------- |:--------:|:--------:|
|Accuracy|91.5%|93.1%|
|Recall|72.0%|71.3%|
|F1|70.8%|74.7%|
|AUC|72.8%|76.9%|

Using Gini provided slightly better recall than entropy, however with the sacrifice of some accuracy, F1, and AUC. Either way, this model outperforms logistic regression, but I felt this could still be improved.

#### Model 3 - XGBoost + Grid Search
For my last set of models, I used XGBoost to train gradient-boosted decision trees. I then used a grid search to optimize the parameters to produce an even better model.
##### Results:
| Metric      | XGB      | Grid Search|
| ---------- |:--------:|:--------:|
|Accuracy|95.3%|95.1%|
|Recall|70.6%|83.9%|
|F1|81.1%|83.0%|
|AUC|88.6%|87.3%|

The recall was boosted significantly by imposing greater penalties for errors on the minor class, which in this situation is identifying the churners.

The best performing model was the grid search with XGBoost with a recall of 83.9%. Below is the decision matrix used to calculate recall and also the Precision-Recall curve used to calculate AUC. PR curve used instead of ROC curve since the classes are imbalanced, as evidenced by the confusion matrix.

![pic2](./images/conf_matrix_gridsearch.png)
recall = True Positives / (True Positives + False Negatives)
= 120 / (120 + 23) = 96.9%

![pic3](./images/pr_curve_gridsearch.png)
AUC = 87.3%

### Evaluation
I stayed with the boosted model to determine feature importance, and used SHAP (SHapley Additive exPlanations) to determine them:
![pic4](./images/shap_importance.png)

### Recommendations

#### Recommendation 1:
Looking at the first feature from the importance graph, I used a boxplot to compare the total day minutes of customers who stayed and those who churned.
![pic5](./images/boxplot_day_minutes.png)
| Metric | No Churn | Churned |
| ---------- |:--------:|:--------:|
|Median|177.2 mins|217.6 mins|
|Mean|175.2 mins|206.9 mins|


Customers who spent more time on the phone during the day are more likely to churn. Customers who average over 200 minutes (outside the "box" for non-churners) should be considered higher risk for churning. Further investigation should be done if the needs of these heavy users of daytime minutes are being met.

#### Recommendation 2:
The next most important feature is the number of service calls. I used a box plot to compare frequency of calls to customer service for both churners and non-churners. I also used a bar graph to look at the number of times churners called customer service.
![pic6a](./images/boxplot_no_service_calls.png)
![pic6b](./images/bar_service_calls.png)
As can been seen from the bar graph, most churners called at least once. If a customer calls, it should be taken as an opportunity to make sure they are satisfied with their service. While plenty of non-churners call, all callers should be treated as a churn risk since there is considerable overlap, and the goal is to minimize churns.

#### Recommendation 3:
The third most important feature is if the customer has an international plan.
![pic7a](./images/bar_intl_plan.png)
![pic7b](./images/bar_intl_plan_churns.png)
| Int'l Plan? | No Churn | Churned |
| ---------- |:--------:|:--------:|
|Yes|186 customers|137 customers|
|No|2664 customers|297 customers|

About 32% of churners have an international plan, compared to about 7% of non-churners. Further investigation should be done to determine if international plans are meetings customers needs.

### Conclusion:
While region did not appear to have much influence on customer churn, several other factors did. The telecom company should conduct further research to ensure their heavy daytime minute user, international plan users, and customers who call customer service are satisfied with their service.

### Deployment
For More Information, please review my full analysis in Jupyter Notebook or my presentation.

For any additional questions, please contact Catherine Fritz: cmfritz0@gmail.com.

## Repository Structure
#### Main Page
    ├── README.md                              <- The top-level README for reviewers of this project
    ├── presentation.pdf                       <- PDF version of project presentation
    ├── analysis.ipynb                         <- master notebook
    ├── data                                   <- folder where data exists
    ├── images                                 <- folder where data visualizations and graphics are located

#### Data
    ├── churn_data.csv                         <- churn data .csv file
    ├── state-geocodes.csv                     <- Census regions .csv file

#### images
    ├── *.png                                  <- various .png files
