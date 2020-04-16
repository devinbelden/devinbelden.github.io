---
layout: post
title:      "Explaining Papers 1: YOLOv1"
date:       2020-04-08 18:07:13 -0400
permalink:  explaining_papers_1
---


This is the first in a series of blog posts explaining scientific papers within the data scientist research community. For this introduction, we'll be taking a look at the popular algorithm [YOLOv1](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Redmon_You_Only_Look_CVPR_2016_paper.pdf) created by FAIR (Facebook AI Research). Unlike object detection models before it, You aim to Only Look Once at any input picture for YOLO to make its classification. Due to this, it is a marked improvement in classification speed^. 

Since learning how it works will be extremely valuable for my field of autonomous driving, I thought I'd take a closer look at it^^.

*Note: This can get pretty technical.*

## Those That Came Before
Previous image classification and object detection models needed to look at the image multiple times; it has to look once for extracting bounding box proposals, again to classify the objects within the bounding boxes, etc. Additionally, models like Fast R-CNN have separate steps for refining bounding boxes, rescoring the confidence levels, and factoring in context clues based on other detected objects within the input image. The reason this is all so time consuming is that each part of this process must be trained independently, which, according to the authors, is hard to optimize. 

By reframing the problem as one of *regression* that leads to classification, YOLO combines all the interim steps into one simple pipeline, wherein the image is resized and divided into a grid, regions are proposed, and objects are classified. This algorithm is so effectively different from models like Fast R-CNN for multiple reasons: the processing time is cut by more than half, the accuracy is improved, *and* the entire model can be trained at once.

But this isn't a complete bed of roses; compared to these other models, YOLO is faster, but the authors concede that it somewhat struggles to localize (place) the bounding boxes. This can be viewed as a classic speed-versus-accuracy tradeoff, wherein YOLO can be used in contexts where localization accuracy is not desired as highly as classification speed. However, in the context of self-driving cars, I would personally say that YOLO isn't the best algorithm, despite being real time or near real time, as localization is incredibly important when traveling at 45 MPH (that said, there are algorithms in use now that are demonstrably better and faster than YOLO at detection, classification, *and* localization, but I digress).

## How Was It Born?
As mentioned before, YOLO combines all the (formerly) separate steps of image classification into one simple neural network. How does this happen? 

**1.** First, the image is resized to 448 x 448, and broken up into a square (**S x S**) grid; the grid cells that contain the center of one particular object or another are responsible for generating the actual detection of that object. 

**2.** For each object, each grid cell generates the same number of bounding boxes **B**, along with confidence scores for each bounding box. Not surprisingly, the algorithm uses IOU between the box proposal and the ground truth in order to score its confidence levels for each bounding box. 

**3.** Each grid cell has its own "softmax" type set of class probabilities for the object contained within it. I use the term "softmax" colloquially because it only generates as many probabilities as there are classes (**C**) to predict, not because the softmax function is literally being implemented here.

Side note: Since the entire process is streamlined into one pipeline, the prediction output is one single tensor for every input image. This input happens to be **S x S x (B*5 + C)**. As an example, the authors note that training the model on the Pascal VOC dataset (which has 20 different classes of objects) outputs a prediction tensor of shape **7 x 7 x (2*5 + 20) = 7 x 7 x 30**.

Somewhat predictably, this algorithm is implemented by way of a neural network, with 24 convolutional layers and 2 fully connected layers. All but one of these layers use the leaky ReLU activation function^^^, and the final prediction layer outputs both class probabilities and bounding box coordinates. The size (w, h) of the bounding box is normalized such that they are percentages of the image width and height, and the center (x, y) of the box is parametrized to be offsets of a particular grid cell location. This means that w, h, x, and y will always be between 0 and 1.

## How Did It Learn?
According to the authors, the network was trained for 135 epochs, with a variable learning rate in order to avoid model divergence due to high initial learning rates. Overfitting, a problem I seem to come across a lot (and I know I'm not alone in this!), was avoided by including a fairly hefty 50% dropout layer to avoid co-adaptation between layers. Of course, this being image classification, augmentation is also implemented by way of scaling, translation, image exposure, and color saturation. 

As mentioned before, YOLO reframes image classification as a regression problem. Due to this, it's obvious that we have to have a loss function that must be optimized. The loss function is as follows:

![](https://imgur.com/J6joOVv.jpg)

This one's a doozy, so let's walk through it term by term.

**Term 1:** The measure of error of the *center* of the bounding box in relation to ground truth.

**Term 2:** The measure of error of the *size* of the bounding box in relation to ground truth.

**Term 3 & Term 4:** The measure of confidence error. I group these together because only one of them is "activated" at a time; the first is activated when there is an object to classify/bound (which makes the second term go to 0), and the second is activated when there is no object (which makes the first term go to 0). 

**Term 5:** SSE from class probabilities. 

As can be gleaned from the terms inside each sigma, the measure of error that the authors chose was sum of squared error (SSE), because, "it is easy to optimize, however it does not perfectly align with [the] goal of maximizing average precision. It weights localization error equally with classification error, which may not be ideal." The lambda terms were introduced into the loss function to remedy this, such that the error penalty from bounding box creation (terms 1 and 2) is increased, and the confidence error when there is no object (term 4) is decreased. In other words, **λnoobj = 0.5** and **λcoord = 5**. 

## How Good Is It?
Pretty darn.

Well, "pretty darn" depending on what you need it for. If real-time object detection is what you need, and don't need the localization to be the most accurate thing in the world, then YOLOv1 is for you. If, however, you're looking for something that prioritizes localization over speed, then going with something like Faster R-CNN is more up your alley. 

Of course, YOLO can be used in an ensemble to achieve marginal increases in accuracy as well, but for something like self-driving cars, which require high levels of localization accuracy in real-time, not even a YOLO ensemble method will cut it. That said, YOLO is great for object detection in real-time, and it serves a greater purpose in demonstrating what can be achieved when we are able to, hopefully one day, transcend the accuracy vs. speed tradeoff.

# Footnotes
^YOLO reaches up to 45 fps on a Titan X card, with a faster version reaching 150+ fps.

^^I picked this paper because I originally tried to use this algorithm for my capstone project on [detecting traffic signs](https://devinbelden.github.io/understanding_what_our_models_understand_a_cautionary_tale), but since the vast majority of it is written in C, a language I studied eight years ago for six total minutes, the idea was shelved.

^^^Specifically, the leaky ReLU function equals 0.1x if x is not positive.

