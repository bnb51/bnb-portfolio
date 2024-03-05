---
title: "PincherX 100 Pen Thief"
author_profile: true
key: 30
excerpt: "Computer vision, Manipulation, OpenCV, ROS2"
header:
  teaser: /assets/images/pen_grab.png
classes: wide
---

## Project Overview
This was presented as a day-long project during the NU MSR hackathon to encourage students to use open source packages to solve challenges. The goal is to use a PincherX 100 robot to grab a pen.

## Project goal
The goals achieved include:
- Use an OpenCV pipeline to identify the pen.
- Use an Intel RealSense D435i camera to get depth information regarding the pen.
- Implement the ModernRobotics libary to manipulate the robot arm and grab the pen.

## Video Demo
This video demonstrates one of the tests where the robot is able to correctly identify the pen from the CV pipeline, position itself to grab it, and autonomously choose to reach and grab the pen.
<iframe width="270" height="480" src="https://www.youtube.com/embed/8R_yO4_NEuI" title="PincherX 100 Pen Grabber" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Vision pipeline
To help the PincherX 100 Robot Arm to grab a pen, we take advantage 
of the depth and color sensors in the Intel RealSense D435i. 
To locate a pen in the 3D space, I used the following pipeline:
1. Locate the pen using the Hue from the HSV stream of the camera.
  - Other instances of that hue are filtered out.
2. Create a bounded region around the contour of the pen.
3. Calculate the centroid of the pen.
4. Align the depth map to the HSV stream to get the XYZ position of the pen's centroid.
 
After the pen is found in 3D space, the  robot is tasked with moving to the pen and grabbing it. 

## Robot control
Students during this project employed 1 of 3 methods for grabbing the pen:
1. Move to the pen and the user engages the grasp feature.
2. Move to the pen and blindly grab.
3. Move to the pen and only grab if the pen is stil there.

I prefered to have the robot grab the pen only when it assessed that the pen is where it expects and otherwise move back to face the pen if it has moved. This presented a challenge since the robot moves slowly and the depth feed can be somewhat noisy. A Kalman Filter was employed to reduce the noise on the camera and ensure that the feed was trustworthy. 

Since the robot has less than 6 degrees of freedom, the robot is considered kinematically deficient. To avoid singularities when moving towards, I had the robot move in it's most compact position (similar to the sleep position) and turn only at the waist until the robot knew that the pen was within the "field of view" of the gripper. Once this was true, the pen would reach out towards the pen and close the gripper.

## Future Work
I would like to add some features to improve upon this and streamline the thievery of the PincherX 100. While the computer vision portion of this project is fairly robust, I would like to use a running average of the last 5 depth calculations to be able to have greater trust in the position of the pen. On the manipulation side, I would employ the MoveIt library and inverse kinematics to find the position of the pen's centroid and move to grab it. This should allow the arm to move faster and with much greater accuracy.

## Source Code
As this is part of the Hackathon Challenges, the code cannot be released at this time.
