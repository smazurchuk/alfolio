---
layout: distill
title: Babies and Moons
date: 2024-09-21
featured: false
giscus_comments: true
_styles: >
  .ovf {
    display: block;
    max-width: -moz-fit-content;
    max-width: fit-content;
    margin: 0 auto;
    overflow-x: auto;
    white-space: nowrap;
  }
bibliography: 2023-11-03-tics_reply.bib
authors:
  - name: Stephen Mazurchuk
    affiliations:
      name: MCW, Milwaukee
---

I have a short story I want to share. I was in the labor and delivery unit on an OB rotation and made a comment that, because that night happened to be a harvest and full moon, the night team would have their work cut out for them. A voice of someone who overheard the comment said “you know that’s been disproven, right?” While I imagined it probably is had been, the researcher in me also wanted to see the data for myself to see what the case was. 

I started where most people might start: reading the wikepedia page on the topic [here]. Of course, while many studies find no significant correlation, there are some that do find a correlation! I also looked on pubmed to see how strong the consensus was. To my surprise, most studies are either single center studies, and at most contain 10’s of thousands of births, or are only over a few years, limiting how many full moons are observed. Further, many of studies didn’t verify any statistical assumptions, which are very much violated if the right nuisance regressors aren’t included, like day of the week. I thought, surely birth data is compiled at a national level?

To that end, one of the studies stands out among the others as it looks at all the birth data across France over a 50-year time period (38.7 million births from 1968-2019). The data is freely and publically available from the French National Statistics and Economic Studies Institute (INSEE; data here). I **highly** recommend people read their short paper, and verify for themselves how good their methods are. In brief, it is probably the most rigorous paper on the topic. I quote from their abstract here:

> A likelihood ratio test is used to reject that the residual means for the 30 days of the lunar month all equal 0 (p-value = 5 × 10−5): the residuals show very small but highly significant variations in the lunar month due to an increase of births at full moon and the day after. The reason for the very small increase of birth at full moon is not investigated but can be suspected to result from a self-fulfilling prophecy.

Later in the paper, they state:

> In this paper, we provide the detailed analysis of a long series of birth data, and to the best of our knowledge, it is the first time that a surplus of births on full-moon days is observed (in mean), with a statistical significance high enough to leave no doubt.

The paper controlled for nuisance variables and tested for normality of residuals using multiple methods before applying a $$\chi^2$$ criterion.  

The relevant figure of their results is reproduced here:

<div class="l-page">
   {% include figure.html path="assets/img/moon/im1.png" caption="Figure 2 reproduced from paper"  
   class="img-fluid rounded z-depth-1 zoomable=true" %}
</div>


After reading this paper, I had to look to see if the CDC reported this data in the US!

## CDC Vital Data
Quickly looking for birth data in the US will take you to the CDC vital page. After downloading some data to see how much of an endeavor analyzing birth frequency, I discovered the us only releases birth data with dates of birth (DoB) before 1988. After 1988, the data is obscured to only show month and day of week. 

   {% include figure.html path="assets/img/moon/im2.png" class="img-fluid rounded z-depth-1 zoomable=true" %}

Too bad :frown:

At first I was deterred, but I kept scrolling around the different CDC pages, and then found a page with some PDFs. One of these PDFs tabulated births by date!

   {% include figure.html path="assets/img/moon/im4.png" class="img-fluid rounded z-depth-1 zoomable=true" %}

These PDFs existed for 10 years (1994-2003). I then thought it shouldn’t be too bad to download these PDFs, import the tables into python, and calculate lunar phases for each day! 

<div class="l-page">
   {% include figure.html path="assets/img/moon/im3.png" class="img-fluid rounded z-depth-1 zoomable=true" %}
</div>

## Analyzing the data
The code to process these PDFs will be posted on GitHub once I clean it up a bit. Some key parts of the dataset is that there are 39.7 million births and 123 full moons over 3,652 days. As seen in the French dataset, a quick glance of the data shows strong periodicity quick glance at the data shows strong periodicity. The most obvious trends are that weekends and holidays have fewer births than weekdays, and that the number of births increases during the summer months. To control for these effects, I created a dummy regressors for each day of the week, each month, the 12 federal holidays (and their observances), and a linear trend. The results of this regression are shown here:
Regression output

I then calculated the moon phase for each day of the dataset. I binned the phases into 90 bins and calculated the mean and 95% confidence intervals for each bin. The results are below:

Regression

<d-code class="l-page" block language="python">
print(model.summary())
                            OLS Regression Results                            
==============================================================================
Dep. Variable:                 babies   R-squared:                       0.930
Model:                            OLS   Adj. R-squared:                  0.929
Method:                 Least Squares   F-statistic:                     1548.
Date:                Sat, 21 Sep 2024   Prob (F-statistic):               0.00
Time:                        17:16:25   Log-Likelihood:                -27820.
No. Observations:                3652   AIC:                         5.570e+04
Df Residuals:                    3620   BIC:                         5.590e+04
Df Model:                          31                                         
Covariance Type:            nonrobust                                         
=======================================================================================================
                                          coef    std err          t      P>|t|      [0.025      0.975]
-------------------------------------------------------------------------------------------------------
DoW_Monday                           -708.9877     31.445    -22.547      0.000    -770.640    -647.335
DoW_Saturday                        -3242.0799     30.663   -105.732      0.000   -3302.199   -3181.961
DoW_Sunday                          -4170.3552     30.661   -136.016      0.000   -4230.469   -4110.241
DoW_Thursday                          180.7374     30.827      5.863      0.000     120.297     241.178
DoW_Tuesday                           367.7301     30.661     11.993      0.000     307.616     427.844
DoW_Wednesday                         140.8353     30.664      4.593      0.000      80.715     200.955
Holiday_Christmas Day               -4274.6548    158.972    -26.889      0.000   -4586.338   -3962.971
Holiday_Christmas Day (observed)    -3284.7145    351.008     -9.358      0.000   -3972.907   -2596.522
Holiday_Columbus Day                 -270.9524    160.396     -1.689      0.091    -585.427      43.523
Holiday_Independence Day            -2693.8766    158.967    -16.946      0.000   -3005.551   -2382.202
Holiday_Independence Day (observed) -2699.7480    351.006     -7.691      0.000   -3387.937   -2011.559
Holiday_Labor Day                   -3703.9428    160.470    -23.082      0.000   -4018.564   -3389.322
Holiday_Martin Luther King Jr. Day   -367.0267    160.491     -2.287      0.022    -681.688     -52.366
Holiday_Memorial Day                -3229.2506    160.396    -20.133      0.000   -3543.726   -2914.776
Holiday_New Year's Day              -2440.9792    159.049    -15.347      0.000   -2752.814   -2129.145
Holiday_New Year's Day (observed)   -2772.9675    350.439     -7.913      0.000   -3460.044   -2085.891
Holiday_Thanksgiving                -4350.4428    160.501    -27.105      0.000   -4665.124   -4035.762
Holiday_Veterans Day                   -3.2597    159.159     -0.020      0.984    -315.310     308.790
Holiday_Veterans Day (observed)       101.4571    287.316      0.353      0.724    -461.859     664.774
Holiday_Washington's Birthday        -685.2941    160.667     -4.265      0.000   -1000.301    -370.287
Month_Aug                             753.8447     40.040     18.827      0.000     675.342     832.348
Month_Dec                             206.8477     40.451      5.114      0.000     127.539     286.157
Month_Feb                              96.8213     41.400      2.339      0.019      15.652     177.991
Month_Jan                             -98.6462     40.738     -2.421      0.016    -178.517     -18.775
Month_Jul                             763.8813     40.434     18.892      0.000     684.605     843.157
Month_Jun                             386.9397     40.367      9.586      0.000     307.796     466.083
Month_March                           113.9699     40.040      2.846      0.004      35.467     192.473
Month_May                             271.5777     40.373      6.727      0.000     192.421     350.735
Month_Nov                             108.8449     41.200      2.642      0.008      28.067     189.623
Month_Oct                             267.9795     40.373      6.638      0.000     188.823     347.136
Month_Sept                            975.4699     40.718     23.957      0.000     895.638    1055.302
const                                1.168e+04     34.960    334.168      0.000    1.16e+04    1.18e+04
==============================================================================
Omnibus:                      730.774   Durbin-Watson:                   1.168
Prob(Omnibus):                  0.000   Jarque-Bera (JB):            10033.868
Skew:                          -0.551   Prob(JB):                         0.00
Kurtosis:                      11.045   Cond. No.                         47.1
==============================================================================

Notes:
[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
</d-code>

And finally, after binning and averaging ...

<div class="l-page">
   {% include figure.html path="assets/img/moon/US_CDC_Data.png" caption="US Births in Lunar Cycle. Error bars represent 95% confidence interval"  
   class="img-fluid rounded z-depth-1 zoomable=true" %}
</div>

Wild! A very *tantalizing* result 😊. While the single center data point is not quite significant, it is likely the linear regression approach leaves a lot of variance on the table. 

An alternative to specifying an explicit regression model is to simply band-pass the time series data. An FFT of the residuals of the linear regression show that there are still some pretty strong periodic signals in the data that we are not controlling for. While ideally we would determine what is causing the periodic signals, for the sake of time, I also calculated the results based on band-pass filtering with a window for frequencies that have periods between 10-50 days. These simple results, are even more tantalizing

<div class="l-page">
   {% include figure.html path="assets/img/moon/US_CDC_Data_filtered.png" caption="US Births in Lunar Cycle based on band-pass filtering raw birth data. Error bars represent 95% confidence intervals"  
   class="img-fluid rounded z-depth-1 zoomable=true" %}
</div>

Ideally, we would construct an autoregressive model that specifies exactly what variance we are controlling for. However, I didn’t want to let the perfect be the enemy of the good. It seems more babies might be born on Full moons. The data used to create these figures can be downloaded here. So, what do you think? Add your comments below!

Stephen
