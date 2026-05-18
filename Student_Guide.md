# Student Guide — Build the Arduinobot (ROS 2 + Arduino + MoveIt 2 + Alexa)

> Repo: `priverse001/Manipulators_ROS2`

This guide walks students step‑by‑step through building the full robot project in this repository. It includes installation, workspace setup, URDF/Xacro modeling, ros2_control plugins, controllers, MoveIt 2, Arduino firmware, and Alexa integration. Students will complete templates and assemble a working simulated and/or real robot.

---

## 0) Learning Outcomes
By the end, students will be able to:
- Create and build a ROS 2 workspace
- Model a robot arm using URDF/Xacro
- Configure Gazebo + ros2_control
- Implement a hardware interface plugin (C++)
- Configure controllers and MoveIt 2
- Drive a real robot via Arduino serial
- Extend control with voice (Alexa)

---

## 1) Prerequisites
### Operating System
- Ubuntu 22.04 (native or VM)

### ROS 2
- ROS 2 Humble

### Required ROS 2 packages
```bash
sudo apt-get update && sudo apt-get install -y \
  ros-humble-joint-state-publisher-gui \
  ros-humble-xacro \
  ros-humble-ros2-control \
  ros-humble-ros2-controllers \
  ros-humble-moveit* \
  ros-humble-ros-gz-* \
  ros-humble-*-ros2-control
```

### Development tools
```bash
sudo apt-get update && sudo apt-get install -y \
  build-essential \
  cmake \
  git \
  python3-pip \
  libserial-dev

pip install pyserial
```

### Arduino tooling
- Arduino IDE
- USB serial drivers (usually built‑in on Ubuntu)

---

## 2) Workspace Setup
```bash
mkdir -p ~/arduinobot_ws/src
cd ~/arduinobot_ws/src
```

Clone this repo (or fork it first):
```bash
git clone https://github.com/priverse001/Manipulators_ROS2.git
```

For the final build, we use:
```
Section9_Build/arduinobot_ws
```

Students can copy that workspace into `~/arduinobot_ws` or work directly inside it.

---

## 3) Package Map (Final Project)
Inside `Section9_Build/arduinobot_ws/src`:
- **arduinobot_description** → URDF/Xacro, meshes, RViz
- **arduinobot_controller** → ros2_control hardware plugin
- **arduinobot_bringup** → launch files
- **arduinobot_moveit** → MoveIt 2 configuration
- **arduinobot_msgs** → custom ROS messages
- **arduinobot_remote** → remote + Alexa bridge
- **arduinobot_firmware** �� Arduino code

---

## 4) URDF/Xacro Modeling (Robot Description)
### Goal
Model the arm with proper links, joints, and limits.

### Student Tasks
1. Create a Xacro file with all links and joints.
2. Add mesh visuals + collisions.
3. Add correct joint axes and limits.
4. Add a mimic joint for the gripper.

### Template: `arduinobot.urdf.xacro`
Fill in all TODOs.
```xml
<?xml version="1.0"?>
<robot xmlns:xacro="http://www.ros.org/wiki/xacro" name="arduinobot">

  <xacro:arg name="is_ignition" default="true"/>
  <xacro:arg name="is_sim" default="true"/>

  <xacro:include filename="$(find arduinobot_description)/urdf/arduinobot_gazebo.xacro" />
  <xacro:include filename="$(find arduinobot_description)/urdf/arduinobot_ros2_control.xacro" />

  <xacro:property name="PI" value="3.14159265359" />
  <xacro:property name="effort" value="30.0" />
  <xacro:property name="velocity" value="10.0" />

  <xacro:macro name="default_inertial" params="mass">
    <inertial>
      <origin xyz="0 0 0" rpy="0 0 0"/>
      <mass value="${mass}" />
      <inertia ixx="1.0" ixy="0.0" ixz="0.0"
               iyy="1.0" iyz="0.0"
               izz="1.0" />
    </inertial>
  </xacro:macro>

  <link name="base_link">
    <xacro:default_inertial mass="1.0"/>
    <visual>
      <origin rpy="0 0 0" xyz="TODO"/>
      <geometry>
        <mesh filename="package://arduinobot_description/meshes/basement.STL" scale="0.01 0.01 0.01"/>
      </geometry>
    </visual>
  </link>

  <!-- TODO: add all remaining links -->

  <joint name="joint_1" type="revolute">
    <parent link="base_link"/>
    <child link="base_plate"/>
    <origin xyz="TODO"/>
    <axis xyz="0 0 1"/>
    <limit lower="-${PI/2}" upper="${PI/2}" effort="${effort}" velocity="${velocity}"/>
  </joint>

  <!-- TODO: joint_2, joint_3, joint_4, joint_5 (mimic) -->
</robot>
```

---

## 5) Gazebo + ros2_control Hooks
### Template: `arduinobot_gazebo.xacro`
```xml
<?xml version="1.0"?>
<robot xmlns:xacro="http://www.ros.org/wiki/xacro" name="arduinobot">
  <gazebo>
    <xacro:if value="$(arg is_ignition)">
      <plugin filename="ign_ros2_control-system" name="ign_ros2_control::IgnitionROS2ControlPlugin">
        <parameters>$(find arduinobot_controller)/config/arduinobot_controllers.yaml</parameters>
      </plugin>
    </xacro:if>
    <xacro:unless value="$(arg is_ignition)">
      <plugin filename="gz_ros2_control-system" name="gz_ros2_control::GazeboSimROS2ControlPlugin">
        <parameters>$(find arduinobot_controller)/config/arduinobot_controllers.yaml</parameters>
      </plugin>
    </xacro:unless>
  </gazebo>
</robot>
```

### Template: `arduinobot_ros2_control.xacro`
```xml
<?xml version="1.0"?>
<robot xmlns:xacro="http://www.ros.org/wiki/xacro" name="arduinobot">
  <ros2_control name="RobotSystem" type="system">

    <xacro:property name="PI" value="3.14159265359" />

    <xacro:if value="$(arg is_sim)">
      <xacro:if value="$(arg is_ignition)">
        <hardware>
          <plugin>ign_ros2_control/IgnitionSystem</plugin>
        </hardware>
      </xacro:if>
      <xacro:unless value="$(arg is_ignition)">
        <hardware>
          <plugin>gz_ros2_control/GazeboSimSystem</plugin>
        </hardware>
      </xacro:unless>
    </xacro:if>

    <xacro:unless value="$(arg is_sim)">
      <hardware>
        <plugin>arduinobot_controller/ArduinobotInterface</plugin>
        <param name="port">/dev/ttyACM0</param>
      </hardware>
    </xacro:unless>

    <joint name="joint_1">
      <command_interface name="position">
        <param name="min">-${PI/2}</param>
        <param name="max">${PI/2}</param>
      </command_interface>
      <state_interface name="position"/>
    </joint>

    <!-- TODO: add joint_2, joint_3, joint_4, joint_5 -->
  </ros2_control>
</robot>
```

---

## 6) ros2_control Hardware Interface Plugin
### Goal
Implement a hardware plugin that sends joint commands to Arduino over serial.

### Template: `arduinobot_interface.hpp`
```cpp
#ifndef ARDUINOBOT_INTERFACE_H
#define ARDUINOBOT_INTERFACE_H

#include <rclcpp/rclcpp.hpp>
#include <hardware_interface/system_interface.hpp>
#include <libserial/SerialPort.h>
#include <rclcpp_lifecycle/state.hpp>
#include <rclcpp_lifecycle/node_interfaces/lifecycle_node_interface.hpp>
#include <vector>
#include <string>

namespace arduinobot_controller {

using CallbackReturn = rclcpp_lifecycle::node_interfaces::LifecycleNodeInterface::CallbackReturn;

class ArduinobotInterface : public hardware_interface::SystemInterface {
public:
  ArduinobotInterface();
  virtual ~ArduinobotInterface();

  CallbackReturn on_activate(const rclcpp_lifecycle::State &previous_state) override;
  CallbackReturn on_deactivate(const rclcpp_lifecycle::State &previous_state) override;

  CallbackReturn on_init(const hardware_interface::HardwareInfo &hardware_info) override;
  std::vector<hardware_interface::StateInterface> export_state_interfaces() override;
  std::vector<hardware_interface::CommandInterface> export_command_interfaces() override;
  hardware_interface::return_type read(const rclcpp::Time &, const rclcpp::Duration &) override;
  hardware_interface::return_type write(const rclcpp::Time &, const rclcpp::Duration &) override;

private:
  LibSerial::SerialPort arduino_;
  std::string port_;
  std::vector<double> position_commands_;
  std::vector<double> prev_position_commands_;
  std::vector<double> position_states_;
};

} // namespace
#endif
```

### Template: `arduinobot_interface.cpp`
```cpp
#include "arduinobot_controller/arduinobot_interface.hpp"
#include <hardware_interface/types/hardware_interface_type_values.hpp>
#include <pluginlib/class_list_macros.hpp>

namespace arduinobot_controller {

ArduinobotInterface::ArduinobotInterface() {}
ArduinobotInterface::~ArduinobotInterface() {}

CallbackReturn ArduinobotInterface::on_init(const hardware_interface::HardwareInfo &hardware_info) {
  CallbackReturn result = hardware_interface::SystemInterface::on_init(hardware_info);
  if (result != CallbackReturn::SUCCESS) return result;
  // TODO: read port parameter
  return CallbackReturn::SUCCESS;
}

std::vector<hardware_interface::StateInterface> ArduinobotInterface::export_state_interfaces() {
  std::vector<hardware_interface::StateInterface> interfaces;
  // TODO: create state interfaces
  return interfaces;
}

std::vector<hardware_interface::CommandInterface> ArduinobotInterface::export_command_interfaces() {
  std::vector<hardware_interface::CommandInterface> interfaces;
  // TODO: create command interfaces
  return interfaces;
}

CallbackReturn ArduinobotInterface::on_activate(const rclcpp_lifecycle::State &) {
  // TODO: open serial port, init vectors
  return CallbackReturn::SUCCESS;
}

CallbackReturn ArduinobotInterface::on_deactivate(const rclcpp_lifecycle::State &) {
  // TODO: close serial port
  return CallbackReturn::SUCCESS;
}

hardware_interface::return_type ArduinobotInterface::read(const rclcpp::Time &, const rclcpp::Duration &) {
  // TODO: open-loop or read sensors
  return hardware_interface::return_type::OK;
}

hardware_interface::return_type ArduinobotInterface::write(const rclcpp::Time &, const rclcpp::Duration &) {
  // TODO: send serial commands
  return hardware_interface::return_type::OK;
}

} // namespace

PLUGINLIB_EXPORT_CLASS(arduinobot_controller::ArduinobotInterface, hardware_interface::SystemInterface)
```

### Plugin Export: `arduinobot_controller.xml`
```xml
<library path="arduinobot_controller">
  <class name="arduinobot_controller/ArduinobotInterface"
         type="arduinobot_controller::ArduinobotInterface"
         base_class_type="hardware_interface::SystemInterface">
    <description>Arduinobot Hardware Interface</description>
  </class>
</library>
```

---

## 7) Controllers Configuration
### Template: `arduinobot_controllers.yaml`
```yaml
controller_manager:
  ros__parameters:
    update_rate: 100

    joint_state_broadcaster:
      type: joint_state_broadcaster/JointStateBroadcaster

    arm_controller:
      type: joint_trajectory_controller/JointTrajectoryController
      joints:
        - joint_1
        - joint_2
        - joint_3
        - joint_4
      command_interfaces:
        - position
      state_interfaces:
        - position
```

---

## 8) Build + Run (Simulation)
```bash
cd Section9_Build/arduinobot_ws
colcon build
. install/setup.bash
```

Launch simulation:
```bash
ros2 launch arduinobot_bringup simulated_robot.launch.py
```

---

## 9) Build + Run (Real Robot)
1. Upload Arduino firmware from `arduinobot_firmware`.
2. Connect USB cable (check `/dev/ttyACM0`).
3. Launch real robot:
```bash
ros2 launch arduinobot_bringup real_robot.launch.py
```

---

## 10) MoveIt 2 Integration
- Generate MoveIt config package.
- Load robot description + controllers.
- Plan motion in RViz.

---

## 11) Alexa Integration (Optional)
- Configure Alexa skill + ngrok tunnel
- Map utterances to ROS commands
- Test voice control

---

## 12) Student Checklist
- [ ] URDF + joints compile without errors
- [ ] Gazebo spawns robot
- [ ] ros2_control loads successfully
- [ ] MoveIt plans motion
- [ ] Arduino responds to commands
- [ ] Alexa triggers robot behavior

---

## 13) Evaluation Suggestions
- **Pass**: Robot runs in simulation + MoveIt planning
- **Merit**: Real robot moves from ROS commands
- **Distinction**: Voice control via Alexa

---

## 14) Helpful Commands
```bash
ros2 topic list
ros2 control list_controllers
ros2 run rqt_graph rqt_graph
```
