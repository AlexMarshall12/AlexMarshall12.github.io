---
layout: post
title: Manga Learn!
category: projects
---

#Why Manga?

Manga (漫画 Manga) are comics created in Japan, or by creators in the Japanese language, conforming to a style developed in Japan in the late 19th century.[1] They have a long and complex pre-history in earlier Japanese art.
Manga stories are typically printed in black-and-white,[9] although some full-color manga exist (e.g., Colorful). Colorization of Manga is usually done after it is released in black and white format. This is often avoided because colorization is time-consuming.
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
This is an attempt to combine the two approaches.

##First attempt - polygons

First we try learning through the use of SVG - we take advantage of the fact that manga has larger and more distinct shapes, as well as more solid and uniform colors than black and white photograph images. To put it specifically, for each page of manga, we

1. convert it from png to svg
2. extract the individual polygons as our samples. With the use of SVG format, each polygon is one solid color, a useful trait as we are trying to predict a single color with our model
3. Feature engineer - for each polygon, choose a set of indentifying characteristics that our model will use to predict the color
4. Fit a model to predict a solid color based on these features. Use colored svg polygons to train this model. 
5. test the model

##Step 1

This approach would not have been possible without the fantastic PNG -> SVG tool that [Vector Magic](http://vectormagic.com/) provides. Step 1 was done entirely with this tool. 

##Step 2 & 3

The code for the extraction and feature engineering is found in the svg_parse.py script in the github repo. The features chosen are 

* complexity - the length of the path
* area
* lines - number of straight lines
* curves - number of curved segments

I considered adding a luminance feature which shows the lightness of the polygon but decided against considering how rarely a polygon is something other than black or white in uncolorized manga. 

I also considered adding an index feature. Since each SVG is built in a layered manner, stacking shapes on top of each other, I figured background objects would have a lower index and be predisposed towards lighter colors. However in each manga page, there are several different images in different areas, so its impossible to confirm that the ordering is done consistently across the entire picture in such a manner so I left this out. 

Now, if you look at prior research <insert link>, you will see that a big problem with colorization is averaging. The average of the color spectrum is an unfortunate greyish brown and when a model is trying to predict a color while getting punished for wrong choices, it will basically just throw it's hands up and predict the middle of the road average in all cases just to minimize loss. We want to prevent this from happening. 


Heres the bigun - 

challenges
 
Trying to engineer my own features using svg
really big features
loss function issues - multimodal regression :(
	 categorization - but it has to be special - soft encoding using k nearest neighbors


what I learned: 

break it down man - thats why you use the generated images.... 

stick to 1 damn strategy! Just carry it out - if you are gonna use keras, do it, if you are gonna copy someone elses strategy, do that. Don't bounce back and forth. 

I rank this as a "BIG" challenge. many times when I thought I would just give up. update tbd....


1 interesting thing to learn - the tiling of pieces. 100x100 vs 224x224. which one is better? No prior art.


big question: 
s

1. svg
2. tensorflow
   reshaping to 224 224
    image_slicer 
      adding black space on the side? - that way black can't get converted with bad color...
3. keras
   setup


   its alllll in the pre-processing. my architecture probably does suck. big leaps in accuracy are from me copying richard zhang's preprocessing - KNN softmax so that categories are close together
   


   big ass problem - dealing with that much damn data - apparently image compression is fucking badass - 10 Ive heard but it seems like more. 

   these were some big momma arrays. at first I tried to store everything in hdf5 but clearly that was not the way to go - only 800 images of space. 

   So I had to put VGG back into the pre-processing step.... good thing I commited that to github. 




 GOD DAMMIT - the whole into hdf5 nightmare and then back into images again
 taight me a lot about working with arrays = numpy which really really helps


 3 seperate branches - 1 totally processes everything and stores it first - problem is the size of the files - you just don't get that many images
 2 - storing straight up images and letting the pipeline worry about all the processing  - problem is how slow the pipeline is - good luck feeding in many images per batch
 3 - somewhere in the middle - store images in hdf5 but don't extract hypercolumns or classify colors until trianing time

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








 Steps in colornet: 


 Here are the resulting images: 




