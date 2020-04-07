---
layout: post
title:      "Understanding What Our Models Understand: A Cautionary Tale"
date:       2020-04-07 00:45:26 +0000
permalink:  understanding_what_our_models_understand_a_cautionary_tale
---


When I was first introduced to convolutional neural networks, I, like others, was captivated by their ability to abstract much of the learning process away from the surface, i.e. to learn on their own without much input from the model's master. This isn't a rare reaction, either; not only has the general public, but students and model creators alike, have expressed this same sentiment in better words than I ever could. CNNs often get compared to things like black boxes seen on airlines, and even to straight up wizardry. As for myself, I'd like to imagine I'll be called a [Tech-Priest](https://warhammer40k.fandom.com/wiki/Tech-Priest) one of these days.

![](https://vignette.wikia.nocookie.net/warhammer40k/images/3/34/Techpriest2.jpg/revision/latest/scale-to-width-down/250?cb=20111026163206)

Actually, bringing up the idea of Tech-Priests is kind of relevant to what I wanted to talk about in this post. I brought up the black box comparison because, truth be told, we *don't* know what's going on inside our models. We can whiteboard them, fine tune the hyperparameters, and train them for days straight, but there's still no way to discern what each individual neuron is doing. While CNNs do have the potential to be the most accurate model type by current metrics, it's important to understand how they achieve this accuracy, and the rationale for their predictions. 

## What CNNs Are, And What They Aren't

CNNs are remarkably similar to the human brain. This is by design, of course; there's a reason the individual nodes within layers are called "neurons", after all. Our brains are incredibly powerful machines that are constantly processing information and learning from it. It makes sense, then, that a digital version of that would be able to emulate the same behavior. With respect to our brain's neurons, they fire in successive order, passing information onto the next neuron, and the next. This is no different than a CNN's neurons. They're each sitting there, doing their own thing, passing on the result of their calculations onto the next layer of neurons. Those neurons have their own take on the input, manipulate it, and pass it on to the next layer. The end result of this Telephone-like operation, then, is the CNN's prediction. Using this process, it "learns" on its own, with little to no post-creation input from humans.

But at its core, a CNN is created using a computer, and a computer is not a brain. For one thing, the former is created by flattening a rock and shooting lightning through it. The latter, on the other hand, is a bigger, more complex organ, which may or may not be fueled by pizza rolls and dark humor*. These differences manifest themselves in a number of different ways. Perhaps the easiest example to grasp (besides the obvious: abstract concepts like love or sarcasm**) is the teachability of the two objects. Humans learn how to classify things after just a few examples, whereas CNNs need thousands, or more, to distinguish which features belong to which category. 

## Use Case: Self-Driving Cars

Take traffic signs for example. Barring obscure edge cases, humans can learn a vast catalog of traffic signs and their meanings, simply by taking a few rides in a car. In fact, if I put a picture of a particular United States traffic sign after this sentence, which I will, chances are that a driver would be able to tell what it is, *even if they've never been to the United States*.

![](https://mynorthwest.com/wp-content/uploads/2020/02/stop-sign-unsplash-620.jpg)

For those of you who don't know, this is a stop sign. People familiar with this sign would recognize the word STOP circumscribed by an octagon so easily, that the octagon could be orange, or green, or blue, and, provided they were paying attention, the driver would still stop for it. Even *children* know what stop signs are. They could pick out this sign even in a sea of traffic, with big buildings on their left, and magnificent trees on their right. This is one bite-sized example of the human brain at work. We humans are extraordinary in our ability to recognize the familiar details in a vast arena of players. 

A computer model, on the other hand, has significantly more difficulty picking out the features of a stop sign within a picture:

![](https://imgur.com/ePH8W7x.jpg)

Here we see the results of a prediction made by a CNN. The raw image is on the left, and the image on the right was produced using a tool called [LIME Image Viewer](https://github.com/marcotcr/lime/blob/master/doc/notebooks/Tutorial%20-%20Image%20Classification%20Keras.ipynb). I won't go into the details of how it works, but the end result is an image separated into superpixels, where the regions of the picture that were considered relevant in deciding what label to predict are colored green (if the feature counted towards the prediction) or red (if the feature counted against the prediction). 

As we can see, the model, even though it was trained on thousands of examples just like the one posted above, doesn't even look at the sign to make its prediction! Even though it got the prediction correct, the model was right for the wrong reasons. This is akin to labeling a basketball as a cat based on its proximity to a litterbox and a food dish. Using context clues is important, but it should by no means be the only method.

## But Why?

![](https://media.giphy.com/media/1M9fmo1WAFVK0/giphy.gif)

I can talk about people's ability to pick out stop signs, or street names, or traffic lights, until my fingers blister over from all the typing, but here's the thing: All those signs were designed specifically to be seen. They were designed by humans who understand how humans detect and process visual stimuli, and, perhaps more importantly, they were designed to be seen by a human brain that evolved over countless millennia. In an attempt to create a neural network, to reverse-engineer our brains, we miss out on all that evolution, all that instinct, and wind up with something that is second-rate at best. 

However, there is something we can take advantage of: Teaching.

Think about it. In school, students aren't trained to learn some new concept by simply looking at a bunch of examples and figuring it out on their own***. They have teachers there to lay the groundwork for the concept and provide verbal aid. By investing just a little bit of extra effort, we can build bridges and shortcuts between novicehood and mastery. So too can we do this with neural networks.

By including additional features within our training data, such as bounding boxes, we can create modified models that understand where exactly to look, such that they might learn what the signs themselves look like. Thus their decisionmaking process is improved, and our trust in their predictions is increased.

## Footnotes

*SCOTUS decision pending.

**You'll see sarcasm brought up a lot in discussing sentiment analysis with regards to natural language processing. It turns out computers have a [pretty hard time](https://blog.infegy.com/sentiment-analysis-and-sarcasm) with it!

***At least not in the schools I attended.
