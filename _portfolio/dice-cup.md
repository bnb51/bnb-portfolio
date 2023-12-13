---
title: "2D Die in a Cup Simulation from Scratch"
author_profile: true
key: 9
excerpt: "Lagrangian Dynamics, SymPy"
header:
  teaser: /assets/images/314.gif
---

{% comment %} 
sidebar:
  - title: "Role"
    image: http://placehold.it/350x250
    image_alt: "logo"
    text: "Designer, Front-End Developer"
  - title: "Responsibilities"
    text: "Reuters try PR stupid commenters should isn't a business model"
{% endcomment %} 

This project simulates a die bouncing in a spinning cup by modeling the Lagrangian dynamics in this 6 DoF system with collisions and forcing.

## Video Demo
<iframe
    width="100%"
    height="50px"
    src="https://www.youtube.com/embed/T6qRhCT54ms"
    frameborder="0"
    allow="autoplay; encrypted-media"
    allowfullscreen
>
</iframe>

## Euler Lagrange Equations
The Euler Lagrange equations are derived by finding the body velocities of the box and the dice, defining the inertial matrices, calculating the kinetic energy and potential energy (so that we have the Lagrangian), and using the Lagrangian to derive the E-L equations. 

## Constraints
The die can only have an impact with the box with its four corners. To define the constraints of this system, we can simplify it by only considering when the four corners of the dice touches the four edges of the box. Using the frames defined, we can define phi with the above reasoning. 

## External Forces
There are two external forces acting on the system in this simulation. First, is a force that is equal to the force of the box due to gravity to keep it up. This force does not account for the impact of the die on the box, so it does fall slightly as they impact each other. The second force is a rotation force on the box to give it an initial rotational velocity. This force has an inverse relationship with time so it provides an initial acceleration that decreases throughout the simulation.