---
title: "Autonomous Salt Dispensing Robot"
author_profile: true
key: 30
toc: true
excerpt: "3D SLAM, Planning, Navigation, ROS2, Nav2, RTABMap"
header:
  teaser: /assets/images/KUKA_best_run.gif
classes: wide
---

The goal of this project is to create a robotic system that can dispense salt in an open space by autonomously mapping the space, planning a path to cover the free area, and navigating through the planned path. This is achieved through the use of the Clearpath Jackal robot with a Velodyne VLP16 3D lidar scanner. I used a custom planner integrated with rtabmap and the Nav2 stack in ROS2 Humble to command the robot. This project uses custom ROS2 nodes written in Python 3 and C++

I chose this task as my individual winter project because I want to solve common problems that many people face while pursuing my passion for mobile robots. Walkway safety is a huge concern for anyone who lives in cold and icy places, and automating this process allows users to mitigate risk of falling and frostbite from the weather. While Chicago presented a warmer than usual winter, I still value this project both for the problem statement and for the 

## Video Demo
<iframe 
width="560" 
height="315" 
src="https://www.youtube.com/embed/LJ4xiuIVoO8?si=NtbWAQEJ0LZpNfXN" 
title="YouTube video player" 
frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
allowfullscreen></iframe>

## Main Hardware Components
  - Clearpath Jackal Robot running ROS2 Humble on Ubuntu 22.04
  - Velodyne VLP16 3D lidar
  - Custom Hitch Mechanism 
  - Linksys E8450 Router with OpenWRT firmware 23.05
  - User Laptop (also running ROS2 Humble on Ubuntu 22.04)
  - AGRI-FAB Tow Broadcast Spreader Trailer

## Main Software Packages
- The current configuration of the robot
- The current joint velocities
- The maximum velocity for the joints
- The timestep for the calculations

 This function then returns the configuration of the robot for the next timestep.

The second function I wrote serves as the control loop that ties the desired trajectory of the end effector to the actual motion of the end effector. A simple diagram of the control loop is seen below:
![control_loop]({{ site.url }}{{ site.baseurl }}/assets/images/youBot_control.png)

## Feedforward Control
To calculate the control law, I calculated the current actual end-effector configuration X(q,θ) as a function of the chassis configuration q and the arm configuration θ. The values (q,θ) come directly from the kinematic simulation results (implying "perfect" sensors)

The output of this control yeilds the commanded end-effector twist expressed in the end-effector frame. To turn this into commanded wheel and arm joint speeds, I calculate the pseudoinverse of the mobile manipulator Jacobian Je(θ) to get the appropriate robot dynamics.

## Joint Limiting Exploration
As a bonus activity, I explored adding joint limits to the youBot to simulate a more realistic scenario. In the CoppeliaSim program, the robot does not have limited joints, so the robot is able to cause collisions with itself if not constrained. To remedy this, I added a catch to evaluate whether the 3rd and 4th joints in the arm of the robot are greater than pi in the next state. If the condition yields true, then the joint positions and velocities are updated for the next state to ensure that they do not violate the constraint. Below is a video of the robot employing the joint constraints.

## Bonus Video Demo
<iframe width="560" height="315" src="https://www.youtube.com/embed/VQ7Lpov6QGI?si=AbYCmGih9wy6BYSO" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Source code
[Github repo](https://github.com/Schelbert197/modern_robotics_projects/tree/main/modern_robotics_projects/Schelbert_Srikanth_capstone_project)