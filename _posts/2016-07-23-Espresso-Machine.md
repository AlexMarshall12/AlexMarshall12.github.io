---
layout: post
title: Coffee Capsule!
category: projects
---

![One Piece title]({{ site.url }}/assets/img/espresso2.jpg)

Mmmmmm espresso. Delightful and suprisingly hard to make. But where theres a will, theres a way. Say you want a cup of this for yourself. Lets checkout some of your options in making one. 

1. Coffee shop quality machines

![One Piece title]({{ site.url }}/assets/img/images-2.jpeg)

Probably the only option for you if you want true espresso. Huge machines with hard to pronounce italian names made out of specific steel alloys with unique heat transfer properties. Cost upwards of $1500 and take up about 300 cubic feed of counter space. Man they work great though. Shot after shot - consistent, creamy, perfect espresso. In truth, going to a coffee shop is not that hard and probably the recommended option if you want espresso. 

Now the homemade options...

2. Moka Pots
![One Piece title]({{ site.url }}/assets/img/moka-pot.jpg)

Not a bad option. The coffee it makes isn't bad, clearly not espresso though. As you can see in this diagram:

![One Piece title]({{ site.url }}/assets/img/moka-pot3.jpg)

Boiling water on the bottom is forced through the coffee and bubbles over into the collection chamber. This makes really hot coffee - not espresso. 

3. Homemade Espresso machines. 


![One Piece title]({{ site.url }}/assets/img/espresso-home.jpg)

These have the same idea as the coffee shop quality machines and make a noble effort to achieve the same results but are unfortunately bound by the price points of a consumer - not a coffee shop. The truth is, espresso is darn hard to make. Those coffee shop quality machines are big and expensive for a reason....

Now, why is making espresso such a PIA? Well there are a very specific set of requirements that are necessary for it to fabricate into existance - like the birth of a basilisk or creation of a high temperature superconductor. 

You need to force water at a specific temperature ~93C and a specific pressure ~9atm through a dense coffee "puck" composed of a specific grain size, compressed to a certain degree. If you get this just right, you will emulsify the grounds, extracting their oils without any of the bitter minerals or fibers of the beans. This oil and the pressurized water will form a creamy blissful substance known as espresso - all the fuss, no muss. 

Most espresso machines use a rotary pump, boilers, and heat exchangers to carefully craft these requirements and make the espresso. The problem is that this is a complex system. Thats why "real" machines are the size that they are. 

![One Piece title]({{ site.url }}/assets/img/ECM_InternalsB.jpg)

Its also why budget machines, while well intentioned, have difficulties with reliably pulling true epsresso shots - so many things can go wrong. 

Now seeing this state of affairs for myself, I decided to attempt to design a better alternative. I wanted it to be a stovetop option as it seems cleaner, more compact, consistent, and able to be used with multiple heat sources - such as when you are camping. 

Thus I started thinking about current stovetop options and why they failed to replicate the necessary enviroment for espresso. If you look back at the mocha pot - you will see that the water is boiling before it goes through the coffee. It basically uses steam pressure to extract coffee from the grounds. And this is a common theme - pretty much every cheap espresso "solution" uses steam pressure to extract coffee. Why is this? Well the alternative is a pump system - something complex that requires a feedback system and a lot of space - usually counter space. 

Some other sollutions such as piston powered machines are on the market but it is difficult to maintain a 9atm pressure on the grounds with such a setup. Remember, espresso is very fickle! It is relatively easy to end up with "almost" espresso, yet hard to make the real thing. 

So, steam pressure is the "simple" go to. It is easy to achieve steam, and once achieved, almost endless pressure is available. Have you ever tried heating water in a closed system? Please don't - its incredibly dangerous because of all the pressure which is created. More on this danger later....

Now the question becomes, how can we achieve pressure on a stovetop and still ensure that the water forced through the coffee grounds maintains a temperature of 93C rather than 100 (steam). Well, here was my first attempt;

This was my first attempt:


![One Piece title]({{ site.url }}/assets/img/2013-07-10.jpg)


The turbine was intended to take energy away from the system so that the water on top near the espresso would calm down below boiling. I say boiling, but of course, water doesn't boil in a closed system, it just increases in pressure and temperature until the system "opens" creating a makeshift bomb. If this were a real object, I doubt the turbine would spin at all, at least not how I hoped and it certainley wouldn't take enough energy out of the system for the water to exit at less than steam pressure. I guess Im stil not sure exactly what would happen, but I've  moved on to a simpler design. 

In summary, what we are trying to achieve is a pressure of 9atm exactly when a temperature of 93C is reached. This is difficult to control with 1 boiling chamber - My gut feeling is that the temperature of the water will be too high once a pressure of 9atm is reached. Using PV = nRT, we use a Pressure of 9atm or 911925 Pa. 


![One Piece title]({{ site.url }}/assets/img/Drawing.jpg)

Heres the general idea, expertly illustrated. If you increase the temperature of a closed chamber filled with water, the pressure will increase. Boiling will not occur however, since its in a closed system. 







