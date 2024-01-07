---
layout: post
title:  "Radio modulation"
published: true
author: nemanjan00
image: /assets/radio_modulation.jpg
hero_image: /assets/radio_modulation.jpg
hero_darken: true
show_sidebar: true
---
RF is like jumping into a pool with cold water. At first, you are like *WTF am I doing*, but, then, you realize, you are already creating some waves... 

In this text I will talk how radio is encoding (modulating) data it is sending into RF signal.

### Watching waves

At certain point of your life, you have probably stopped to observe behaviour of waves on a calm surface of a water. 

Imagine there is a bobbing object somewhere in the middle of that water, creating waves. How are they moving? What is their amplitude (how high are they compared to the rest of the water surface)? What happens to them as they move away from the source of bobbing? What is the distance between them?

![Photo of an ripple in water](/assets/ripple.jpg)

Let's start with answering the questions, one by one.

* How are they moving?

If we have an object that is relatively small to water surface we are observing and it is impacting it at 90 degrees to the surface plane, we can expect that it will create circular waves.

* What is their amplitude?

If we think about it, the harder we move that bobbing object, the bigger waves are. So, amplitude of wave is in proportion to the amount of water displaced by object bobbing.

* What is the distance between them?

If we agree that wave is started each time we hit the surface of water, then, it makes sense to understand that distance depends on 2 things. First things is, how fast wave is travelling, second is, how often we impact the surface of water.

We have an method to express those values mathematicaly. 

How often we hit the surface of water in unit of a time would be a frequency of that wave. Let's say we are creating 10 waves a second, we could say that frequency of that wave is 10Hz.

Speed of movement is preaty obvious, we measure how much distance wave travels in unit of time and that is speed of wave.

Or if we want to express relationship between those two values, we can use what is called a wavelenth. Wavelength is distance wave crosses in the interval of one oscillation.

![](/assets/water_surface.png)

### Using waves to send messages

Now that we understand some of the properties of waves, we need to figure out how to meaningfully use them. 

What is the simplest thing we can do? 

#### AM modulation

We can "encode" data in how hard we hit the water. For example, if you are familiar with morse code, you know you can encode each letter in sequences of `-` and `.`, you also need to take a break between letters. 

Imagine that, you are hitting water at predefined intervals, either very hard, very soft or not at all. Somewhere else, where waves reach, someone can measure peaks of waves and determine if you are sending `-`, `.` or space. 

That is what we call AM modulation. This is oversimplified, slow, weird version of AM, but, it is AM modulation.

![](/assets/morse_am.png)

But, what happens if we want to encode more that one character each wave? Well, this is where things get interesting.

![](/assets/am_lobes.png)

At this points, it looks like we have new waves with different frequency that is slightly shifted from main frequency. Imagine if it was analog signal we were multiplying, and not simple integers. Now we understand that by multiplying simple wave with different values, we end up with different frequencies. (The more data we encode, we have more width of frequency, or bandwith)

#### FM modulation

Similar principle applies to FM modulation. In case of modulation, we are not changing how hard we are hitting surface of water, we are changing how often we are doing that. 

In reality, FM modulation takes just a periodic wave and analog signal that is being modulated and changes how fast phase of signal changes based off that value. (or how fast object that is bobbing is moving, over time)

![](/assets/fm.png)

