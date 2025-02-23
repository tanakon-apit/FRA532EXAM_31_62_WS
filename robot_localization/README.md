robot_localization
==================

robot_localization is a package of nonlinear state estimation nodes. The package was developed by Charles River Analytics, Inc.

Please see documentation here: http://wiki.ros.org/robot_localization

# FRA532 EXAM
## 1. Getting Started
## 1.1 Intallation package
To installation this project

```bash
git clone https://github.com/tanakon-apit/FRA532EXAM_31_62.git
cd  FRA532EXAM_31_62
colcon build
source install/setup.bash
```
To automate workspace setup every time you open a new terminal, add the following line to your `~/.bashrc` or `~/.bash_profile` file:

```bash
echo "source ~/your_workspace/install/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

## 1.2 Run visualization Rviz.
To start Run Simulation you can run launch file

```bash
$ ros2 launch robot_bridge robot_bridge.launch.py
```
## 1.3 Step to using visualization Rviz.




# 2. System overview
## 2.1 system_interface_diagram

### Block Description

white box: package / exacutable file

green box: subscript topic

blue box: publish topic

blue box: tf

purple box: tunning parameter
## 2.2 rqt_graph 
```bash
rqt_graph
```

# 3. Software
## 3.1 Low Level

### Connect to Micro ROS via WiFi.

### Connect to IMU and publish topic data to ROS2.

### Receive cmd_vel topic from ROS2 to control motors.

### Publish wheel angular velocity to ROS2.

### Program IK to calculate wheel speeds from cmd_vel.

## 3.2 High Level

### Node: Calibrate Sensor

- Find offset & covariane of sensor
- Save offset & covariane into .yaml

### Node: Robot Bridge

- Receive wheel angular velocity to calculate wheel odometry.
- calibrate IMU then publish IMU topic.



# 4. Local Planner

A local planner is use to navigate by constrant of immediate obstacles and make short-term decisions. It's like the brain's reflexes, quickly adjusting movement based on what's directly in front of it. Instead of planning a whole journey, it focuses on the next few steps, considering things like nearby objects, terrain, and the robot's capabilities to choose the best path in real-time. So, while the global planner maps out the overall route, the local planner handles the nitty-gritty details to keep things moving smoothly.

## 4.1 Pure Pursuit Algorithm
Pure Pursuit Algorithm is a method used in robotic or autonomous vehicle control for path tracking. It calculates the steering commands required to follow a specified path by continuously aiming to intercept a reference point on that path. The algorithm works by selecting a point ahead of the vehicle along the desired path. This point serves as the target for the vehicle to reach.The algorithm generates steering commands that guide the vehicle towards the path while maintaining a smooth trajectory.

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; ![image](https://github.com/tuchapong1234/Lab1-Local-Planner/assets/113016544/81d92bd2-316b-437f-a603-689f61e9f2db)

### Pseudo Code

    path [array of point generate from the robot to goal point by nav2]

    lookahead distance [distance between robot position and the goal point from Pure Pursuit Algorithm]

    p_x, p_y [Init empty list]

    current_pose_index [Init select point in path]

    For i from current_pose_index to end of path do

        Append position from the path at i index to p_x and p_y 

    Convert list of p_x and p_y to numpy arrays as (p_x_np and p_y_np)

    distance_array = Calculate the Euclidean distance between p_x_np, p_y_np and robot position 

    Find indices where distance_array is greater than lookahead distance

    Find the index of the minimum distance within indices and set it as min_goal_point_idx

    Update current_pose_index based on min_goal_point_idx and previous current_pose_index

    goal_point = Find the different between path[current_pose_index] (current goal point) and robot position 

    Return goal_point [Return goal_point to use as a local set point for robot to follow]

## 5. Obstacle Avoidance

obstacle avoidance refers to the process of designing and implementing systems that enable robots or autonomous vehicles to detect and navigate around obstacles in their environment. This typically involves using sensors such as cameras, lidar, radar, or ultrasonic sensors to perceive the surroundings, and then employing algorithms to analyze this sensory data and make decisions about how to maneuver to avoid collisions. The algorithms may involve techniques such as path planning, trajectory generation, and control theory to ensure safe and efficient navigation. Conclusion, obstacle avoidance is a critical aspect of autonomous systems design, enabling them to operate safely and effectively in complex and dynamic environments.

## 5.1 Virtual Force Field (VFF) Algorithm

Virtual Force Field (VFF) algorithm is a computational method used for simulating the behavior of interconnected objects within a system. Attraction and Repulsion: Each object in the virtual environment exerts a force on other nearby objects. Contain of two force can be either attract them (like gravity) or repel them (like a magnetic force). VFF algorithm calculates these virtual forces between objects based on their positions and certain properties like mass or charge. For example, if two objects are close together, they might exert an attractive force on each other, trying to pull them closer. If they get too close, they might exert a repulsive force to prevent them from colliding.
VFF algorithm provides a way to simulate the behavior of objects in a virtual environment by modeling the forces acting on them and how they respond to those forces.

&emsp;&emsp;&emsp;&emsp; ![image](https://github.com/tuchapong1234/Lab1-Local-Planner/assets/113016544/48505d37-ea1b-4a79-b81b-fae3bb801062)

### Pseudo Code

    OBSTACLE_DISTANCE [Threshold distance for obstacle influence]
    
    vff_vector [Initialize the VFF vectors containing 3 vector 1. attractive (Goal-directed vector), 2. repulsive (Obstacle avoidance vector), 3. result (Combined vector)]

    vff_vector[attractive] = Find attractive vector from goal_point (position x,y of goal point)

    min_idx = Find the nearest obstacle distance index 
    distance_min = Find the nearest obstacle distance 

    if distance_min less than OBSTACLE_DISTANCE do
        obstacle_angle = Find angle that has the nearest obstacle distance 
        opposite_angle = Make the opposite angle from the obstacle_angle by plus pi to the obstacle_angle 
        complementary_dist = Find the different value of distance_min and OBSTACLE_DISTANCE
        gain = [Weight of the repulsive vector] recommend to use square of complementary_dist

        vff_vector[repulsive_vector] = [Calculate repulsive vector components]
    
    vff_vector[result_vector] = Calculate the resultant vector by combining attractive and repulsive vectors
    
    return vff_vector

## 5. Implementing local planner (Pure Pursuit Algorithm) with Obstacle Avoidance (VFF Algorithm)

You can implement these two algorithms by using a goal point selected from the Pure Pursuit Algorithm as an attractive vector of the VFF Algorithm. These can generate a vector toward the goal but still avoid obstacles by repulsive vectors that generate away from obstacles. The combination of these two vectors can generate a new vector pulling the robot toward the goal and pushing away from obstacles.



### 5.1 Standard VFF Algorithm & Modify VFF Algorithm Testing

### Condition 1: Evaluation in Condition of No Additional Obstacles

1. Evaluating Robot Movement Narrow / Wide Doorway 

2. Evaluating Robot Movement Narrow / Wide Pathways 

3. Evaluating Robot Movement in Narrow Angles / Compact Spaces

### Video of Modify VFF Algorithm Testing

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; [![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/JB25mcsy4QA/0.jpg)](https://www.youtube.com/watch?v=Tkn7DVA4PY4)

### Video of Modify VFF Algorithm Testing

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; [![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/JB25mcsy4QA/0.jpg)](https://www.youtube.com/watch?v=JB25mcsy4QA)

### Condition 2: Evaluation in Condition of Additional Obstacles

1. Evaluating Robot Movement in the middle of Wide Pathways with (Cylindrical / Cube)

<img src="https://github.com/tuchapong1234/Lab1-Local-Planner/assets/113016544/8551e69c-0ab8-4a45-bd59-f6df07123bcd" width="500" height="500">

<img src="https://github.com/tuchapong1234/Lab1-Local-Planner/assets/113016544/6b02df88-5e93-4c46-aed4-4b73824ea775" width="500" height="500">

3. Evaluating Robot Movement offset from the middle of Wide Pathways with (Cylindrical / Cube)

<img src="https://github.com/tuchapong1234/Lab1-Local-Planner/assets/113016544/5da0cc1b-ab2a-45ce-be99-2833a34b9316" width="500" height="500">

<img src="https://github.com/tuchapong1234/Lab1-Local-Planner/assets/113016544/babb0aa4-4d38-4bb4-81de-93b5370934d2" width="500" height="500">


5. Evaluating Robot Movement at the corner with (Cylindrical / Cube)

<img src="https://github.com/tuchapong1234/Lab1-Local-Planner/assets/113016544/23c701c3-f607-4b4e-895a-0db099b17830" width="500" height="500">

<img src="https://github.com/tuchapong1234/Lab1-Local-Planner/assets/113016544/71d2d065-001b-4540-a91e-d4f151fb218a" width="500" height="500">

### 5.2 Comparing Result between Standard VFF Algorithm & Modify VFF Algorithm

### Condition 1: Evaluation in Condition of No Additional Obstacles

| Type       |  Testing Situation       |Standard VFF  | Modify VFF
| :---: | :---:| :---: | :---:
| Narrow    | Doorway  | Fail | Pass |
| Wide  | Doorway  | Pass | Pass |
| Narrow  | Pathways   | Fail | Pass |
| Wide  | Pathways   | Pass | Pass |
| Narrow  | Angles  | Fail | Pass |
| Compact  | Spaces | Fail | Pass |

### Condition 2: Evaluation in Condition of Additional Obstacles

| Obstacle Object       |  Testing Situation      |Standard VFF  | Modify VFF
| :---: | :---:| :---: | :---:
| Cylindrical | middle of Pathways  | Fail | Fail |
| Cube  | middle of Pathways  | Fail | Fail |
| Cylindrical  | offset middle of Pathways  | Pass | Pass |
| Cube  | offset middle of Pathways  | Pass | Pass |
| Cylindrical  | corner  | Fail | Fail |
| Cube | corner  | Fail | Fail |

### Conclusion

From the experiments in case 1, which tested the capabilities of navigation in various points such as tight corners or narrow pathways, it was found that our Modified VFF performed better than the general VFF. This is because it can navigate well in narrow paths, including the ability to reverse out of tight corners, which the general VFF cannot do. As for case 2, both algorithms are able to avoid cylindrical obstacles well, except when they are positioned at the apex of a curve. This is because the repulsive force cannot counteract the attractive force. However, neither algorithm can avoid rectangular obstacles because the repulsive force is insufficient to prevent collisions during evasion maneuvers. The reason for not being able to increase the repulsive force is because it would hinder the robot from navigating narrow passages. Therefore, it can be concluded that the VFF algorithm is not suitable for narrow pathways.





