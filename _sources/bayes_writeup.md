---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Bayesian Methods to Assess Uncertainties related to the Classification of Tornado Magnitude

## Latifa Hasan, Kevin Hoffman, Prabhjot Singh, Jae Yoon Sung


Tornadoes are rotating columns of air that have the ability to condensate into a funnel-like structure that reaches towards the ground. Once at the ground level, tornadoes are capable of uprooting trees and houses, leaving cities and towns leveled, and causing mass casualty. The exact intensity and speed of a tornado is classified after assessing the damage profile of the storm and is rated using the Fujita (F) and Enhanced Fujita (EF) scale [1]. The EF scale is an updated version that takes into account engineered wind estimates and better damage descriptions, and went into effect in 2007. Since our primary focus will be an assessment of tornadoes from the years 2010-2018, we will not be worried about inconsistencies regarding the measurement of tornadoes. For comparison, an F0/EF0 tornado is typically regarded as weak in intensity, while F5/EF5 tornadoes are regarded as violent and are among the most damaging and deadliest of tornadoes [2]. More on the magnitudes and wind speeds of each tornado can be seen in the table below. Despite advancements in meteorological technologies over the years, tornadoes have remained extremely difficult to both predict and classify on the F/EF scale. Therefore in this project we hope to apply many of the concepts we have learned in Bayesian Machine Learning to implement probabilistic models to assess the uncertainties around the classification of tornadoes in the F/EF scale.



```{figure} /images/bayes1.png
---
name: barplot-fig
---
Table showing the various magnitudes of Tornoadoes and their associated wind speeds.
```

## Description of the Data
We obtained a collection of tornadoes (summer of 2010 to 2018) from the National Oceanic and Atmospheric Administration (NOAA)  database that openly allows users to obtain weather related datasets.  We also web-scraped to obtain weather conditions an hour and two hours prior to tornadogenesis or the formation of a tornado. The rationale being that these conditions could provide additional insight into the varying nature of tornado intensity and thus the classification of tornadoes. Any significant findings could also be pivotal in determining what weather conditions are ripe for dangerous tornadoes. In addition, all variables used were standardized and centered to have a mean of 0. 

Response Variable: Magnitude - (mag, from 0-5, categorical) - Classification of tornado ranging from EF0-EF5
Predictors: NOAA Database - Time (time_binned, binned numerical times into categorical), State (st, categorical), Injury(injuries, numerical) , Fatalities (fat, numerical),  estimated property loss in dollars (loss, numerical), amount of crop loss in million of dollars (closs, numerical), starting and ending location coordinates (slat, slon, elong, elat; numerical), length tornado traveled in miles (lenm, numerical), width tornado traveled in miles (wid, numerical) 
Web-scrapped - weather conditions (wind speed, visibility distance, temperature, temperature_dewpoint, air pressure 1 hr/2hr before tornado)

## Models Used and Mathematical Linkage Between Problem and Methods
### Mixed Naive Bayes  
In a Naive Bayesian model we assume features of a multidimensional likelihood are independent, even though they are not. In this case we know that many of our variables are dependent and related to one another. However this suboptimal approach still offers a method to obtain good parameter estimation, and thus we decided to build the Naive Bayes model. 

```{figure} /images/bayes2.png
---
name: barplot-fig
---
```

Where x is the data and giis the classification of the tornado. It is important to note that we have both categorical and numerical variables.  Therefore we require a mixed naive bayes approach: multinomial for categorical data and Gaussian for numerical. From here the probability of an observation belonging to a class is simply the product of both of the categorical and numerical naive bayes probabilities. The class with the greatest probability is the determined class, or in this case magnitude, of the tornado. This model yielded an accuracy of 57.1%. 

### Multinomial Logistic Regression  
Since our response variable is categorical with more than two classes, we modeled our data using a multinomial logistic regression model. Multinomial logistic regression uses a softmax function to compute the regression parameters, as opposed to the sigmoid function used in binary logistic regression. 

```{figure} /images/bayes3.png
---
name: barplot-fig
---
```

Here, P(yi = k|X) is the probability that yi, the ith observation is in class k, and K is the total number of classes. Here, we constructed two models to test whether a certain set of predictors can predict the magnitude of tornadoes better. The plot on the right shows the graphical representation of the models that were created. We constructed a multinomial logistic regression model  by incorporating all the predictor variables, and obtained an accuracy of 64.1%. The forest plots and probabilities generated from this model indicated that the variables ‘loss’, ‘length’, and ‘width’ were significant. We then constructed the second model  using the set of significant predictors and obtained an accuracy of 64.5%.The accuracy score from the model using the significant predictors is very similar to that of the model using all predictors, indicating similar predictive power. 

```{figure} /images/bayes4.png
---
name: barplot-fig
---
```

### Hierarchical Model Using Regions (binned states)   
We also modeled this data as a hierarchical multinomial logistic regression. Our assumption was that the weather and climates in different states vary and this could have an impact on the strength of tornadoes. A hierarchical model allows us to have a different slope and intercept for each state. The accuracy of the model when making hard classifications was about 48%. Most likely, there were too many groups and some states shared the same weather characteristics. NOAA group states into 9 weather regions based on climate and we used these as the basis of our groups for the hierarchical model [4]. Below is a graphical representation of our model using all predictors.


```{figure} /images/bayes5.png
---
name: barplot-fig
---
```

The inputs of our class probabilities p are the intercepts, features(data), betas, and the region associated with each tornado. We modeled the betas as a normal distribution. There is a different beta for each feature (23), each region (9), and for each of the classes except one (the 6th class probability can be inferred from the other 5). Similarly the intercept is modeled as a normal distribution and there is a different one for each region (9) and class (5). We put priors on the intercept and betas, using a normal distribution for the means and a half normal distribution on the sigmas (since sigma must not be negative). We get a probability for each class for each of the 4,459 tornados in the training data and that goes into the y, the true class label of the tornado. The features, region, and category are in data containers so after using variational approximation or sampling to approximate the posterior we can swap out the training data for the test data to make predictions and assess accuracy. Sampling generally produced better accuracy as it can get closer to the true distribution, but took longer to train. To produce predictions, we sampled from the posterior and made hard classifications by selecting the class with the highest probability. For the full model, the accuracy from sampling was 56.7%.

To improve this model, we looked at correlations between the features to see what features we could drop.

```{figure} /images/bayes6.png
---
name: barplot-fig
---
```

All of the weather features from 1 hour and 2 hours before the tornado touchdown were correlated so we transformed those features into a difference between each pair of observations. Inj, fat, loss, closs had some correlations together and very low variance (the majority of the observations were 0) so we removed those as well. This model with only 7 predictors of len, wid, delta_wind_speed, delta_visibility, delta_temperature, delta_dewpoint, and delta_air_pressure produced much better results. Below is the trace plot of the first three predictors for the first class and region from sampling. They show good convergence.

```{figure} /images/bayes7.png
---
name: barplot-fig
---
```

After approximating the posterior with the MCMC No-U-Turn sampling and sampling from the posterior to make predictions on our test set, the accuracy improved to 63.5%. We compared the WAIC of the two models and used Bayesian Model Averaging to make predictions from the combined models. 

```{figure} /images/bayes8.png
---
name: barplot-fig
---
```

The weight of the small model (one with only 7 predictors)  is 1 because the WAIC is much closer to 0 than the full model so this would give us the same result as just making predictions with the small model.

## Model Results

```{list-table} Results of Bayesian Approaches to Tornado Classification
:header-rows: 1
:name: results2

* - Model
  - Predictor
  - Accuracy
* - Naive Bayes
  - All
  - 0.571
* - Multinomial 
  - All 
  - 0.641
* - Multinomial
  -  ‘len’, ‘’wid’, and ‘loss’
  - 0.643
* - Hierarchical 
  - All
  - 0.567
* - Hierarchical
  - ‘len’,’wid’, change in weather predictors
  - 0.635
```


## Conclusions
Generally, we obtained similar predictions for all three of the models that were created. The Naive Bayes, surprisingly, performed up to par with the other models, indicating that a simple Bayesian model design can be successful is the classification of magnitudes of tornadoes with an accuracy of about 57%. In the Multinomial Logistic Regression model, we were able to better assess the uncertainties that lie within our parameter estimates. This was evident through our forest plots (may be seen in the Multinomial Logistic Regression Notebook) that indicated that only the length, width, and loss predictors were significant as they did not include 0 in their credible interval. Overall the accuracy of these two models was reported to be generally around 64%. Lastly, in our Hierarchical Multinomial Logistic Regression Model, we were able to create a precise model by first binning states into discrete regions, to furthermore be used as the categorical feature by which we created a hierarchical model. The parameter estimates from this model were very interesting, as we were able to clearly see the combinations of predictors that worked for each region and class and obtained an accuracy of about 57%, similar to our Naive Bayes model. After analyzing a correlations plot, we were able to improve these initial estimates by removing some features, increasing the accuracy up 64%.  Although this was slightly below the accuracy reported for the multinomial model, we believe that the insight into the uncertainty of parameters across regions provides a very novel look into how classification may differ based on where in the United States a tornado happened. 

One overall takeaway that can’t be ignored is the amount of influence that the predictors length and width that a tornado travels had in all our models. The credible interval on the coefficients were negative for weaker tornadoes and positive for stronger tornadoes. This makes sense as the length or width traveled by a tornado logically plays a role in the damage profile that ultimately happens after a tornado. A disappointing result that we did not expect was how poorly of an indicator weather (and change in weather) was to any of the models that were created. Although we included it in the Hierarchical model, it most certainly is not a predictor that we have much confidence in alone in the classification of tornadoes. 

For future research, since we are confident that the stronger tornadoes are more certain to predict the magnitude of tornadoes than the weaker tornadoes, we can try differentiating the tornadoes with EF greater than 2 or not.

One other interesting novel application worth exploring that is related to tornado classification, is taking advantage of doppler radar, and creating a Gaussian Mixture Model or K-means image classifier to locate pixels and signals indicative of a different magnitude of tornadoes. Radar images are particularly interesting because they provide a plethora of interesting weather patterns that could all be important indicators to a storm's development. In addition, a classification of this sort would be interesting as it could be used to classify tornadoes before or during its origination, rather than the current system where a tornado is classified with its respective damage profile. 

## References
[1] The Online Tornado FAQ (by Roger Edwards, SPC). Web.archive.org
[2] Fujita Tornado Damage Scale. Spc.noaa.gov.
[3] Chris Albon. 2017. Multinomial Logistic Regression. (December 2017). Retrieved December 8, 2020 from https://chrisalbon.com/machine_learning/naive_bayes/multinomial_logistic_regression/ 
[4] Thomas R. Karl and Walter James Koss, 1984: "Regional and National Monthly, Seasonal, and Annual Temperature Weighted by Area, 1895-1983." Historical Climatology Series 4-3, National Climatic Data Center, Asheville, NC, 38 pp.
*[5] Yves Jacquot (11 Oct. 2017.). Predicting tornado magnitude with Machine Learning.

