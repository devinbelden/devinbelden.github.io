---
layout: post
title:      "Explaining Papers 1: YOLOv1"
date:       2020-04-08 18:07:13 -0400
permalink:  explaining_papers_1
---


This is the first in a series of blog posts explaining scientific papers within the data scientist research community. For this introduction, we'll be taking a look at the popular algorithm [YOLOv1](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Redmon_You_Only_Look_CVPR_2016_paper.pdf) created by FAIR (Facebook AI Research). Unlike object detection models before it, You aim to Only Look Once at any input picture for YOLO to make its classification. Due to this, it is a marked improvement in classification speed, reaching up to 45 fps, on a Titan X card, with a faster version reaching 150+ fps. Since learning how it works will be extremely valuable for my field of autonomous driving, I thought I'd take a closer look at it.

*Note: Halfway into this post, I thought I'd put a note here to future readers that this can get pretty technical.*

## Those That Came Before
Previous image classification and object detection models needed to look at the image multiple times; it has to look once for extracting bounding box proposals, again to classify the objects within the bounding boxes, etc. Additionally, models like Fast R-CNN have separate steps for refining bounding boxes, rescoring the confidence levels, and factoring in context clues based on other detected objects within the input image. The reason this is all so time consuming is that each part of this process must be trained independently, which, according to the authors, is hard to optimize. 

By reframing the problem as one of *regression* that leads to classification, YOLO combines all the interim steps into one simple pipeline, wherein the image is resized and divided into a grid, regions are proposed, and objects are classified. This algorithm is so effectively different from models like Fast R-CNN for multiple reasons: the processing time is cut by more than half, the accuracy is improved, and the entire model can be trained at once.

But this isn't a complete bed of roses; compared to these other models, YOLO is faster, but the authors concede that it somewhat struggles to localize (place) the bounding boxes. This can be viewed as a classic speed-versus-accuracy tradeoff, wherein YOLO can be used in contexts where localization accuracy is not desired as highly as speed. However, in the context of self-driving cars, I would personally say that YOLO isn't the best algorithm, despite being real time or near real time, as localization is incredibly important when traveling at 45 MPH (that said, there are algorithms in use now that are demonstrably better and faster than YOLO at detection, classification, *and* localization, but I digress).

## How Was It Born?
As mentioned before, YOLO combines all the (formerly) separate steps of image classification into one simple neural network. How does this happen? 

**1.** First, the image is resized to 448 x 448, and broken up into a square (**S x S**) grid; the grid cells that contain the center of one particular object or another are responsible for generating the actual detection of that object. 

**2.** For each object, each grid cell generates the same number of bounding boxes **B**, along with confidence scores for each bounding box. Not surprisingly, the algorithm uses IOU between the box proposal and the ground truth in order to score its confidence levels for each bounding box. 

**3.** Each grid cell has its own "softmax" type set of class probabilities for the object contained within it. I use the term "softmax" colloquially because it only generates as many probabilities as there are classes (**C**) to predict, not because the softmax function is literally being implemented here.

Side note: Since the entire process is streamlined into one pipeline, the prediction output is one single tensor for every input image. This input happens to be **S x S x (B*5 + C)**. As an example, the authors note that training the model on the Pascal VOC dataset (which has 20 different classes of objects) outputs a prediction tensor of shape **7 x 7 x (2*5 + 20) = 7 x 7 x 30**.

This algorithm is implemented by way of a neural network, with 24 convolutional layers and 2 fully connected layers. All but one of these layers use the leaky ReLU activation function, and the final prediction layer outputs both class probabilities and bounding box coordinates. These coordinates are parametrized as percentages of image width and height.

## How Did It Learn?
According to the authors, the network was trained for 135 epochs, with a variable learning rate in order to avoid model divergence due to high initial learning rates. Overfitting, a problem I seem to come across a lot (and I know I'm not alone in this!), was avoided by including a fairly hefty 50% dropout layer to avoid co-adaptation between layers. Of course, this being image classification, augmentation is also implemented by way of scaling, translation, image exposure, and color saturation. 

As mentioned before, YOLO reframes image classification as a regression problem. Due to this, it's obvious that we have to have a loss function that must be optimized. The loss function is as follows:

![](https://imgur.com/J6joOVv.jpg)


