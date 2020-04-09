---
layout: post
title:      "Explaining Papers 2: LIME Image Viewer"
date:       2020-04-09 00:23:15 +0000
permalink:  explaining_papers_2_lime_image_viewer
---


For this second entry in this series, I'll be explaining the concepts behind the paper, [“Why Should I Trust You?”, Explaining the Predictions of Any Classifier ](https://arxiv.org/pdf/1602.04938.pdf) (Direct PDF download link). This is the main paper, submitted on ArXiv, that introduces the Local Interpretable Model-agnostic Explanations, or LIME. I talked about this in brief  in a previous blogpost outlining a list of possible desires to [understand what our models understand](https://devinbelden.github.io/understanding_what_our_models_understand_a_cautionary_tale), and now I'd like to expand upon the methods and technicalities of this program. I'll try to keep it as non-technical as I can, but I may fail in that regard at some points, so I apologize in advance.

Note that, while LIME works for both image data and text data, for both classifier and regressor models, I'll only be talking about LIME with respect to image classifiers. It's not that I don't understand the program's application to other styles of models and data (as they are virtually the same save for their visual representations of the individual explanations), but because I find image classification to be the most fascinating of the four options.

# Why Do We Need It?
In the paper, the authors explain at length that it's important we understand why our models are making the right decisions for the right reasons. In the title itself, even, it puts into question the practice of blindly* trusting the accuracy percentage output by our standard scoring methods. 

As LIME is applicable mostly to neural networks, it makes sense that such a black box model might need help further explaining itself than something like a linear regression model. If we can achieve high accuracy with neural networks, especially when comparing them to other modeling techniques, then being able to present a robust and airtight set of reasons for any and all predictions made by that model is a very powerful tool to be boasted.

Again, I talked about this in my post introducing LIME, but I'll reiterate that we want our models to use the appropriate features of the data to arrive at the correct conclusions. After all, if us data scientists can't even trust the model, how do we expect anyone else to? And if nobody trusts our work, then I don't think it's too alarmist to say that our field will disappear within a fortnight. Thus arises the need for us to understand our models such that we may pass that understanding onto our stakeholders. 

# How Does It Work?
Conceptually speaking, the viewer works by transforming uninterpretable feature importance into interpretable feature importance. In the example of image viewing, it transforms features deemed "important" by the image classifier, which are stored as uninterpretable tensors, back into images, and assigns a binary classification to each feature. In other words, if our model takes images as input, LIME outputs its interpretable explanation as another image. 

First, the image is separated into super-pixels based on patches of similarly-colored pixels. If there's a slow, gradient-like change from black to white, LIME might classify all those pixels as belonging to the same super-pixel. If, on the other hand, a stretch of black pixels suddenly changes to white, those black pixels will be in one super-pixel, and the white pixels will belong to another. The prevailing logic is that something that is considered a "feature" will generally be the same color all the way through, or will have a slow change from one side of the feature to the other. This is how things like trees are differentiated from the sky, or how lane lines are differentiated from the road they're painted on. After the image is segmented, LIME can start its explanation.

As for how the explanation is created, we run into our old friend, the **Fidelity-Interpretability Trade-off**. In order to retain the property of model-agnosticity, LIME works by using a different, more interpretable (yet acceptably faithful) model to approximate the predictions made by the models we create. It maximizes this *local* faithfulness by taking samples around a particular area, perturbing them slightly, and watching how the model we've created reacts. If the perturbation of a particular feature changes the model's predicted classification, then we have a clue that the (unperturbed) feature is important towards (or sometimes against!) the predicted label. For example, if we input a picture of a white cat sitting on a stretch of tan carpet, (which, let's pretend, the model has *correctly* labeled as a cat), LIME might attempt perturbations on the carpet in the image, which will not change the model's prediction. If, however, LIME perturbs features like the cat's ears, mouth, tail, or paws, then the model will react accordingly and might change its prediction altogether. This is essentially how LIME can label features as "important" or "not important". 

# Use Case: Traffic Signs
Of course, what would a post about image viewers be without images?**

![](https://imgur.com/ePH8W7x.jpg)

As noted in my [previous post](https://devinbelden.github.io/understanding_what_our_models_understand_a_cautionary_tale), here's an example of LIME's usage in action. On the left we see the raw image; its header indicates that the model implemented (a Keras sequential model trained on ~4000 images) predicted Stop Sign. This is expected, as, I mean, *it's right there*. Any human would be able to tell what sign that is. The model that produced this prediction was 86% accurate across the board. 

The right image, however, indicates the LIME explained instance; here, we can see that the actual sign did not factor into the prediction label in one direction or the other; the model didn't even look at it! It deemed things like fences, sky, and trees to be more important in determining the function of the nearby sign than the actual sign itself. 

This is not an isolated instance, either; virtually all of the predictions made by the model were based on features other than the signs themselves. Thus, we see concrete examples of the need for calling our models' predictions into question, and how understanding what our models understand can lead to better model creation and better communication of findings.
# Footnotes
*Maybe not completely "blindly", per se, but definitely blind in one eye. And some kind of milky film over the other.

**A terrible one.

