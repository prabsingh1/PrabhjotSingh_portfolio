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


# Application of Neural Networks to Enable Efficient Use of Wind Energy Final Writeup

##   Cory Clayton | School of Data Science | University of Virginia | Charlottesville, VA    
##   Emily Lien | School of Data Science |University of Virginia |Charlottesville, VA 
##   Prabhjot Singh |School of Data Science | University of Virginia | Charlottesville, VA | ps4fk@virginia.edu


## Abstract

Wind harnessing energy devices such as turbines provide attractive alternatives to fossil fuels. However, wind turbines don’t provide steady streams of energy, leading to periods of time where the energy grids must rely on conventional methods. Therefore, the wide-scale adoption of these technologies is hindered greatly by an inability to utilize wind energy at appropriate time intervals. Recognizing this, this project aims to leverage deep learning techniques to predict wind speed. Specifically, long short-term memory (LSTM), recurrent neural networks (RNN), and deeper LSTM and RNN have been used to obtain MSE values 0.49, 0.5797, 0.3129 and 0.5386 respectively. The initial results indicate that more data integration and model tuning is required to obtain a successful model. However, there is promise that this method could lead to predictive capabilities of wind speeds for the purposes of wind energy efficiency.


## Motivation

The efficient use of clean energy resources such as wind turbines relies directly on the ability to predict short-term wind speeds. Due to the unstable generation of power from wind turbines and a lack of large scale means to store power, it is necessary that they are part of a larger energy system with other sources of power, such as coal or natural gas, that can supplement the energy produced by wind power to provide it’s customer a reliable flow of electricity. Being able to forecast the speed and direction of the wind, and by extension the power generated, can help the system as a whole make decisions about how much supplemental power is needed to provide sufficient electricity to its customers without waste due to overproduction.


In addition, climate change has disrupted our previous understanding of the atmosphere and how it impacts weather. Thus, although clean energy may over time alleviate concerns related to global warming and climate change, the repercussions are already being felt, making it difficult to appropriately leverage clean energy resources that rely on adequate weather prediction. Beyond this, the wide-scale adoption of wind energy also relies on the direct financial gains that the system is able to produce. These challenges presented by the environmental and economic aspects surrounding weather forecasting are the main motivations to our project, which aims to investigate if neural networks can be a viable option for predicting wind speeds to enable efficient use of wind energy.

## Methods
### Baseline Model - LSTM
The initial baseline model that was implemented was a long short-term memory (LSTM) 16 layer model using an interval of the past 96 hours of data to predict wind speed 1 hour into the future. The main motivation behind the use of LSTM was because of the sequential nature of the data. An RNN structure, despite its ability to leverage sequential data, does not retain information from previous timestamps as well as theoretically claimed. This inability to learn long-term dependencies is the main rationale for using LSTM, which is a more complex recurrent unit  that is able to control the information that is passed through via gates. 

### Secondary Model - Deeper LSTM
We implemented a second LSTM model, this time with three LSTM layers of 75, 30, and 30 units in the hopes of improving performance. Early stopping was also incorporated into this deeper model as a preliminary regularization method.


### RNN models
As an experiment, we ran a simple RNN with no regularization, and worked through a few different versions with increasing depth. While the values of the validation loss were a step backwards from the initial LSTM models, starting from the most basic model and working our way towards higher levels of complexity was an opportunity to see how much of a difference extra layers makes, and will be helpful in informing our future model tuning.
            
### Final Hourly Model - LSTM
After experimenting with even deeper architecture, different regularization    strategies, and varying number of units without significant improvements, sometimes with worse performance than before, we settled on a version of the deeper LSTM model with 64, 32, and 16 units, predicting the wind speed one hour into the future with a similar MSE to the initial deeper LSTM
          
### Multi-Hour Model - LSTM
A two layer model with 32 and 16 units was used for predicting six hours into the future, with added depth not contributing significantly to model performance. 


## Experimentation
We are attempting to use weather data from Charlottesville to predict the future wind speed. After obtaining and cleaning weather data for Charlottesville from 2017 to 2020 from NOAA's database, the data was down-sampled to the hourly data. Using a deep learning model with 16 long short-term memory (LSTM) layers the model uses the past 96 hours of data to predict the wind speed 1 hour in the future. Methods of tuning and optimization were also integrated, but little to no improvement was found.  In addition a Persistent Model, in which wind speeds are inferred to be the same as the interval before, was used to compare against as well. 


## Results 
Our models tuned for predicting one hour in the future had mean squared errors of 0.49 for the LSTM model, 0.3129 for the deeper LSTM model, 0.5797 for the RNN and 0.5386 for the deeper RNN. Only the the deeper LSTM model was able to outperform the Persistent model that had a MSE of 0.322. The results are summarized in Table 1, and samples of model performance are shown in figures 1 and 2.

```{list-table} Results of initial model runs.
:header-rows: 1
:name: results1

* - Model
  - MSE
* - LSTM
  - 0.49
* - Deeper LSTM
  - 0.31
* - RNN
  - 0.58
* - Deeper RNN
  - 0.54
* - Persistent
  - 0.322
```

In addition, the deep LSTM model was changed to predict six hours into the future and compared to the Persistent model. In this version of the model the persistent model outperformed the deep LSTM model for the first hour, but quickly falls off the further into the future it is required to predict. In contrast the LSTM model's performance declines significantly slower as the prediction gets further out.


```{list-table} Results of Multi-Hour LSTM
:header-rows: 1
:name: results2

* - Hour
  - LSTM
  - Persistent
* - 1
  - 0.0410
  - 0.0245
* - 2
  - 0.0544
  - 0.0666
* - 3
  - 0.0772
  - 0.1230
* - 4
  - 0.1004
  - 0.1886
* - 5
  - 0.1221
  - 0.2580
```

```{figure} /images/NNonehour.png
---
name: barplot-fig
---
Plotting the predicted values vs. the actual values of the one-hour ahead model after training. The start-up speed indicates the speed at which the turbine begins moving, while the cut-in speed is the wind speed necessary for power production. Furling speed is where the turbine cuts off to prevent damage to the system. These values may differ depending on the turbine.
```

```{figure} /images/NN6hour.png
---
name: barplot-fig
---
Making predictions with the six hours ahead model.
```


## Conclusion
Predicting weather in a time series can be tricky, but in our experiment the recurrent neural network using long short-term memory units was able to make accurate predictions about future wind speeds. The model tuned for one hour ahead only slightly outperformed the persistent model making it's applications not very useful. However the model predicting six hour in the future significantly outperformed the persistent model baseline. This model would be beneficial to a wind energy system allowing the other energy producers to plan and adapt for their energy production needs. 

Future work should consist of either building on the initial LSTM model or attempting other deep learning methods that utilize more complex models. Although the LSTM model performed fairly well, there were weaknesses indicated by the fact that the Persistent model outperformed it on predictions for wind speed an hour away. Future work should examine the reasoning behind why the LSTM falls short on on this interval, primarily by incorporating in more or less layers. In addition, other deep learning methods such as the use of transformers have shown promise in the domain of sequential data. Thus, taking advantage of transformers could lead to better predictions of wind speeds and should be evaluated thoroughly. 

Overall, the results obtained support the idea that leveraging deep learning tactics could be useful in the prediction of wind speed. To elaborate, predicting wind speeds allow us to know the intervals of time where wind turbines could be the most useful. This knowledge is essential given that we are not currently capable of being fully reliant on renewable sources of energy. Therefore in order to make sure that the energy grid doesn’t collapse and we can transition away from fossil fuels, having a system in place in which we can leverage wind energy when its most useful is integral. Beyond this, we believe that this  this speaks to the larger societal implications of building a society where we are less reliant on energy sources that exaggerate global warming
