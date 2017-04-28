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

The expeirment is not risky, because there is no sensitive data being collected. What the experiment does is give the users a reminder to make them a further consideration that if they have enough time to complete the course, so the user experience or Udacity content will not be affected. Even later the experiment is ended, there will be no side-­effect for the experiment subjects. In general, there is no chance that anyone gets hurt because of the experiment.

In light of all these, I would like to divert the entire traffic to the exoeriment. For 100% exposure, it will take 18 days to complete the testing, which is a suitable duration for A/B testing.

## Experiment Analysis

### Sanity Checks

Given the confidence interval of 95% and the [data](https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0), the calculations are shown as below:

__Number of cookies__
* cookies (control) = 345543
* cookies (experiment) =  344660
* Standard deviation = sqrt(0.5*0.5 / (345543 + 344660)) = 0.0006018
* Margin of error = 1.96 * 0.0006018 = 0.00118
* Bounds = [0.5­ - 0.00118 , 0.5 + 0.00118] = [0.4988 , 0.5012]
* Observed value = 345543 / (345543 + 344660) = 0.5006

__Number of clicks__
* clicks (control) = 28378
* clicks (experiment) = 28325
* Standard deviation = sqrt(0.5*0.5 / (28378 + 28325)) = 0.0021
* Margin of error = 1.96 * 0.0021 = 0.0041
* Bounds = [0.5 ­- 0.0041 , 0.5 + 0.0041] = [0.4959, 0.5041]
* Observed value = 28378 / (28378 + 28325) = 0.5005

__Click­-through­-probability__
* Click­-through­-probability (control) = 28378 / 345543 = 0.0821
* Standard deviation = sqrt(0.0821 * (1 ­- 0.0821) / 344660) = 0.000468
* Margin of error = 1.96 * 0.000468 = 0.0009
* Bounds = [0.0821 ­- 0.0009 , 0.0821 + 0.0009 ] = [0.0812 , 0.0830]
* Observed value(experiment) = (28325 / 344660) = 0.0822

Results summary:

Evaluation Metrics        | Lower Bound | Upper Bound | Observed | Passes
------------------------- |------------ | ----------- | -------- | ------
Number of cookies         | 0.4988      | 0.5012      | 0.5006   | Yes   
Number of clicks          | 0.4959      | 0.5041      | 0.5005   | Yes   
Click­-through­-probability | 0.0812      | 0.0830      | 0.0822   | Yes   

### Result Analysis

#### Effect Size Tests

Given the confidence interval of 95% and the [data](https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0), the calculations are shown as below:

__Gross conversion__
* Gross conversion = 3785 / 17293 = 0.2189
* Standard deviation = sqrt(0.2189 * (1 ­- 0.2189) * (1/17293 + 1/17260)) = 0.0044
* Margin of error = 1.96 * 0.0044 = 0.0086
* d = 3423/ 17260 - 3785 / 17293 = -0.0206
* Bounds = [-0.0206 ­- 0.0086 , -0.0206 + 0.0086 ] = [-0.0291 , -0.0120]  
* not cover zero : statistically significant
* not cover dmin of -0.01 : practically significant

__Net conversion__
* Gross conversion = 2033 / 17293 = 0.1176
* Standard deviation = sqrt(0.1176 * (1 ­- 0.1176) * (1/17293 + 1/17260)) = 0.0034
* Margin of error = 1.96 * 0.0034 = 0.0067
* d = 1945/ 17260 - 2033 / 17293 = -0.0049
* Bounds = [-0.0049 ­- 0.0067 , -0.0049 + 0.0067 ] = [-0.0116 , 0.0019]
* cover zero : not statistically significant
* cover dmin of -0.0075 : not practically significant

Results summary:

Evaluation Metrics | Lower Bound | Upper Bound | Statistical significance | Practical significance
------------------ |------------ | ----------- | ------------------------ | -----------------------
Gross conversion   | -0.0291     | -0.0120     | Yes                      | Yes                     
Net conversion     | -0.0116     | 0.0019      | No                       | No                      

#### Sign Tests

Given the confidence interval of 95% and the [data](https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0), it can be found that gross conversion shows that 4 out of 23 days have positive increase and net conversion shows that 10 out of 23 days have positive increase, use the [online calculator](http://graphpad.com/quickcalcs/binomial1.cfm), the results are shown as below:

Evaluation Metrics | P-value | Statistical significance
------------------ |---------| ------------------------
Gross conversion   | 0.0026  | Yes (0.0026<0.05)       
Net conversion     | 0.6776  | No (0.6776>0.05)   

#### Summary

Bonferroni correction was not used during the analysis phase. In this case, a statistically and practically significant decrease fpor gross conversion and no significant change in net conversion are required. However, the Bonferroni correction will reduce the likelihood of false positives and increased false negatives, since a single false negatives would make the decision not lanch, it is not the expectation that both metrics are satisfied to trigger launch.

There are no discrepancies between the effect size hypothesis tests and the sign tests. Both tests declare the difference between the control group and the experiment group for gross conversion as statistically significant, but not for net conversion.

### Recommendation

I would recommend not to launch the feature.

The tests demonstrate that gross conversion is statistically practically significant, it matches the expectation because we hope the frustrated students would not continue after the time requirement reminder. It's a good news for Udacity, since it would reduce the cost of taking care of those frustrated students and Udacity coaches can focusing on students students who are likely to complete the course.

However, the lower bound of the confidence interval for net conversion(-0.0116) is much lower than -0.0075. This means that if this change were launched, there would be a chance to reduce the net conversion, which means the Udacity would lose paying customers. Apparently, it is not acceptale for the Udacity business because it can reduce the profit of Udacity.



## Follow-Up Experiment

From the reslut of above expeiment, it is likely that the net conversion will reduce after the time requirement screener launched. Why does students cancel the course if they have enough time to complete it? Other reason might be that these students does not meet the prerequisite of the course.

In the Follow-Up Experiment, Udacity should test the second change that ask students if they meet the prerequisite of the course after they are available to devote time to the course. For example, for the data analyst nanodegree, the prerequisites are knownledge of basic statistics and Python programming. The screener popup can be designed similar as the time requirement one, and add the details of prerequisites. If students answer "no", a message would appear indicating that the course requires some prerequisites to complete the course or have huge difficulty during the process of study.

The hypothesis was that this might set clearer expectations for students upfront, those student think they cannot overcome the dfficluties will enroll the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.

The unit of diversion is a cookie and the metrics I would want to measure are similar to those used in the A/B Test discussed in this report.

__Invariant Metrics__
* Number of cookies: The number of unique cookies to view the course overview page.
* Number of clicks: The number of unique cookies to click the "Start free trial" button (which happens before the free trial screener is trigger).
* Click­-through­-probability: The number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page.

__Invariant Metrics__
* Gross conversion: The number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button.
* Net conversion: The number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button.

## References:
- [A/B Test Final Project Introduction (Udacity)](https://docs.google.com/document/u/1/d/1aCquhIqsUApgsxQ8-SQBAigFDcfWVVohLEXcV6jWbdI/pub?embedded=True)
- [Evan Miller](http://www.evanmiller.org/ab-testing/sample-size.html)
- [GraphPad](http://graphpad.com/quickcalcs/binomial1.cfm)
