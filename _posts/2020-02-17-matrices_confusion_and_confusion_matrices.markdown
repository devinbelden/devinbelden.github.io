---
layout: post
title:      "Matrices, Confusion, and Confusion Matrices"
date:       2020-02-18 00:27:20 +0000
permalink:  matrices_confusion_and_confusion_matrices
---

Multiclass classification problems present issues not just with creating and running a multitude of models, but also with interpreting those models' results. We see this issue arise time and time again when it comes to black-box models such as convolutional neural networks, etc. We don't necessarily know *how* these models produce their results, even though we can talk at length about the activation functions within each neuron.

Fortunately, there exist models where, not only can we see the results, we can see into the glass box where all the calculations are done, giving us a chance to pinpoint where things went right and wrong. One such model is the decision tree, which I used to help predict the winners of various chess matches. 

As we all know, the idea behind a decision tree is simple: take an observation and sort it through each branch of the entire tree, which was created (or "grown", as it were), based on training observations, to pinpoint the thresholds for each split for each feature of the observations. The final leaf at which a test observation arrives dictates the predicted class for that observation. Now, I won't pretend that a decision tree is more accurate than those black box models to which I referred above, but they are easily tunable once we've peered into the machinations behind each Plinko-esque operation. This obviously hearkens back to the "accuracy versus interpretability" tradeoff, but when accuracy is already acceptably high, we can afford the extra interpretability. Such was the case with my Gradient Boosted Trees algorithm.

Implementing the algorithm is straightforward; after preprocessing the input data, including any one-hot encoding that must be done for categorical variables, it's as simple as the code block below:

```
from sklearn.ensemble import GradientBoostingClassifier

grad = GradientBoostingClassifier()

grad.fit(X_train, y_train)

print(grad.score(X_train, y_train))
print(grad.score(X_test, y_test))

output:
0.959910514541387
0.8111665004985045
```

This particular model (note: I did use a gridsearch to achieve further granularity with the hyperparameters, but this led to an increase in accuracy of only 1%), with this particular train-test split, achieved an 81% accuracy, with a minor amount of overfitting. But what if we wanted to delve deeper into the inner workings? After all, if you have a class imbalance of 81-19, you can achieve the same level of accuracy as a gradient boosted classifier by simply guessing class 1 every time. What if you wanted to see the individual accuracy layers? Perhaps the model is much better at predicting one or two classes over others? How do we even answer that? 

Enter **Confusion Matrices.**

```
from sklearn.metrics import plot_confusion_matrix

sns.set_style(style='ticks')
plot_confusion_matrix(grad, X_test, y_test, normalize='true', display_labels=['White','Black','Draw'], cmap='OrRd')
```

![](https://i.imgur.com/EVxPakB.png)

The above image might take some getting used to (it certainly did for me once my code spat that out), but it's fairly straightforward once you've gotten the decoder ring. The dark squares represent situations that had a high rate of occurrence when predicting the testing data. For example, when the true label was "White", the Gradient Boosted algorithm had an 84% chance of guessing "White" as well. Likewise with Black, the model also had an 84% chance of guessing the true label. However, when the true label ended up being a "Draw", the model guessed correctly a mere 17% of the time, guessing 38% of Draws as "White" and 45% of Draws as "Black" instead.

This isn't altogether awful, but it does reveal that the cost of a high overall accuracy was a low predictability for a rare (5% occurrence) label. Additionally, we can probably guess that the feature measures for the majority of games that end in a draw look remarkably similar to those games that end in either player's victory. Of course, this is all speculation; perhaps there simply isn't enough data to be able to predict draws with anything resembling accuracy or reliability. 

So where do we go from here? Well, it's up to us whether we want to call this project complete (which I did), or if we wish to use a further set of models (such as neural networks) to hopefully increase the overall accuracy. As mentioned before, however, using more complicated models will dim the clarity of the glass around the computations, and will therefore be more difficult to interpret. 
