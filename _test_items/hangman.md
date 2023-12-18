---
title: "Hangman Game Player"
author_profile: true
key: 1
toc: true
excerpt: "ROS2, MoveIt, OpenCV, Paddle OCR, Force Feedback Control"
header:
  teaser: /assets/images/playing_game.gif
classes: wide
gallery7892:
  - url: /assets/images/setting_up.gif
    image_path: /assets/images/setting_up.gif
    alt: "placeholder image 1"
    title: "Image 1 title caption"
  - url: /assets/images/playing_game.gif
    image_path: /assets/images/playing_game.gif
    alt: "placeholder image 2"
    title: "Image 2 title caption"
---

The aim of this project is to use the Franka Emika Panda 7-DoF arm to play the word game "hangman" with a human player in the loop.

## Group Members
This project is a group effort including Ananya Agarwal, Graham Clifford, Ishani Narwankar, Abhishek Sankar, and Srikanth Schelbert

## Overview
This project is the culmination of 3 weeks of work showcasing the various skills and capabilities gained using ROS2 throughout the course. Our aim is that through playing hangman, our team can showcase robotic manipulation, Opitical Character Recognition (OCR), control theory, apriltag localization, and system design and integration.

The robot begins by initiating a "kickstart" sequence that localizes the board and draws the necessary lines on the board to make the game recognizable. This includes 5 dashes for the word to be guessed, 5 dashes for incorrect guesses, and the location for the hangman when incorrect guesses are made. Once this sequence is complete, then the gameplay loop initiates until the game is complete. The rules of the game are as follows:
- The player shall win by correctly guessing the robot's chosen word before the whole stick figure is drawn.
- The robot will always choose a 5 letter word at random.
- The player may guess a letter or a 5 letter word at any point in the game.
- All incorrect guesses will be counted equally.
- The game ends either when the word is guessed (either letter-by-letter or all at once) or when the player has made 5 incorrect guesses.
{% include gallery id="gallery7892" %}

The steps of the robot's gameplay loop are as follows:
1. Move to face the player and initiate the OCR pipeline.
2. Use the OCR pipeline to read the entry and confirm the confidence is high enough to publish to the game player node.
  - The OCR pipline will turn off once the guess is published.
3. The player node processes the guess for correctness and evaluates what the robot will need to draw and where and publishes to the overall state machine.
4. The state machine processes these characters and positions into poses and trajectories and sends these trajectories to the robot until everything is drawn.
5. Upon completing the play, the robot then returns to facing the player described in step 1 unless the game has ended with a win or loss.

## Approach
This project was divided into a few concise subsystems:
- April Tag localization and calibration
- Force Feedback control and motion planning
- Gameplay and system state machine
- CAD and gripper manipulation
- OCR usage

As the system integrator, main tasks included writing the gameplay node and the state machine for the overal system. 

## Full Gameplay
<iframe 
width="560" 
height="315" 
src="https://www.youtube.com/embed/tvCiYLAyh-0?si=ZKbWAICDMrywSt-V" 
title="YouTube video player" 
frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
allowfullscreen></iframe>

### Video Recap
In the video, the initial kickstart sequence is seen drawing the dashes to setup the game as well as the location in which the hangman will be drawn. Shortly after, I am seen showing the robot the letter "Q" that acts as my guess for the turn. Since the letter is not in the word that the robot chose, my guess is incorrect. This means the letter I guessed is written in the lower portion of the board and the first portion of the stick figure, the head, is drawn. 

It is worth noting that the head looks "scruffy" due to the adjustment from the force feedback control. An added challenge that we chose as a group was to have the robot manipulate the marker without any additional aid from custom gripper attachments. While drawing, the robot will adjust to ensure that the admittance force is within the appropriate force range at each given waypoint on the trajectory. Due to flexion of the board, marker, and robot, this adjustment can be seen visually as it draws. 

To demonstrate how the robot handles a full word (and a correct guess), I temporarily have the robot print the word it chose to the terminal. The word "fight", which was chosen by the robot at the beginning of the game, is my guess. The robot is able to correctly draw the letters in the correct places. All letters are bubble letters, though due to error in the force control, some letters show up better than others. As mentioned, the force control adjusts at every point of the trajectory as it connects the dots. This means that the robot may not know it has fallen out of range until it reaches the next waypoint.

### Future Work
Looking forward, there are some things that we seek to improve for optimized drawing and gameplay. First, a more highly tuned control loop that accounts for the robot having an easier time drawing upwards and sideways strokes than it does downward strokes would significantly improve the fidelity of the writing. Second, a more even distribution of some of the discrete points even on perfectly straight strokes, would allow for better evenness in the letters. 

For future work, the team envisions a mode where the robot changes markers in between each play depending on whether the play was correct or incorrect. Some of the code for this was already written though it could not be effectively implemented in the time given before presentation.


<!-- ## Calibration

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
   - Learning Rate: 0.001 -->

<!-- ## Custom MoveIt Library
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
 - Howard, A., Zhu, M., Chen, B., Kalenichenko, D., Wang, W., Weyand, T., Andreetto, M., & Adam, H. (2017). MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications. [https://doi.org/10.48550/ARXIV.1704.04861](https://doi.org/10.48550/ARXIV.1704.04861)  -->
