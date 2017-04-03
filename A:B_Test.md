# Design and Analyze A/B Testing - Udacity Free Trial Screener
by Li Cai

## Experiment Hypothesis

Udacity would like to test out [a screen](https://drive.google.com/file/d/0ByAfiG8HpNUMakVrS0s4cGN2TjQ/view) that is shown up by clicking the “Start free trial” button for a course.

The hypothesis was that this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time—without significantly reducing the number of students to continue past the free trial and eventually complete the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.

## Experiment Design

### Metric Choice

#### Invariant Metrics

1. __Number of cookies__ (_number of unique cookies to view the course overview page_): As the unit of diversion is cookies and they should be assigned to both control and experiment groups randomly, these two groups should have approximately equal number of cookies.

2. __Number of clicks__ (_number of unique cookies to click the "Start free trial" button_): As the click happen before the users see the experiment, number of clicks shouldn’t be affected by the screen changed, in other words, the course overview page remains the same for both control & experiment group.
3. __Click­-through­-probability__ (_number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page_): According to its definition, click­-through­-probability should be the invariant metric since both number of cookies and number of clicks are invariant metrics.

#### Evaluation Metrics

1. __Gross conversion__ (_number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button, dmin=0.01_): the number of user-ids to enroll in the free trial to decrease since the screener popup will make frustrated students don't enroll, but the number of unique cookies to click “Start free trial” remains constant. Thus, gross conversion rate should decrease.

2. __Net conversion__ (_number of user-ids to remain enrolled past the 14-day boundary divided by the number of unique cookies to click the "Start free trial" button, dmin= 0.0075_): Ideally the number of user-ids to make a payment should remain unchanged since the frustrated students were prevented by the reminder screen, and the number of cookies to click “Start free trial” remains constant. Thus, net conversion is supposed to remain constant. Make it as a evaluation metric, we could know whether the new feature has its effect.

The expectation for the experiment should be as follow:
* Gross conversion(dmin  = 0.01) will decrease practically significance.
* Net conversion(dmin  = 0.0075) will not decrease below the practical significance boundary.

#### Unused Metrics

__Number of user-ids__ (_number of users who enroll in the free trial_): it is not appropriate as an invariant metric because it will be affected by the new screener feature. Also, it is not a good evaluation metric since the numbers of user-ids of control and experiment groups are not normalized.

__Retention__ (_number of user-ids to remain enrolled past the 14-day boundary divided by number of user-ids to complete checkout_): it can reflect the potential preservation of paying students who complete the course, but gross conversion and net conversion both can capture this. In addition, its denominator is the number of user-ids, which is not a good invariant metric or evaluation metric, so retention is not considered a evaluation metric.


### Measuring Standard Deviation

Table of baseline values can be found [here](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0).

Given a sample size of 5000 cookies visiting the course overview page, unique cookies to click "Start free trial" per day will be 3200/(40000/5000) = 400, so the standard deviation of evaluation metrics can be calculated as following:

__Gross conversion:__ se = sqrt(0.20625*(1-0.20625)/400) = 0.0202

__Net conversion:__ se = sqrt(0.1093125*(1-0.1093125)/400) = 0.0156

For both evaluation metrics, the denominator is number of unique cookies to click the "Start free trial" button, so the unit of analysis is the same as the unit of diversion(a cookie), so the analytic estimates would be comparable to the empirical estimates.

### Sizing

#### Number of Samples vs. Power

I will not use the Bonferroni correction during my analysis phase because it is less likely that false positives be detected and more likely false negatives be detected by using the Bonferroni correction.

Given alpha of 0.05 and a beta of 0.2, the number of samples needed for each evaluation metric can be calculated using the [online calcultor](http://www.evanmiller.org/ab-testing/sample-size.html) with the parameters in the [table of baseline values](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0), the results are shown as below:

Evaluation Metrics | Number of sample
------------------ |----------------------------
Gross conversion   | 25835
Net conversion     | 27413(bigger one is chosen)

Since only 0.08 pageviews leads to click and double pageviews are needed for both experiment & control group, the number of pageviews need can be calculated:

* 27413/0.08*2 = 685325

#### Duration vs. Exposure
