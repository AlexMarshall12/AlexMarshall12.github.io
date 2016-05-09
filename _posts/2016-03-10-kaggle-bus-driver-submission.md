---
layout: post
title: Kaggle Bus Driver submission	
category: projects
---

Ok so the one piece manga colorizer is harder than I expected. Still do-able but tricky. In the meantime, Im gonna try a fairly standard image classification problem on Kaggle - [State Farm Distracted Drivers](https://www.kaggle.com/c/state-farm-distracted-driver-detection). As a perfect driver myself, I am shocked and appaled at the need for such a system, but apparently there is a lot of darkness in the world :p 

So this is my first neural network that I completed from start to finish. The entire time I basically had a "points on the board" mentality. Don't worry about perfecting the network, great results even, just make something that works from start to finish and submit it to Kaggle. 

Thus, I basically copied code from the github user - ZFTurbo. I forked his repo, though I wrote it in line by line to see what it was doing - not a complete copy and paste I swear! I changed a few things to make it even simpler like taking out the holdout step. 

So what did I learn 

1. Keras rules. Finally a library that makes me do what I think I should be doing (from what I've read about neural networks). 
2. Points on the board. I learned much more here about the process of training and testing a neural network than I would have by being a perfectionist and trying to micromanage each step
3. Caching! I have never used caching or pickling in my code/scripts before though I have heard of their value. Man, its unbelievable how useful these are, and I probably would have taken about a week longer to do this 
4. My computer can handle some training. The training set was a little over 22,000 images here so my dream of colorizing one piece manga seems achievable without using an AWS GPU instance by necessity. Of course those are bigger images, but lets cross that bridge when we get to it. 

Anyways, props to me for finally doing this and submitting something which ranked #508! Onto the next one. 