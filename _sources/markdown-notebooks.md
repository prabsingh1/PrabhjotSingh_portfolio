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

# NLP Analysis of Storm Report Final Writeup

## Introduction  
The study of weather has always provided an interesting perspective of how societies perceive
the natural events happening around them. In many ancient civilizations, weather was believed to
be playing an existential role as many religious and spiritual beliefs were tethered to the patterns
of weather that came and went. Although modern methods have largely evolved from this
understanding, the manner by which we describe weather still reveals much about society's
perspective on weather.

Despite the advancement in weather technology, meteorology remains an elusive science to fully
predict and understand. This is largely due to the manifold of variables that surround these
natural phenomena, any of which can change the trajectory, weaken, strengthen a storm. In
addition, contributing factors such as global warming has also added to the complexity of
weather prediction. To elaborate, some evidence suggests rising ocean temperature to be playing
a role in the rise in both the frequency and intensity of extreme weather events over the last
decade.

Given the complex challenge that weather presents, this project aims to utilize weather narrative
data to gain a comprehensive understanding of the evolving nature of weather prediction and
observations. Furthermore, the two main research questions of interest that will be addressed are:

1. How do weather narratives differ between different types of weather?
2. How has the sentiment related to weather narratives changed over time?

Corpus
Weather narratives were obtained from the National Oceanic and Atmospheric Administration storm database. The original file that was scraped featured every storm from the years 1950 to 2020 in the United States. However, after data cleaning and processing, the corpus was limited to the interval of 1993 to 2020. 

The weather narratives themselves are descriptive accounts on what happened during a particular storm and formed by the National Weather Service. Beyond this, the meta data itself has many interesting features. It has highly specific markers such as the location, time, and day of the storm. Then depending on the storm type, other qualifying metrics such as the number of fatalities, magnitude of the tornado, cause of flood etc., were included. For this project, a broad understanding between storm types and the year that they occurred was evaluated. Therefore, the OHCO changed depending on what exact research question was being assessed:

1.	OHCO: [“EVENT_TYPE”, “EVENT_ID”,  "para_num", "sent_num", "token_num"]
2.	OHCO: [“Year”, “EVENT_ID”, "para_num", "sent_num", "token_num"]

To contextualize the corpus prior to textual analysis, figure 1 shows the breakdown of total storms per a storm type. It can be seen that Thunderstorm Wind is by far the most common storm that has an event narrative. That being said, other variations of thunderstorm weather, such as lightning and high wind are also observed. Therefore, there is some blurred distinction between storm types, however, for the sake of this project the given classifications were not changed. 

Beyond storm types, figure 2 shows another interesting take into the corpus. The immediate impression from the line graph is that storms are increasing dramatically over the years of 1993 to 2020. However, this isn’t necessarily the case. The data was filtered to only include storms that feature storm narrative reports. Thus, it is much more likely that storms itself haven’t increased, but rather simply weather narrative styles of reporting have increased. Still, this points to an interesting evolution, as it seems that over the years, this form of describing a storm in narrative format has increased in value. 

```{figure} /images/weathernarratives_figure1.png
---
name: barplot-fig
---
Bar plot indicating the total event types per a storm type from the years 1993 to 2020 in the United States.
```

```{figure} /images/weathernarratives_figure2.png
---
name: lineplot-fig
---
Line Plot showing the total amount of storms happening each year, starting from 1993 to 2020. It can be seen that the reported narratives have increased over these years; however, this does not necessarily mean that storms itself have increased
```

Now that an understanding has been made about the data, the following section will evaluate the research questions that were posed with the use of a variety of ETA methodologies.  


## How do weather narratives differ between different types of weather?
### Similarity and Clustering

To gain an initial understanding about the relationships that different weather events may have in common, clustering based off term similarity was conducted. The dendrogram in figure 3 did not reveal very confident results as the branches weren’t very distinguishable from each other. 

One surprising result was weather events on the polar extremes of each other (i.e., winter storms and thunderstorms) were observed being quite close to each other on the dendrogram. But, due to the lack of clarity in this method, no further interpretations were made.

```{figure} /images/weathernarratives_figure3.png
---
name: hieracrhical-fig
---
Hierarchical clustering via cityblock distance and Ward’s method. Other clustering methods with varying distances were attempted but results were consistent.
```

### Principal Component Analysis (PCA)
Next, principal component analysis was performed to assess if different event types could be separated. The first and second principal components indicated good separation for some distinct event types such as “Thunderstorm Winds” (red), “Marine Thunderstorms” (yellow), and “Hail” (pink). Other event types (after hiding the aforementioned storm types) also exhibited some degree of clustering, however were definitely not as separated. Such results reveal that certain storm types do have distinct repetitive patterns of words that are distinct to them. Event storms that would be expected to be very similar in their textual content such as “Thunderstorm Winds” and “Marine Thunderstorms” were clustered separately. Such a separation can be interpreted to reveal the precise advancements made in our understanding of distinct weather types. 


```{figure} /images/weathernarratives_figure4.png
---
name: pca-fig
---
First and second principal components, with storms colored by classified event type. Distance computing and eigenvectors were computed via scipy and decomposition/PCA via sklearn. 
```

### Topic Modeling (Latent Dirichlet Allocation)
Although clustering and PCA revealed interesting aspects of the corpus, it did not allow us to understand mixtures of a variety of different topics in the text. Therefore, via topic modeling, as opposed to having simply one topic being assigned to a particular weather event, a combination of different topics with differing probabilities are assigned to each weather type. Doing so provides a sort of distribution of labels that enables a more comprehensive view into a text, which in this case are narratives belonging to specific weather events. To specify this further, Latent Dirichlet Allocation (LDA), which can be seen as an intermediate form of topic modeling, was used. Following this, the event type that most likely was associated with a topic was used as the label. Clustering was performed at the last step similar to earlier. 

Results from the outputted dendrogram show many similar types of events being grouped together. For example, weather events that typically drop lots of snow are all seen being clustered at the top together. There were also some peculiar observations noticed from this as well, such as Hurricane being in the next most immediate branch after the snow cluster. But upon further investigation, this makes perfect sense. Hurricanes, in a way, are the rain equivalent to heavy snow events. And if we go a step further and evaluate the words that are involved in that topic, we can see a lot of similarities, most notably being the use of “inches” in the measurement of either snow or rain. 

```{figure} /images/weathernarratives_figure5.png
---
name: lda-fig
---
LDA performed with 4000 terms, 40 topics, and the top 10 terms per topic. Hierarchical clustering was performed next to obtain the dendrogram seen. Top of the dendrogram (purple) shows winter related events being clustered together with also Hurricane events. Another observation (red) shows events such as tornadoes and wildfires being grouped together, believed to be because of terms related to damage profiles. Lastly, high wind events are seen in the bottom (yellow) of the dendrogram clustered together. The sklearn package was used for this. 
```

## How has the sentiment related to weather narratives changed over time?
### Sentiment Analysis
For the other research question, instead of using a OHCO with “EVENT_TYPE” as the highest level, “Year” was used to evaluate the change in sentiment overtime. Figure 6 shows that overtime there is a change in several different emotions. Emotions such as anger, fear, sadness have decreased overtime. One way to interpret this could be that weather events in general have not been attributed with as much of a negative connotation over the years. Adding to this potential theory, emotions such as anticipation, joy, and trust have increased over time. This could be due to the fact that our ability to predict weather has improved, thus leading to overall boost positive emotions such as trust and joy. 

Another observation from the heatmap in figure 6, is the increase in the surprise emotion over the years. This might be reflective of the general increase in the strength of extreme weather-related events. As mentioned earlier, some theories suggest that global warming is likely culprit to some of the anomalies occurring in meteorology, as it is increasing the temperature of our oceans leading to more powerful storms. 

Beyond the general trends that are seen in the heatmap, the year 1996 is a peculiar standalone year with regards to its emotional sentiment. 1996 has the highest anger, disgust, and fear, while also consequently having the lowest anticipation and trust. However, this is likely due to there being just a general less amount of storm narratives available for this year, as is indicated on figure 2. 

```{figure} /images/weathernarratives_figure6.png
---
name: heatmap-fig
---
Heatmap of emotions in sentiment analysis, with year being used as the bag. Scipy was primarily used for distance computation.  
```

## Conclusion
The purpose of this project was to investigate weather narratives that were recorded for storms between the years of 1993 to 2020. The two research questions of interest related to this purpose aimed to analyze how different weather events are composed and how they have changed overtime. With the help of techniques such as clustering, principal component analysis, topic modeling, and sentiment analysis, many interesting observations were made. Firstly, it was shown that there are various elements to the composition of weather narratives that may be generalized to several different event types. This is important as it gives us a glimpse into how society interprets weather phenomena. In addition to this, sentiment analysis was carried out to evaluate the change with regard to emotional state of the text overtime. The results from this indicated several very interesting observations, one of the most notable being the rise in the sentiment of surprise over the years. Overall, the project provided an overview into the textual elements that are prominent in weather narratives, and may be used as a continuous tool to evaluate societal perceptions of natural phenomena around them. 

## References and GitHub 
Emanuel, Kerry. “Assessing the Present and Future Probability of Hurricane Harvey’s Rainfall.” Proceedings of the National Academy of Sciences, vol. 114, no. 48, 13 Nov. 2017, pp. 12681–12684, www.pnas.org/content/114/48/12681.full, 10.1073/pnas.1716222114. Accessed 11 May 2021.  

IPCC, 2014: Climate Change 2014: Synthesis Report. Contribution of Working Groups I, II and III to the Fifth Assessment Report of the Intergovernmental Panel on Climate Change [Core Writing Team, R.K. Pachauri and L.A. Meyer (eds.)]. IPCC, Geneva, Switzerland, 151 pp.  

Yves Jacquot. “Predicting Tornado Magnitude with Machine Learning - Yves Jacquot - Medium.” Medium, Medium, 10 Oct. 2017, medium.com/@yves.jacquot/predicting-tornado-magnitude-with-machine-learning-c76df84d7872. Accessed 11 May 2021.



