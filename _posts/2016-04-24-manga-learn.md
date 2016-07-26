---
layout: post
title: Manga Learn!
category: projects
---
* TOC
{:toc}

# Why Manga? #

Manga (漫画 Manga) are comics created in Japan, or by creators in the Japanese language, conforming to a style developed in Japan in the late 19th century.[1] They have a long and complex pre-history in earlier Japanese art.
Manga stories are typically printed in black-and-white,[9] although some full-color manga exist (e.g., Colorful). Colorization of Manga is usually done after it is released in black and white format. This is often avoided because colorization is time-consuming.

<img src="http://alexmarshall12.github.io/assets/img/manga-sample.jpg" width="500">


To this point, the goal of this project is to aleviate this time consuming pain of colorization by using machine learning to train a model which will thereby be able to perform automatic colorization. It will train on colorized examples to identify shapes and objects in the manga image which are a consistent color. Then, when fed a black and white manga from the same artist and series, it should be able to guess the fill color by relating the properties of the shape to the training set. The same artist and series is chosen to simlify this procedure. Generalization may be part of expansion of this project if it goes well :).
Now, what manga will we use? What a silly question! One Piece of Course! The inspiring story of a boy who eats the Gomu Gomu no mi (rubber fruit) and is going to be the Pirate King one day! If you haven't read this epic tale, not only will you get a fun glimpse of it while reading through this notebook, but I highly suggest you read the rest.

![One Piece title]({{ site.url }}/assets/img/one-piece-title.png)

First and most importantly, its awesome. This will make it a blast to "examine the data" so to speak.
Second, there are 800 mangas chapters out so far! At 20 pages per chapter, this amounts to 16000 pages to train on, and we will most likely only choose a small subset of this though we can always get more. All one piece chapters are available in many different places online. 
Luffy is only 1/2 of the way through the grand line, so there is tons more manga to come. That means, that unlike complete series such as naruto which have been colorized in full already, utility may come out of the output model from this project for years to come!
Prior art.
There have been a number of previous attempts to do this, divided into 2 categories:
Manga is often auto-colored via this technique. A section is manually colored and a "bucket fill algorithm is used
Machine learning auto-colorization techniques are often pixel based. They work on real life jpgs.

Now, note that all of the code I am about reference lives in [This Repo](https://github.com/AlexMarshall12/manga-learn)

# SVG approach #

First we try learning through the use of SVG - we take advantage of the fact that manga has larger and more distinct shapes, as well as more solid and uniform colors than black and white photograph images. To put it specifically, for each page of manga, we

1. convert it from png to svg
2. extract the individual polygons as our samples. With the use of SVG format, each polygon is one solid color, a useful trait as we are trying to predict a single color with our model
3. Feature engineer - for each polygon, choose a set of indentifying characteristics that our model will use to predict the color
4. Fit a model to predict a solid color based on these features. Use colored svg polygons to train this model. 
5. test the model



## Step 1 ##

This approach would not have been possible without the fantastic PNG -> SVG tool that [Vector Magic](http://vectormagic.com/) provides. Step 1 was done entirely with this tool. 

## Step 2 & 3 ##

The code for the extraction and feature engineering is found in the svg_parse.py script in the github repo. The features chosen are 

* complexity
* length
* area
* lines - number of straight lines
* curves - number of curved segments

I considered adding a luminance feature which shows the lightness of the polygon but decided against considering how rarely a polygon is something other than black or white in uncolorized manga. 

I also considered adding an index feature. Since each SVG is built in a layered manner, stacking shapes on top of each other, I figured background objects would have a lower index and be predisposed towards lighter colors. However in each manga page, there are several different images in different areas, so its impossible to confirm that the ordering is done consistently across the entire picture in such a manner so I left this out. 

## Step 4  & 5 ##

As a first cut, I decided to run a linear regression prediction using those 5 features and the 'r','g','b' values as output labels. You can see the code in the linear_regression.py file on github.

Unfortunately, if you run this code, you will see that the model doesn't perform very well on test data. I get a score of 0.0164566366493. As mentioned in the introduction, one cool aspect of this dataset is that we can evaluate it visually. Note that the linear_regression.py script colors in a test svg with the predicted colors. See the result below...

<img src="http://alexmarshall12.github.io/assets/img/best_guess.png" width="500">

Clearly not good - in fact the color choice seems almost random rather than based on the polygon features like we'd hoped. We could pour more time into improving this model, or perhaps using a random forest regression predictor. Addtionally, we could quantize the output color space into bins and attempt classification instead. These are all valid directions, however I believe the large issue at this point is our feature selection. The polygon itself includes no information about the scene its in, the context of the image, surrouding objects, etc - all things which I believe are necessary to predict the color. 

Meanwhile, reading more about machine learning colorization has confirmed this belief as well as pointed me in the direction of pixel based neural networks

# Neural Networks #

Neural Networks, specifically Convolutional Deep neural netowrks have recently improved image classification and object recognition drastically as compared to other image processing methods. 

Research has shown that they can also be used to predict the color of pixels. A large contributing factor to this ability is the fact that they identify features in the image on various layers of the network. Earlier layers may detect specific features such as edges, while middle layers may detect objects such as luffy's hat, and the final layers may detect the differences between the boxes - in a best case scenarios of course, but this is the idea at least. 

## Pre-trained model ##

By far, the paper I drew the most inspiration from is Richard Zhang's [Colorful Image Colorization](http://richzhang.github.io/colorization/). To begin with, I fed my manga images through his trained network that he graciously put on github for people to try. You can see the result below

![One Piece title]({{ site.url }}/assets/img/rzhang-colorized-manga.png)

kind of gross looking tbh haha. This is not totally unexpected of course - as his network was trained on photographs as opposed to manga pictures. I believe it has a difficult time because such large white spaces are not in photographs whereas in manga they are pretty much the majority of the page - and are exactly what we are trying to color. Thus in immitating his methods, we are taking a leap of faith that the failure of this test is due to a lack of training on the right features, rather than a fundamental difference between photography and animation which prevents learning with such an architecture from being successful in the later case. If this makes us uneasy, let us shelve our discomfort for now and revist at a later time :). 


Unfortunately the code that richard uses to train his network is not online and even if it was, I question whether I could get it to work with caffe - I had a hard enough time just installing it to test the completed model. 


## Training the colornet architecture with manga images ##

Luckily, there are other colorization networks online that use tensorflow - something which I can install and have used before. One such network is well described here: [Automatic Colorization](http://tinyclouds.org/colorize/) with code here: https://github.com/pavelgonchar/colornet. 

A notable difference between this architecture and other colorization architectures is that this uses the popular VGG16 network weights for feature identification. It feeds images forward through this network and then extracts the "hypercolumns" which give summary activations at various layers. These hypercolumns are then fed as features into the colorization half of the network. This essentially saves you the trouble of training the feature identification yourself. 

To start with I fed 10,000 images through the network. Because the network accepts 224 x 224 image sizes, I had to slice my manga pages into seperate squares. Since my  1200 x 780 pages don't quite chop up nicely, I ended up centering each page onto a 1344 x 1344 black backdrop and chopping this into 36 equal 224 x 224 squares. Thus there are some squares that are only black and others which have large black borders. The all black squares are thrown away and not used for training. The partially black ones are used for training. 

Its important to note one potential drawback of this method is that since these squares must be stiched together again after the color is predicted, a difference in their edges will be apparent in the resulting colorized manga page. I haven't determined how big of an issue this will be. There are a couple of ways we might avoid this but for now, we are going to turn a blind eye to this problem as well. This is a research project remember! Perfection is not required :)

 Below are some test images taken at various stages of training. On the left is the black and white input image, the middle is the model's predicted colorization, and the right is the true colorization


![One Piece title]({{ site.url }}/assets/img/1000_0.png)
![One Piece title]({{ site.url }}/assets/img/2000_0.png)
![One Piece title]({{ site.url }}/assets/img/3000_0.png)
![One Piece title]({{ site.url }}/assets/img/4000_0.png)
![One Piece title]({{ site.url }}/assets/img/5000_0.png)
![One Piece title]({{ site.url }}/assets/img/6000_0.png)
![One Piece title]({{ site.url }}/assets/img/7000_0.png)
![One Piece title]({{ site.url }}/assets/img/8000_0.png)
![One Piece title]({{ site.url }}/assets/img/9000_0.png)
![One Piece title]({{ site.url }}/assets/img/10000_0.png)
![One Piece title]({{ site.url }}/assets/img/11000_0.png)
![One Piece title]({{ site.url }}/assets/img/12000_0.png)
![One Piece title]({{ site.url }}/assets/img/13000_0.png)
![One Piece title]({{ site.url }}/assets/img/14000_0.png)
![One Piece title]({{ site.url }}/assets/img/15000_0.png)
![One Piece title]({{ site.url }}/assets/img/16000_0.png)
![One Piece title]({{ site.url }}/assets/img/17000_0.png)
![One Piece title]({{ site.url }}/assets/img/18000_0.png)
![One Piece title]({{ site.url }}/assets/img/19000_0.png)
![One Piece title]({{ site.url }}/assets/img/20000_0.png)
![One Piece title]({{ site.url }}/assets/img/21000_0.png)
![One Piece title]({{ site.url }}/assets/img/22000_0.png)
![One Piece title]({{ site.url }}/assets/img/23000_0.png)
![One Piece title]({{ site.url }}/assets/img/24000_0.png)
![One Piece title]({{ site.url }}/assets/img/25000_0.png)
![One Piece title]({{ site.url }}/assets/img/26000_0.png)
![One Piece title]({{ site.url }}/assets/img/27000_0.png)
![One Piece title]({{ site.url }}/assets/img/28000_0.png)
![One Piece title]({{ site.url }}/assets/img/29000_0.png)
![One Piece title]({{ site.url }}/assets/img/30000_0.png)
![One Piece title]({{ site.url }}/assets/img/31000_0.png)
![One Piece title]({{ site.url }}/assets/img/32000_0.png)
![One Piece title]({{ site.url }}/assets/img/33000_0.png)
![One Piece title]({{ site.url }}/assets/img/34000_0.png)
![One Piece title]({{ site.url }}/assets/img/35000_0.png)
![One Piece title]({{ site.url }}/assets/img/36000_0.png)
![One Piece title]({{ site.url }}/assets/img/37000_0.png)


Again, not terrific results, but only 10,000 images were used. I do think its neat that you can see it start to recognize features. For instance, the 3rd image shows that a foot is colored in. The model realized that the foot, not the surrounding white areas is what is supposed to be colored. You can see this in many places where the right object is being colored but it the wrong color. 

Most of the time, its just kind of a bland brownish grey. There are 2 reasons for this. 

1. We didn't train enough images - no question about it. To truly test this model, we should put all of our manga images into the model. The fact that it can detect features such as feet while not knowing what colors to use is further testament to this. Since the object segmentations/recognition piece has essentially already been trained when we use the VGG16 weights, it makes sense that it would be good at this part while failing at the colorization part. It simply hasn't seen enough examples to know what color a foot should be. This is clearly an avoidable problem - we simply need to feed it more data!

2. The linear nature of a color space and our error function mean that when the model is trying to minimize the loss, it simply guesses the middle of the road - brownish grey. Even if a shape is perfectly recognized as a flag for instance, pure blue is very far away from pure red in euclidean terms which the optimizer uses to judge correctness. Middle of the road greyish brown is relatively close to every true color just because it is in the middle of the color space. Thus when our poor model is trying to predict a flag to be blue or red (both reasonable choices), it learns to throw up its hands and guess greyish brown every time because this minimizes the penalization it sees for each wrong guess. This is a more worrysome issue than the one described above. No matter how many images we train, greyish brown will remain a reasonable choice for a model to guess over and over again. Thus, perhaps a change in achitecture is afoot!

For this, we go back to Richard Zhang's approach. He mentions this issue in his paper and comes up with an execellent workaround. Istead of treating this as a regression problem, we will quantize the output color space and thus the task become classifying each pixel into one of these quantized color bins. Specifically, our task is to predict a probability distribution for each pixel of the image where each probability is the likelyhood of it belonging to a particular bin. We then select the most likely bin and color the pixel as such. This way, rather than judging each guess as "correct" or "incorrect", we can jugde a guess by its predictive power against each bin. This type of evaluation gives an optimizer much more information for it to improve the next time around.


## Custom Model ##

My model borrows heavily from both richard's and the colornet model from pavelgonchar. Here is its architecture:


 ![One Piece title]({{ site.url }}/assets/img/Architecture.png)


As you can see, I am using the LAB colorspace. For each image, I break it into its L and a,b consitutents. The a,b is converted into a 112 bin color probability distribution and fed in as the labels. The L (luminance) becomes the input black and white image. This image is fed into VGG16 and hypecolumns are extracted after the 8th and 15th layer - both pooling layers. 

These hypercolumns are fed into a simple colorization network. 

Here is the loss after the first attempt, tried with 3 different optimizers - sgd with a learning rate of 0.001, momentum of 0.9 and nesterov enabled, adadelta, and adam. 

unfortunately, its hovering at 4850, seemingly no matter what sample I try it on. 

![One Piece title]({{ site.url }}/assets/img/training-loss-on-1000-samples.png)

All 3 seem to run into the same basic bottleneck at the same point, leading me to avoid trying even more different optimizers. 

At this point in the project, I have 2 conflicting emotions. First of all, pride. I am finally training my own custom network! Uncharted waters. No one has ever tried to solve this problem in the way that I am doing it now. This really feels like Im finally doing machine learning. 2nd, I feel overwhelmed. There are no guidelines for solving this type of problem. Uncharted waters are like that. 

My first move is to simplify the network. Input features with size (n_samples,960,224,224) are MASSIVE in comparsion to most neural networks. The conical architecture where the data block is reduced each at each step is also unusual from what I've seen. Thus, in an effort to conform, I think reducing the scale of the whole thing is a good step. 

 My current model is sitting at around 5,800,000 parameters. I have a total of 300,000 sample images to work with

In order to simplify this model, there are several possibilities

1. remove hypercolumns
2. reduce image size. This would add a benefit of increasing number of samples. However I am worried about the patching issue described above
3. remove layers from our colorization network.

I ended up doing all 3. I resized each image down from 224x224 to 50x50 effectively reducing the number of input features by a ratio of 50174/2500 or 18.1. 

![One Piece title]({{ site.url }}/assets/img/ship-224px.png)

![One Piece title]({{ site.url }}/assets/img/ship-50px.png)

original image (above) and scaled image (below). Note obviously the lack of detail on the right. This is an especially detailed image however, so usually it won't be this bad. 

I also only used hypercolumns 8 and 15 after some deliberation. I only wanted 2 layers. Since as you move forward through the network, the hypercolumns give increasingly semantic rather than location specific information, Thus I want the earlier layers as i am interested in pixel specific coloring. So not 22. Then I picked 8 and 15 rather than 3 and 15 as 3 just seems a bit too early to give useful information, especially if it was one of only 2 hypercolumns. 

Finally, removing layers from the colorization network basically came with the territory. 

This reduces my network down to about 1.1 million parameters. When we train again....

![One Piece title]({{ site.url }}/assets/img/smaller-model-loss.png){: .center-image }
![One Piece title]({{ site.url }}/assets/img/batch-64.png){: .center-image }

# Retrospective #


Since this is my first time working with neural networks, and first non-kaggle machine learning project (where I can't copy from others), I figured I should write down some things I've noticed/learned.

1. Learn from others! My first attempts at SVG based processing were not a waste at all - in fact I might try them again. However, reading more on the prior art first would have given me invaluable information about feature selection and algorithm selection/strategy. Apparently, machine learning is more than just throwing classifiers at data! :p

2. Working with massive amounts of data was by far the most time consuming part. Also the most metally taxing part. By the time I actually had input data and labels and was running the neural networks, it felt like a huge relief. Slicing that many images, moving them around, worrying about the size, worrying about memory issues, connection speeds when uploading and downloading them, GPU tax - everything takes time. You have to strategize. You'll want to start certain processes before you go to sleep so that you don't waste time in the morning seeing as they take several hours. This is not a "solvable" problem either. It doesn't go away - you just have to work around it in certain ways. HDF5 is a neat tool but its just another step in the chain of things you have do to prep the data. Unfortunately this also means that you are incentized to train with less data. If you look at everything I tried above, you'll see a pattern that results could have been better with more data. However, its a tradeoff. You can't keep throwing time at a problem, at least not until its proven to be worth it. A better computer can help a lot. I always figured comptuers were as fast as they needed to be until I tried my hand at machine learning. Apparently they have a ways to go.

3. AWS is the second best thing you can do if you wish to solve machine learning problems. The best is to buy your own computer. 2.2xlarge gpu instances are a lot better than a macbook pro laptop (yep I did attempt this), but getting the data onto them and installing everything is a serious PIA. For instance, the current version of tensorflow only works with newer graphics cards than the ones provided by amazon. It also means that every time you add more data, you have to go through the process of uploading it, transfering it, downloading it, etc. Ive done seperate parts of this project on a total of 4 computers (not counting the many many ec2 instances I tried to get things running on intially). Every time you add another computer to the mix, you are increasing the complexity of your workflow. 1 computer would have been the way to go. 


4. Blacks are never colored. You can especially see this in the Kanji (lettering) where a black colored marking is supposedly bright red or purple to accent intensity, violence, suprise, etc. Note that in the case of black and white photographs for which this architecture was designed, this is fine. Black is supposed to stay black. HOwever, here, we are converting this to a black channel and trying to guess its a,b constituents, then converting it back into rbg to be shown. Predition of the black (luminance) channel is not something that the model covers. It assumes that the luminance will not change and for the lettering, this is not the case. It goes from 100% luminance(black) to 50% red. The only way we could forseeably pick this as well is to include this information in the prediction.



