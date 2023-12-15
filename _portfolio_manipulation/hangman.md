---
title: "Hangman Game Player"
author_profile: true
key: 1
excerpt: "ROS2, MoveIt, OpenCV, Paddle OCR, Force Feedback Control"
header:
  teaser: /assets/images/jenga.gif
classes: wide
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

The aim of this project is to use the Franka Emika Panda 7-DoF arm to play the word game "hangman" with a human player in the loop.

## Group Members
This project was a group project including Ananya Agarwal, Graham Clifford, Ishani Narwankar, Abhishek Sankar, and Srikanth Schelbert

## Video Demo
<iframe
    width="100%"
    height="50px"
    src="https://www.youtube.com/embed/aCotjAaHfwM"
    frameborder="0"
    allow="autoplay; encrypted-media"
    allowfullscreen
>
</iframe>

## Overview
The robot begins by initiating a "kickstart" sequence that draws the necessary lines on the board to make the game recognizable. This includes 5 dashes for the word to be guessed, 5 dashes for incorrect guesses, and the location for the hangman when incorrect guesses are made. Once this sequence is complete, then the gameplay loop initiates until the game is complete. The rules of the game are as follows:
- The player shall win by correctly guessing the robot's chosen word before the whole stick figure is drawn.
- The robot will always choose a 5 letter word at random.
- The player may guess a letter or a 5 letter word at any point in the game.
- All incorrect guesses will be counted equally.
- The game ends either when the word is guessed (either letter-by-letter or all at once) or when the player has made 5 incorrect guesses.

The steps of the robot's gameplay loop are as follows:
1. Move to face the player and initiate the Opitical Character Recognition (OCR) pipeline.
2. Use the OCR pipeline to read the entry and confirm the confidence is high enough to publish to the game player node.
  - The OCR pipline will turn off once the guess is published.
3. The player node processes the guess for correctness and evaluates what the robot will need to draw and where and publishes to the overall state machine.
4. The state machine processes these characters and positions into poses and trajectories and sends these trajectories to the robot until everything is drawn.
5. Upon completing the play, the robot then returns to facing the player described in step 1 unless the game has ended with a win or loss.

## Calibration

 - Calibrating the robot relies on adding frames to the tf tree based on known geometry. 
 - The robot is sent to a calibration position, then given an april tag that aligns with the gripper frame. 
 - Once the camera can see the tag, it creates series of transforms from the camera to the tag and finally to the base of the robot. 
 - Calibration pose: 

![Calibration Pose]({{ site.url }}{{ site.baseurl }}/assets/images/jenga-calibration.png)

## Tower Detection

 - We detect the tower using the depth image from an Intel Realsense camera. 
 - Once the top of the tower and the table are found by generating contours on the depth image, we repeatedly scan between these locations looking for partially removed pieces. 
 - The starting orientation of the tower is determined through line detection in OpenCV. 

![Tower Detection]({{ site.url }}{{ site.baseurl }}/assets/images/jenga-tower-detection.png)

## Machine Learning for Hand Detection

 - Overview:
   - We performed transfer learning on a deep neural network to help us detect whether there are hands in the scene so that the robot knows when it should look for a Jenga brick. 
 - Dataset:
   - Video samples of the Jenga tower were taken and separated into two classes: with hand(s) and without hand(s) around. 
   - 1000 images were extracted from each class. 
 - Model: 
   - We performed transfer learning on a pretrained neural network - MobileNets
   - The MobileNet we are using implements depth-wise separable convolutions to build light weight deep neural networks
   - We only train the last layer of the MobileNet to perform our specific task
 - Training:
   - Epochs: 50
   - Batch Size: 16
   - Learning Rate: 0.001

## Custom MoveIt Library
 - Overview:
   - This package allows us to move our robot around while also avoiding objects by using different services. 
 - GetCartesianPath:
   - Moves the end-effector gripper in a straight line from start to end.
   - Uses multiple intermediate waypoints to ensure straight path.
 - GetPositionIK:
   - Calculates possible joint positions of robot from a given start and end position and orientation for the end-effector.
   - Used to Orient the gripper without shifting the position .
   - Used to move the robot when straight movement is not necessary.   
 - GetPlanningScene:
   - The environment around the robot.
   - This allows us to add in the table, camera, and jenga tower to avoid collisions .

![MoveIt Visual]({{ site.url }}{{ site.baseurl }}/assets/images/jenga-moveit1.png)




## Source code
[Github repo](https://github.com/hang-yin/Jenga-Assistance)

## Reference
 - Howard, A., Zhu, M., Chen, B., Kalenichenko, D., Wang, W., Weyand, T., Andreetto, M., & Adam, H. (2017). MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications. [https://doi.org/10.48550/ARXIV.1704.04861](https://doi.org/10.48550/ARXIV.1704.04861) 

## Group members
Hang Yin, Katie Hughes, Liz Metzger, Alyssa Chen