# A-B-Testing-for-e-commerce-webpage
In this Project we are helping e-commerce company in taking a decision in whether to go for the new webpage or keep the old webpage based on a test results of convert rate for people who used the old page and others who used the new page

## Introduction
A/B tests are very commonly performed by data analysts and data scientists. For this project, we will be working to understand the results of an A/B test run by an e-commerce website. Your goal is to work through this notebook to help the company understand if they should:

* Implement the new webpage,
* Keep the old webpage, or
* Perhaps run the experiment longer to make their decision.

The project is devided into 4 Steps:
* Part I - Probability
* Part II - A/B Test
* part III - Z-score test
* Part V - Regression
* Final Check

## Part I - Probability
in this section, we mainly try to understand our data:
* what columns we have
* what is the probability of people to convert
* what is the probability of people to convert while using either the new or old page
* and we do some cleaning of our data

## Part II - A/B Test
in this section we apply the A/B testing as per below steps:
* set the null and new hypothises
* Simulate (bootstrap) sample data set for both groups, and compute the "converted" probability  𝑝  for those samples.
* Use a sample size for each group equal to the ones in the df2 data.
* Compute the difference in the "converted" probability for the two samples above.
* Perform the sampling distribution for the "difference in the converted probability" between the two simulated-samples over 10,000 iterations; and calculate an estimate.
* calculate the p_value and compare it to type I error rate of 0.05

### Conclusion of A/B testing:
the calculated value in J is called in scientific studies the "p_value" and its value as 0.908 is large enough compared to the "Type I error rate (0.05) to say we failed to reject the null hypothises using the converted ratio metric

## part III - Z-score test
in this section, we will use a built-in to achieve similar results, specifically we will use sm.stats.proportions_ztest() to compute the test statistic and p-value
The syntax is: 
```bash
proportions_ztest(count_array, nobs_array, alternative='larger')
```
where, 
- `count_array` = represents the number of "converted" for each group
- `nobs_array` = represents the total number of observations (rows) in each group
- `alternative` = choose one of the values from `[‘two-sided’, ‘smaller’, ‘larger’]` depending upon two-tailed, left-tailed, or right-tailed respectively. 
>**Hint**: <br>
It's a two-tailed if you defined $H_1$ as $(p_{new} = p_{old})$. <br>
It's a left-tailed if you defined $H_1$ as $(p_{new} < p_{old})$. <br>
It's a right-tailed if you defined $H_1$ as $(p_{new} > p_{old})$. 

The built-in function above will return the z_score, p_value. 
perform the Z-test to calculate the Z_score, as shown in the equation below:

$$
Z_{score} = \frac{ (p{'}_{new}-p{'}_{old}) - (p_{new}  -  p_{old})}{ \sqrt{ \frac{\sigma^{2}_{new} }{n_{new}} + \frac{\sigma^{2}_{old} }{n_{old}}  } }
$$

where,
- $p{'}$ is the "converted" success rate in the sample
- $p_{new}$ and $p_{old}$ are the "converted" success rate for the two groups in the population. 
- $\sigma_{new}$ and $\sigma_{new}$ are the standard deviation for the two groups in the population. 
- $n_{new}$ and $n_{old}$ represent the size of the two groups or samples (it's same in our case)


>Z-test is performed when the sample size is large, and the population variance is known. The z-score represents the distance between the two "converted" success rates in terms of the standard error. 

Next step is to make a decision to reject or fail to reject the null hypothesis based on comparing these two values: 
- $Z_{score}$
- $Z_{\alpha}$ or $Z_{0.05}$, also known as critical value at 95% confidence interval.  $Z_{0.05}$ is 1.645 for one-tailed tests,  and 1.960 for two-tailed test. You can determine the $Z_{\alpha}$ from the z-table manually. 

Decide if your hypothesis is either a two-tailed, left-tailed, or right-tailed test. Accordingly, reject OR fail to reject the  null based on the comparison between $Z_{score}$ and $Z_{\alpha}$. 
>Hint:<br>
For a right-tailed test, reject null if $Z_{score}$ > $Z_{\alpha}$. <br>
For a left-tailed test, reject null if $Z_{score}$ < $Z_{\alpha}$. 


In other words, we determine whether or not the $Z_{score}$ lies in the "rejection region" in the distribution. A "rejection region" is an interval where the null hypothesis is rejected iff the $Z_{score}$ lies in that region.



Reference: 
- Example 9.1.2 on this [page](https://stats.libretexts.org/Bookshelves/Introductory_Statistics/Book%3A_Introductory_Statistics_(Shafer_and_Zhang)/09%3A_Two-Sample_Problems/9.01%3A_Comparison_of_Two_Population_Means-_Large_Independent_Samples), courtesy www.stats.libretexts.org

### Conclusion of Z-testing testing:
>Results are in alignment with the results in parts j & k where we can notice that p_value is almost equal to the one we get above in j, also as in k we failed to reject the null hypothises, we also failed to reject it gere since Z_score is not larger than the alpha (0.05) based on the statement that $Z_{score}$ > $Z_{\alpha}$ is the case where we belive we can reject the H_null in the right tailed test, but it didn't happen and what we can notice is that $Z_{score}$ < $Z_{\alpha}$ after the calculations 

## Part V - Regression
to have a better confidence on our results,we will apply a regression model by using statsmodels library to fit the regression model and get the P_value by different ways as follows:
* Since there are only two options, then Logistic regression is to be performed
* create the following two columns in the dataframe:
 1. `intercept` - It should be `1` in the entire column. 
 2. `ab_page` - It's a dummy variable column, having a value `1` when an individual receives the **treatment**, otherwise `0`. this is a work around to fit a model for categoral variable which is in our case the new page or old page
* Use statsmodels to instantiate your regression model on the two columns you created above, then fit the model to predict whether or not an individual converts
* Provide the summary of the model below, and use it as necessary to answer the following questions

### Observations: 
- p_value associated with ab_page is 0.1899
- p_value is different because the null and alternative hypothesis in the model is different from those in the AB testing we did
- null hypothesis: Beta_1 (slope) is equal to zero, that means in english there is no change in the response with any change in explanatory, this means specifically in our case that receiving new page dosent change the convert ratio
- alternative hypothesis: Beta_1 (slope) is NOT equal to zero, that means there is a change in the response associated with the change in explanatory, in our case this means receiving new page is having a change to the convert ratio
- hypothesis in part II is one-sided but in part III is two-sided
- comparing the p_value to alpha of 0.05 suggests we fail to reject null hypothesis mentioned above, that is we don't suggest to go for the new page which in fact aligned with AB testing done in part II

## Final Check
then, by adding countries, we are considering other things that might influence whether or not an individual converts, it is always a good idea to consider other factors to avoid falling in Simpson's Paradox
* Join the countries data frame to the main data frame
* Create the necessary dummy variables for the 3 countries (again to work araound the categoral variable)
* Fit the model, and summarize the results

### Observations:
based on the exponential of coeffiecent results, i believe the three countries are liekly to convert almost same regardless of receiving new page or not, as per below statical reasons:

- inverse of exp of CA coefficient is 1.04, that means CA is almost likely to convert same as our baseline (US), regardless of using new page or not
- exp of UK coefficient is 1.01, that means UK is almost likely to convert same as our baseline (US), regardless of using new page or not
- by looking at the confidence intervals, we can compare UK and CA users, as we can see an overlap, so this suggests that UK and CA will have the same convertion regardless of using new page or not

* Though we have now looked at the individual factors of country and page on conversion, we would now like to look at an interaction between page and country to see if are there significant effects on conversion. Create the necessary additional columns, and fit the new model


# Project Final Conclusion
as a final conclusion, i think we failed to prove that we can reject the null hyposthesis, that means based on the data we have and tests we have done, we are far from being confident enough to assume that population is likely to convert if they received the new page!
