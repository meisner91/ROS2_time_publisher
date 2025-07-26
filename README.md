# ROS2_time_publisher
Simple Time publisher Node - for ROS2 Humble (Jetson Nano Orin)

## Step 1: Setup Your Package
If you don’t already have a package, create one:

```cpp
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_cmake time_publisher --dependencies rclcpp std_msgs
```
