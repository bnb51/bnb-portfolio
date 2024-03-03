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
<iframe width="560" height="315" src="https://www.youtube.com/embed/x_r3QtYOPaI?si=EySl7sIvZ3BBynXz" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Main Hardware Components
  - Clearpath Jackal Robot running ROS2 Humble on Ubuntu 22.04
  - Velodyne VLP16 3D lidar
  - Custom Hitch Mechanism 
  - Linksys E8450 Router with OpenWRT firmware 23.05
  - User Laptop (also running ROS2 Humble on Ubuntu 22.04)
  - AGRI-FAB Tow Broadcast Spreader Trailer

## High Level Overview
The below diagram describes the information flow of this system. This dose not describe every node in the system (some background dependencies are omitted), though it does describe the main approach to the problem.

The second function I wrote serves as the control loop that ties the desired trajectory of the end effector to the actual motion of the end effector. A simple diagram of the control loop is seen below:
![control_loop]({{ site.url }}{{ site.baseurl }}/assets/images/youBot_control.png)

## Map Slicer Node
To plan the path that the robot intends to take, the map slicer node handles a few tasks that make this possible. First, it listens to the /map topic from rtabmap to ensure that there is in fact a map to be used. If there is no map, the node will alert the user. 

### Creating wayopints
If a map is present, the user may run the "lean_waypoints" service call which will generate waypoints that the robot can follow. This command involves a few steps:
1. Naively place a waypoint of the specified radius anywhere it can fit within an open space.
2. Run through each waypoint generated, and remove waypoints that are islands or peninsulas (only having 0 or 1 neighbor) to eliminate any waypoints that are unreachable or could trap the robot.
- Since the robot has the trailer, the assumption is that it may not back up.
3. Add an orientation for the robot to have at each waypoint so that it sets up a heading for the next waypoint.
4. Save the waypoints and publish them as a set of markers to be visualized in Rviz.

It is worth noting that a purple puck indicates the area of a waypoint, and the green arrow shows the orientation or heading for the robot.

### Traveling
Write about nav.

## Bonus Video Demo
<iframe width="560" height="315" src="https://www.youtube.com/embed/VQ7Lpov6QGI?si=AbYCmGih9wy6BYSO" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Source code
[Github repo](https://github.com/Schelbert197/modern_robotics_projects/tree/main/modern_robotics_projects/Schelbert_Srikanth_capstone_project)