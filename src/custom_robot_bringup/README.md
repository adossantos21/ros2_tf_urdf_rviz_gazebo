# Notes regarding the custom robot

When you execute `ros2 launch custom_robot_bringup custom_robot_maze_gz.launch.xml`, the initial cost map looks decent. However, once you start navigating in an attempt to SLAM, the created map is not great. There are two potential reasons for this (at least there are 2 that have been surmised). NOTE: Launching RQT Graph is very helpful for visualizing the custom robot's setup in the maze world.

1. Drift is causing SLAM to perform terribly. Odometer, LiDAR, and IMU sensor information typically facilitate robust localization and mapping. 
    1. Odometry has been integrated through `gz::sim::systems::DiffDrive`, though the /odom topic in ROS2 is publishing msg type `nav_msgs/msg/Odometry`, where some documentation says the msg type should be `nav_msgs/Odometry`; however, I'm fairly confident the msg type should be `nav_msgs/msg/Odometry`. Custom odometry publisher and subscriber nodes would clear up any ambiguity. Not sure if the /odom topic is supposed to publish to the /tf topic, but it isn't publishing to /tf in your current configuration; this could be a problem.
    2. Your LiDAR sensor is publishing to the /scan topic as intended, where the /slam_toolbox node is a subscriber. Meanwhile, /slam_toolbox node publishes to the /tf topic, which seems correct.
    3. You have an IMU sensor plugin coded and bridged, but the topic is not showing up in RQT Graph. Integrating this sensor could cause SLAM to perform better. Running `gz topic -e -t /imu` does seem to work.

2. Your custom robot's localization and mapping is happening in a 3D Rviz2 environment, whereas turtlebot3 SLAM happens in a 2D Rviz2 environment. This could be the SLAM problem, but is unlikely.

3. There is a reason you have not found as to why SLAM is performing poorly for your robot. Debugging your code and searching the internet are your best bets if (1) and (2) do not solve your issues.