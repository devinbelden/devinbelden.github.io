---
layout: post
title:      "Attempts and Techniques to Detect and Eliminate Multicollinearity"
date:       2019-11-08 02:21:18 +0000
permalink:  attempts_and_techniques_to_detect_and_eliminate_multicollinearity
---


In discussing large-scale datasets, we often talk about the difficulties of and rationales behind omitting certain columns and keeping others. One reason for eliminating a column is because it is merely a placeholder, such as an arbitrary ID number, and will certainly have no effect, linear or otherwise, on our target variable. Sometimes the data in one or more columns is so mangled, with so little hope of restoring it, that it is in everyone's best interest if we put those columns out of our misery. Another particularly common reason for column elimination, especially when employing linear regression, is **multicollinearity**. 

How do we detect multicollinearity? The simplest way, I've found, is to employ a correlation heatmap, located in the seaborn package:

```
seaborn.heatmap(abs(df.corr()), annot=True);
```

This will create a heatmap based on the absolute value of the correlation coefficients between each variable (obviously, keeping in `abs` or omitting it is personal preference, but I like to minimize the colors on my screen when I've already got a ton of information at which to look). Not only can you use this to find multicollinearity, but since it includes the target variable, it will also give you a jumping off point for feature selection. 

If you've used this technique in your EDA, and you've found multicollinearity (which you most likely have), it only really has one solution, which is to delete all but one of the collinear columns. The reasoning behind this is similar to passing in `drop_first = True` when one-hot encoding categorical variables; the level of information contained in the model is not lost, but we still don't have perfect collinearity between variables. 

It is worth noting that a correlation heatmap should be used not only when exploring data, but also after employing data engineering techniques such as outlier removal. A word of caution, however, is not to delete entire columns simply because they have an unacceptably high correlation coefficient with another column. When working with King County house pricing data, for example, I found two columns (square footage and housing grade) to have relatively high collinearity (0.76), but there was hardly a business reason to not talk about both of those things. I decided to keep it in the dataset, and I did another check for this collinearity after the feature engineering stage. Once the outliers were removed, the features were standardized, and the model was created, both features were removed, one at a time, to see their sole effect on the final R-squared value. If there was true collinearity, the R-squared value would have stayed the same, but, as the R-squared went down both times, I was confident that both features brought unique information to the model.
