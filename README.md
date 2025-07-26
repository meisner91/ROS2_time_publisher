# ROS2_time_publisher
Simple Time publisher Node - for ROS2 Humble (Jetson Nano Orin)

## Step 1: Setup Your Package
If you don’t already have a package, create one:
```cpp
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_cmake time_publisher --dependencies rclcpp std_msgs
```

## Step 2: Add the C++ Node
Create a file time_publisher_node.cpp inside src/time_publisher:
```cpp
cd ~/ros2_ws/src/time_publisher/src
touch time_publisher_node.cpp
```
Paste this content:
```cpp
#include <chrono>
#include <memory>
#include <rclcpp/rclcpp.hpp>
#include <std_msgs/msg/string.hpp>

using namespace std::chrono_literals;

class TimePublisher : public rclcpp::Node {
public:
    TimePublisher()
    : Node("time_publisher_node") {
        publisher_ = this->create_publisher<std_msgs::msg::String>("current_time", 10);
        timer_ = this->create_wall_timer(1000ms, std::bind(&TimePublisher::publish_time, this));
    }

private:
    void publish_time() {
        auto message = std_msgs::msg::String();
        auto now = this->get_clock()->now();
        message.data = "Current time: " + std::to_string(now.seconds());
        RCLCPP_INFO(this->get_logger(), "%s", message.data.c_str());
        publisher_->publish(message);
    }

    rclcpp::Publisher<std_msgs::msg::String>::SharedPtr publisher_;
    rclcpp::TimerBase::SharedPtr timer_;
};

int main(int argc, char * argv[]) {
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<TimePublisher>());
    rclcpp::shutdown();
    return 0;
}
```

## Step 3: Update CMakeLists.txt
In ~/ros2_ws/src/time_publisher/CMakeLists.txt, update/add:
```cpp
add_executable(time_publisher_node src/time_publisher_node.cpp)
ament_target_dependencies(time_publisher_node rclcpp std_msgs)

install(TARGETS
  time_publisher_node
  DESTINATION lib/${PROJECT_NAME}
)
```
Ensure find_package includes rclcpp and std_msgs.

## Step 4: Build the Package
```cpp
cd ~/ros2_ws
colcon build --packages-select time_publisher
source install/setup.bash
```

## Step 5: Run the Node
```cpp
ros2 run time_publisher time_publisher_node
```
You should see log messages showing the current time in seconds.

## Step 6: Verify with rqt or rqt_topic
```cpp
rqt
```
Or just the topic plugin:
```cpp
rqt --perspective-file `ros2 pkg prefix rqt_gui`/share/rqt_gui/perspectives/Topics.perspective
```
Then:
Go to Plugins > Topics > Topic Monitor
Look for /current_time
Expand it and you should see the time strings being published


