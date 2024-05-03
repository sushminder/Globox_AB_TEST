# A/B Test Report: Food and Drink Banner (Banner Impact Analysis)

## Purpose
GloBox, an e-commerce company specializing in unique global products, aims to increase awareness and revenue for its growing food and drink category. To achieve this, the Growth team initiates an A/B test focusing on the mobile website. Users are randomly assigned to either the control group, without a banner, or the test group, where a banner highlighting key food and drink products is displayed. The goal is to measure whether the banner significantly increases conversions and raises awareness for this product category. This data-driven approach helps GloBox optimize its platform to boost revenue and highlight diverse offerings to its customer

## Hypotheses for Conversion rate between control and treatment group
Null Hypothesis (H0): There is no significant difference in the conversion rates between the treatment and control groups.

Alternative Hypothesis (H1): There is a significant difference in the conversion rates between thetreatment and control groups.

## Hypotheses for difference in the average amount spent per user between the two groups
Null Hypothesis (H0): There is no significant difference in the average amount spent per user between the treatment and control groups.

Alternative Hypothesis (H1): There is a significant difference in the average amount spent per user between the treatment and control groups.

## Methodology

### Test Design

- **Population:**: 
The A/B test was conducted with a total of 48,943 users who visited the website during the test period.

- **Test and Control Groups:**
Test Group (Treatment): Consisted of 24,600 users exposed to the new Food and Drink banner.
Control Group: Included 24,343 users who saw the original banner (no change).

- **Duration:**
The test will run for two weeks to gather sufficient data and account for any variations in user behavior over different days of the week.

- **Metrics:**
The primary metrics for evaluation include:
Conversion Rate: A binary metric indicating whether a user converted (1) or did not convert (0).
Amount Spent: A continuous variable representing the amount spent by users

- **Statistical Analysis:**
Z-test for Proportion (Conversion Rate): To compare the conversion rates between the test and control groups. This test is appropriate for binary outcomes (converted 0s and 1s).
T-test for Means (Amount Spent): To compare the average amounts spent by users between the test and control groups. This test is suitable for continuous variables.

## Results 

## Data Analysis

--Write a SQL query that returns: the user ID, the user’s country, the user’s gender, the user’s device type, the user’s test 
--group, whether or not they converted (spent > $0), and how much they spent in total ($0+).

```sql
SELECT 
    u.id AS user_id,
    COALESCE(u.country, 'N/A') AS Country,
    COALESCE(u.gender, 'N/A') AS Gender,
    CASE
        WHEN g.device = 'I' THEN 'i_phone'
        WHEN g.device = 'A' THEN 'android'
        ELSE 'other'
    END AS Device_Type,
    CASE
        WHEN g.group = 'A' THEN 'Control'
        ELSE 'Treatment'
    END AS group_type,
    CASE WHEN SUM(a.spent) > 0 THEN 1 ELSE 0 END AS converted,
    COALESCE(SUM(a.spent), 0) AS total_spent
FROM 
    users u
JOIN 
    groups g ON u.id = g.uid
LEFT JOIN 
    activity a ON u.id = a.uid
GROUP BY 
    u.id, u.country, u.gender, g.device, g."group";
```
### Python code for further insights and statistical tests.
- **statistical tests Used:** Z-test for difference conversion rates and T-test for difference in means

Please follow my link below for my insights and codes 

python notebook link: https://github.com/sushminder/Globox_AB_TEST/blob/main/AB_TEST_GLOBOX.ipynb


### Findings

## Interpretation

- **Outcome of the Z tests:**
_Conduct a hypothesis test to see whether there is a difference in the conversion rate between the two groups. What are the resulting p-value and conclusion?_

z statistic: 3.86

p-value: 0.0001

The Z statistic of 3.86 suggests that the observed data point is 3.86 standard deviations away from the mean. The p-value of 0.0001 is very small less than 0.05, _we reject the Null Hypothesis_. This means we would conclude that there is a significant difference in the conversion rates between the treatment and control groups.

- **confidence Level for Z-test:**

estimate difference: 0.007069

confidence interval: [0.00348605 0.01065359]

Here estimate difference indicates that on average, the conversion rate in the treatment group is estimated to be 0.00707 units higher than the conversion rate in the control group. And confidence interval indicates that you can be 95% confident that the true difference in conversion rates between the treatment and control groups is somewhere between 0.00348605 and 0.01065359.

- **Outcome of the T tests:**
_Conduct a hypothesis test to see whether there is a difference in the average amount spent per user between the two groups. What are the resulting p-value and conclusion?_

T-statistic: 0.070432

P-value: 0.943849

A higher p-value, such as 0.9438, suggests weak evidence against the null hypothesis. _So here we are fail to reject Null hypothesis_. This means we would conclude that there is no significant difference in the average amount spent per user between the treatment and control groups.

- **95% confidence interval:** [-0.47, 0.44]

The confidence interval provides a range within which we are 95% confident that the true difference in means lies. In this case, the interval [-0.47, 0.44] includes zero, further supporting the idea that there may not be a significant difference. 

### Visualize the Confidence Intervals
For confidence interval plots illustrating the difference in conversion rates and the difference in average amount spent, please refer to the link below:

_confidence interval plot for difference in conversion rates_
https://public.tableau.com/views/confidence_interval_conversion_rate/CI_conversion_rate?:language=en-US&:display_count=n&:origin=viz_share_link

_confidence interval plot for difference in average amount spent_
https://public.tableau.com/views/confidence_interval_total_spent/CI_total_spent?:language=en-US&:display_count=n&:origin=viz_share_link


### Power Analysis

#### Sample Size for Comparing conversion rates
The Minimum Detectable Effect (MDE) is calculated as the sum of the baseline proportion and the desired difference:

Baseline proportion(control group conversion rate) = 0.0392(3.92%)

Desired Difference in Proportions is 0.007 (or 0.7%)

MDE (%) =Baseline Proportion (%)+ Desired Difference (%) MDE (%)=Baseline Proportion (%)+Desired Difference (%)

MDE (%)=3.92%+0.7%=4.62%

Baseline Conversion Rate (%): 3.92% Minimum Detectable Effect (%): 4.62% A/B Split Ratio: 0.5 (equal group sizes) Significance (α): 0.05

Statistical Power (1 - β): 0.8 (the probability of correctly rejecting a false null hypothesis)

Test Size: 180.1k Control Size: 180.1k Total Sample Size: 360.2k

https://www.statsig.com/calculator?mde=4.62&bcr=3.9230990428459926&twoSided=true&splitRatio=0.5&alpha=0.05&power=0.8

In evaluating the sample sizes for comparing two independent means and conversion rates, it was determined that the study lacked sufficient sensitivity. For the comparison of means, assuming a pooled standard deviation of 25.674946 units, a sample size of 38714563 for each group, totaling 77429126, is recommended to achieve 80% power at a 5% significance level, detecting a true difference of 0.016348999999999947 units. Similarly, for comparing conversion rates, considering a baseline rate of 3.92% and a desired difference of 0.7%, a total sample size of 360.2k, evenly split between test and control groups, is advised to attain a statistical power of 80% at a 5% significance level.

#### Sample Size for Comparing Two Independent Means_

calculate the pooled standard deviation 
```python 
pooled_sd  = np.sqrt(((25.936391)**2 + (25.414110)**2) / 2)
pooled_sd
```
pooled standard deviation = 25.676
difference in means = 0.01634

Results produced by Statulator beta: http://statulator.com/SampleSize/ss2M.html

Assuming a pooled standard deviation of 25.674946 units, the study would require a sample size of:

38714563 for each group (i.e. a total sample size of _77429126_, assuming equal group sizes), to achieve a power of 80% and a level of significance of 5% (two sided), for detecting a true difference in means between the test and the reference group of 0.016348999999999947 units.

In other words, if you select a random sample of 38714563 from each population, and determine that the difference in the two means is 0.016348999999999947 units, and the pooled standard deviation is 25.674946 units, you would have 80% power to declare that the two groups have significantly different means, i.e. a two sided p-value of less than 0.05.

## Conclusions
- **Key Takeaways:**
1. The A/B test indicates a significant increase in the conversion rate for the treatment group, where the Food and Drink banner was displayed.
2. Surprisingly, there was no significant difference in the average amount spent per user between the treatment and control groups. This implies that while the banner influenced users to convert, it did not significantly impact the amount they spent. 
3. The slight increase in spending in the treatment group could be attributed to a novelty effect, where users initially respond positively to a new feature but may not sustain this behavior over time.
4. The highest conversion rates were observed in countries like Canada (5.61%) and the USA (5.44%).



- **Limitations/Considrations:**
1. The observed increase in conversion rates may be influenced by short-term factors. Monitoring user behavior over an extended period can reveal whether the effects are sustainable.
2. The two-week test duration may not capture long-term user behavior changes. Short-term effects or novelty impacts could influence the results.
3. A significant number of users have unknown gender, impacting gender-specific analyses. Encouraging users to provide this information could enhance targeting strategies.


## Recommendations for Further Action
_I strongly recommend launching the Food and Drink banner to all users._

Business Factors Considered:

Costs: Launching the banner won't break the bank. Any operational costs are justified by the potential revenue increase and positive impact on user engagement.

User Experience: Even though the banner takes up prime space, the observed increase in conversion rates makes it worthwhile. The potential boost in revenue per user outweighs any impact on user experience.

In a nutshell, launching the Food and Drink banner is a smart move. The stats and business factors align, showing positive outcomes for GloBox. Additionally, continuous monitoring and iterative improvements should be integral parts of our strategy to ensure sustained success and user satisfaction.
