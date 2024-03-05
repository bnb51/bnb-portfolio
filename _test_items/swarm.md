---
title: "Swarm Shape and Behavior Control"
author_profile: true
key: 20
toc: true
excerpt: "Distributed Control, Swarm Robotics, Pygame"
header:
  teaser: /assets/images/3_radius.png
classes: wide
gallery7831:
  - url: /assets/images/hopcount.png
    image_path: /assets/images/hopcount.png
    alt: "placeholder image 1"
    title: "Original Hopcount"
  - url: /assets/images/N_prop.png
    image_path: /assets/images/N_prop.png
    title: "Original Hopcount"
    alt: "placeholder image 2"
    title: "Propagating N"
  - url: /assets/images/N_near.png
    image_path: /assets/images/N_near.png
    alt: "placeholder image 1"
    title: "Nearly Complete N"
gallery7832:
  - url: /assets/images/N_full.png
    image_path: /assets/images/N_full.png
    alt: "placeholder image 2"
    title: "Full N"
  - url: /assets/images/Northwestern_N.png
    image_path: /assets/images/Northwestern_N.png
    alt: "placeholder image 2"
    title: "N Inspiration"
gallery7833:
  - url: /assets/images/1_radius.png
    image_path: /assets/images/1_radius.png
    alt: "placeholder image 2"
    title: "One Radius"
  - url: /assets/images/2_radius.png
    image_path: /assets/images/2_radius.png
    alt: "placeholder image 2"
    title: "Two Radii"
  - url: /assets/images/3_radius_sq.png
    image_path: /assets/images/3_radius_sq.png
    alt: "placeholder image 2"
    title: "Three Radii"
---

## Overview
As a part of my interest in mobile robots, I took the opportunity to learn about swarm robotics. Many robots seen in industry are deployed in distributed systems to drive value in their space, and the lab exploration into these systems provides valuable experience.

In these labs, I was able to implement 3 unique algorithms based on the research papers describing them.
- [Hopcount Localization](https://www.researchgate.net/publication/221284158_Organizing_a_Global_Coordinate_System_from_Local_Information_on_an_Ad_Hoc_Sensor_Network)
- [Brazil Nut Effect](https://dl.acm.org/doi/10.5555/2343576.2343599)
- [Reynold's Flocking](https://ieeexplore.ieee.org/document/6095129)

Each lab is simulated using differential drive robots that can move, broadcast information, and change color based on their given constraints for the experiment. These simulations are intended to model the real NU coachbots, so added complications like noise and robots "sticking" are added to more realistically simulate the behavior.

## Hopcount Localization
{% include gallery id="gallery7831" %}
The goal of this project is to create a representation of the Northwestern "N" using robots that may not know their ground truth position and may only localize based on their hopcount from two "seed" robots seen in blue and teal. The first image shows that based on the relative hopcounts the robots can correctly display even and odd hops from each seed robot using LED color. 

Using this localization to create a coordinate system, the robots can decide whether to turn purple based on their placement in the coordinate system. As seen in the next pictures, this propogates as the robots correct their accuracy in the coordinate system. Eventually, the robots can make a shape resembling the "N" shown using only their generated coordinate system in spite of the simulated communication noise.

{% include gallery id="gallery7832" %}

## Brazil nut effect spatial sorting

The "Brazil Nut Effect" is a term stemming from scientists noticing that the larger nuts in Muesli cereal would migrate to the top of the box when shaken. This idea using either a vibration table (in the paper) or random motion (in this lab) can be adapted to swarm robotics allowing them to self-sort based on gradient for and relative "size."

{% include gallery id="gallery7833" %}

This project implements the algorithm described in the paper using the control points mentioned below. The sum of each vector allows the robots to eventually separate into groups of small (red), medium (green), and large (blue) radius robots. These trials were run with 1, 2, and 3 different sizes to ensure functionality of the algorithm.

### Control
The motion of each robot is governed by the following three forces:
1. Attraction towards the center of the arena, which emulates the effect of a gravitational pull. 
2. Random motion, which emulates the effect of vibration. 
3. Repulsion from nearby robots, which emulates the relative "size" of the robot based on collision radius.

The photos show the sorting with each of the 3 scenarios where the robots are sorted based on their size.

## Reynolds flocking
![flocking]({{ site.url }}{{ site.baseurl }}/assets/images/flock.png)
<!-- ![flocking]({{ site.url }}{{ site.baseurl }}/assets/images/flock2.png) -->


Reynolds flocking is a model aimed at simulating how birds fly in cohesive flocks that can move as a collective unit. This same behavior is seen in schools of fish. This project implements the algorithm described in the paper on the simulated NU coachbots. Unlike the paper that used a large area with fixed wing aircraft, this experiment was run on diff-drive robots programmed to continuously move forward.

### Control
Each robot is steered by changing their turn rate proportionally to the error between their current heading and their desired heading. The desired heading is given by the weighted sum of the following vectors: 
1. Migration: A vector pointing towards the center inversely proportional to their distance from it.
2. Cohesion: A vector pulling them towards the center of mass of the robots they can hear.
3. Separation: A repulsion vector from all robots within range to ensure they still move as individual robots and minimize collision.
4. Alignment: A mean velocity vector of all robots within communication range to keep an even heading.

## Source Code
Since this is a class project, the source code can not be provided for now. 