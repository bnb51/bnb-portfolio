---
title: "Autonomous Salt Dispensing Robot"
author_profile: true
key: 1
toc: true
excerpt: "3D SLAM, Planning, Navigation, ROS2, Nav2, RTABMap"
header:
  teaser: /assets/images/saltbot.gif
classes: wide
---

The goal of this project is to create a robotic system that can dispense salt in an open space by autonomously mapping the space, planning a path to cover the free area, and navigating through the planned path. This is achieved through the use of the Clearpath Jackal robot with a Velodyne VLP16 3D lidar scanner. I used a custom planner integrated with RTABmap and the Nav2 stack in ROS2 Humble to command the robot. This project uses custom ROS2 nodes written in Python 3 and C++

I chose this task as my individual winter project because I want to solve common problems that many people face while pursuing my passion for mobile robots. Walkway safety is a huge concern for anyone who lives in cold and icy places, and automating this process allows users to mitigate risk of falling and frostbite from the weather. While Chicago presented a warmer than usual winter, I still value this project both for the problem statement and for the opportunity to pursue desireable robotics skills. 

I am still completing this project, so please enjoy the progress and check back for the conclusion of the post!

## Outdoor
### Video Demo
<iframe width="560" height="315" src="https://www.youtube.com/embed/Ds8fOuWjql8?si=h1Npc1BC5I5yZ3La" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

The above video demonstrates the robot traveling with the salt trailer it uses for this project.

## Main Hardware Components
  - Clearpath Jackal Robot running ROS2 Humble on Ubuntu 22.04
  - Velodyne VLP16 3D lidar
  - Custom Hitch Mechanism 
  - Linksys E8450 Router with OpenWRT firmware 23.05
  - User Laptop (also running ROS2 Humble on Ubuntu 22.04)
  - AGRI-FAB Tow Broadcast Spreader Trailer

## Main Software Components
  - RTABmap package (with dependencies)
  - Nav2 Stack
  - Custom Planner ROS2 Node (written in python) 
  - Custom Waypoint Navigation Node (written in C++)

## High Level Overview
To use this robot, the user first creates a map of the area using RTabmap on the robot. By driving the robot through teleoperation, the robot can use the Velodyne VLP16 lidar to create an occupancy grid describing the area of interest. 

Once a map is present (either from real time mapping or from a presaved map), the user can call a series of services to have the robot autonomously cut the map into cells, plan a path of poses from cell to cell, and navigate to each waypoint in series using custom nodes and the Nav2 stack. To cover the area once as evenly as possible, the waypoints are generated in a way that creates a Hamilton Path where each point is visited once. This becomes a lawmowing like path.
<!-- The below diagram describes the information flow of this system. This dose not describe every node in the system (some background dependencies are omitted), though it does describe the main approach to the problem. -->

<!-- The second function I wrote serves as the control loop that ties the desired trajectory of the end effector to the actual motion of the end effector. A simple diagram of the control loop is seen below:
![control_loop]({{ site.url }}{{ site.baseurl }}/assets/images/youBot_control.png) -->

## Map Slicer Node
To plan the path that the robot intends to take, the map slicer node handles a few tasks that make this possible. First, it listens to the /map topic from rtabmap to ensure that there is in fact a map to be used. If there is no map, the node will alert the user. This node is considered the overall planner for the system.

### Creating waypoints
If a map is present, the user may run the "lean_waypoints" service call which will generate waypoints that the robot can follow. This command involves a few steps:
1. Naively place a waypoint of the specified radius anywhere it can fit within an open space.
2. Run through each waypoint generated, and remove waypoints that are islands or peninsulas (only having 0 or 1 neighbor) to eliminate any waypoints that are unreachable or could trap the robot.
- Since the robot has the trailer, the assumption is that it may not back up.
3. Add an orientation for the robot to have at each waypoint so that it sets up a heading for the next waypoint.
4. Save the waypoints and publish them as a set of markers to be visualized in Rviz.

It is worth noting that a purple puck indicates the area of a waypoint, and the green arrow shows the orientation or heading for the robot. The image below shows a map created in rviz from a Gazebo simulation where the pucks have been placed in the confidently open spaces and the arrows show the direction of travel for the robot to take a "lawnmower" like path.

![sim_path]({{ site.url }}{{ site.baseurl }}/assets/images/sim_path.png)

### Traveling
Once the plan has been written, the user can call the "travel" service that initiates the robot. When the service is called, the state machine will leave the "IDLE" state and switch to the "SEND GOAL" state where the robot will plan a path to the first waypoint. The "SEND GOAL" state interfaces with the nav_node which calls the nav to waypoint action client. The planner then waits in the "AWAITING GOAL COMPLETION" state and echoes the current goal. After some time, the nav_node will respond with either "Succeeded", "Aborted", "Canceled" or "Unknown Error" messages which the planner state machine can triage. 

If the message reads "Succeeded", the planner state machine then switches to the "SEND GOAL" state again and sends the next waypoint to nav_node to plan with Nav2. The image below shows the red path that the robot will take to the next waypoint accounting for the obstacle inflation cost. If there are no more waypoints from the original plan, then the state machine returns to "IDLE" with a message indicating to the user that the route was successfully completed. 

If the message reads "Aborted", the planner state machine will check a flag deciding to reset upon abort. If the flag reads true, the state machine returns to "IDLE" and removes any progress of the overall plan. If the flag reads false, the state machine will alert the user that it is skipping that waypoint in the plan and is looking to move to the next waypoint in the plan. It will switch to the "SEND GOAL" state, send the pose of the waypoint at the next index, and then await completion of the next move. 

In the case that the response is either "Canceled" or "Uknown Error", the state machine simply clears the progress and resets to idle. The user can replan at any time. 

![local_nav]({{ site.url }}{{ site.baseurl }}/assets/images/local_nav.png)

## Nav Node
The nav node is made to handle the interfacing with the Nav2 stack. It has it's own state machine and allows the planner to remain separate from the Nav2 interface. 

### Nav to pose
When the planner sends a pose, the nav node takes in the x, y, and yaw of the pose since the robot is considered to be moving in a 2D map. Like the planner, the state switches from "IDLE" to "SEND GOAL" where the NavigateToPose action client for Nav2 is called. In the "SEND GOAL" state, the nav node attempts to contact the action server. If the server is inactive or the Nav2 node died, then the nav node will log an error and abort the waypoint (which will also alert the planner). 

If the action call succeeds, then the state will switch to "WAIT FOR GOAL RESPONSE". If this state gets a message that the goal was rejected by the Nav2 server, the node alerts the user and returns to idle. This could happen due to no feasible paths being available. If the local plan is generated by Nav2, then the state moves to "WAIT FOR MOVEMENT COMPLETE" where the feedback callback will print the current pose of the robot until the goal is completed. If the robot is unable to move for a specified amount of time (in this case 30 seconds) the movement will be considered aborted.

Once feedback on a goal status is achieved (success, abort, etc.), then the node will respond to the planner. 

### Indoor Planning Video Demo
The video below shows the RViz screen as the robot moves through some of the middle waypoints sent from the planner. The red path shows the local plan it creates as it navigates from point to point. The dark parts of the map are occupied (obstacles) and the colored "shorelines" surrounding the dark areas is the high cost area that the robot attempts to avoid during it's planning. 

<iframe width="270" height="480" src="https://www.youtube.com/embed/z3MtAmL7pZU" title="Saltbot indoor planning" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Progress
This post shows the progress achieved in 8 short weeks of Northwestern's winter quarter. Please check back soon for the conclusion of this project!

## Source code
Due to this project being in progress, the code cannot be shared at this time.

<!-- ## Bonus Video Demo
<iframe width="560" height="315" src="https://www.youtube.com/embed/VQ7Lpov6QGI?si=AbYCmGih9wy6BYSO" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Source code
[Github repo](https://github.com/Schelbert197/modern_robotics_projects/tree/main/modern_robotics_projects/Schelbert_Srikanth_capstone_project) -->