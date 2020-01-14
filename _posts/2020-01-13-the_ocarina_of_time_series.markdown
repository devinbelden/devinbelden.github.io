---
layout: post
title:      "The Ocarina of Time (Series)"
date:       2020-01-14 02:19:18 +0000
permalink:  the_ocarina_of_time_series
---


Remember The Legend of Zelda: Ocarina of Time? It's one of those classic video games that's very black-and-white. The Hero saves the day by defeating the Big, Bad, Evil Guy, the latter of whom wants nothing more than the destruction of the entire world. It doesn't get any simpler than that. Light overcomes Dark, Courage overcomes Power, and in the case of this particular game, it happens all while being able to see *exactly* what happens if you don't complete your mission. 

![A glimpse of Hyrule into the future](https://twoguysplayingzelda.com/wp-content/uploads/2017/08/Ganons_Castle_Ocarina_of_Time.png)

A scary sight, indeed. How fortunate, then, that Link is able to travel from the past to the future, and back again, in order to prevent this disastrous outcome. But it's one thing to say that a fictional hero can predict (and possibly change) the future, but how are us mere mortals supposed to get our hands on that kind of power?

![](https://media0.giphy.com/media/ujUdrdpX7Ok5W/source.gif)

Only the Sages know for sure, but perhaps one small step towards this sort of Temporal Nirvana, especially for data scientists, is Time Series Analysis. 

## Tackling Prediction With Time Series

When first introduced to time-series data, I was given a rather large dataset from [Zillow](https://www.zillow.com/research/data/). The task was to find a list of the five best ZIP codes in which to invest. Even setting aside the search for the proper definition of the word "best", it was still a bit of a chore figuring out exactly how to tackle this problem. In addition to the other usual data science steps, I found out--preprocessing, massaging, and the like--it's mandatory to have a `datetime` index, so that the time-series modeling can run properly. Unfortunately, this is the easiest part of time-series modeling that I've yet to find, as the future parts of this post will illuminate, and can be done with two lines of code:

```
df['Month'] = pd.to_datetime(df['Month'], errors='coerce')
df = df.set_index('Month', drop=False)
```

Now, we've got to answer the real question here: How much of our data can be attributed to some real, observed trend, and how much of it is simply natural fluctuations, or white noise? This might seem difficult to do by hand, but in practice it is much simpler than that. We look to the `seasonal_decompose` function from the `statsmodels` package, but first, it's good practice to resample our data by month.

```
ts = df.loc[df['ZipCode'] == 85719]['MeanValue']
ts = ts.resample('MS').asfreq()

from statsmodels.tsa.seasonal import seasonal_decompose

seasonal_decompose(ts).plot();

ts_seasonal = seasonal_decompose(ts).seasonal
```

This spits out a visualization showing the decomposition of the observed data into three parts: the trend, the seasonality, and the white noise. 



Given that our seasonality is relatively low when compared to the trend, we can do an ARIMA model without the seasonal component. This will save on time and computing power.
