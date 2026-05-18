# **Robotics-Summer-Camp 2026 — Manipulators (ROS 2 + Arduino + MoveIt 2 + Alexa)**

Welcome to the Manipulators Summer Camp.

Hope you all have completed the ROS installation. If not, complete **Week 0 — Software Installation** first. We aim to deliver a beginner‑level understanding of ROS 2 and then help you build a full end‑to‑end project from this repository: **a 3D‑printed robot arm powered by ROS 2 + Arduino, controlled via MoveIt 2, and extendable to Amazon Alexa**.

**NOTE (Very Important):** Always prefer the official ROS docs (ROS Wiki) whenever you’re stuck. Most of the best explanations live there:

- **ROS 2 Tutorials (Humble):** https://docs.ros.org/en/humble/Tutorials.html

---

# **ROS (Robot Operating System) Basics**

## Introduction
In this theme (and most robotics projects), you will use:
- **ROS 2** (Robot middleware)
- **Python and/or C++** (programming)
- **Gazebo + RViz** (simulation + visualization)
- **URDF/Xacro** (robot modeling)
- **ros2_control** (controllers + hardware plugins)
- **MoveIt 2** (motion planning)

If you’re not convinced about ROS, here’s a video worth watching:

- **Bloomberg — Building a Robot Operating System for the Future (YouTube)**

ROS 2 is the successor to ROS 1. It was redesigned to provide better:
- real‑time support
- security
- multi‑robot / multi‑computer networking

---

## Here are the Learning Resources for ROS 2 Basics
Below is the exact set of headings/resources you must learn (same structure as previous year). If a link is missing here, use ROS docs.

### Linux Resources (optional)
- Linux File System Directories
- Linux File Permissions
- Linux File Commands (Important)
- Shell Scripting

### ROS 2 Workspace
You must understand:
- `src/`, `build/`, `install/`, `log/`
- underlay vs overlay
- sourcing setup files
- `colcon build --symlink-install`

### ROS 2 Package
You must understand:
- package types: `ament_cmake`, `ament_python`
- package.xml dependencies
- install rules for launch/config/urdf

### ROS 2 Nodes
You must understand:
- node lifecycle (conceptually)
- node names, namespaces
- publishers/subscribers/services/actions

### ROS 2 Launch
You must understand:
- LaunchDescription, Node actions
- arguments, parameters, remaps

### ROS 2 Topic
You must understand:
- pub/sub model
- message types
- `ros2 topic echo`, `ros2 topic info`, `ros2 topic pub`

### ROS 2 Service
You must understand:
- request/response
- `ros2 service list`, `ros2 service type`, `ros2 service call`

### ROS 2 Action
You must understand:
- goal/feedback/result
- cancelable long‑running tasks
- `ros2 action list`, `ros2 action send_goal --feedback`

### Robotics Simulation Overview
You must understand:
- RViz vs Gazebo
- how robot_description is used
- TF tree basics

---

## Always refer Google or ROS Wiki for any confusion
They are the best resources for learning.

---

# **Python Basics (Required)**
If you’re weak in Python, revise:
- variables, functions
- classes
- lists/dicts
- reading files
- virtual environments

---

# **Week 0 — Software Installation (Mandatory)**

## Install OS
- Ubuntu 22.04

## Install ROS 2 Humble
Follow ROS docs (Debian install).

## Install extra ROS packages used by this repo
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

## Install build + serial dependencies
```bash
sudo apt-get update && sudo apt-get install -y \
  build-essential \
  cmake \
  git \
  python3-pip \
  libserial-dev

pip install pyserial
```

## Install tools
- VS Code
- Arduino IDE

---

# **Week 1 — Introduction to ROS 2**

## Introduction
ROS 2, the successor to ROS 1, is an open-source framework that provides tools, libraries, and conventions to simplify creating complex and robust robot behaviors across various platforms.

### Minimum Learning Targets
Students must be comfortable with:
- Workspace creation and building
- Package creation (C++ and Python)
- Running nodes
- Launching systems with launch files
- Using topics/services/actions

---

# **Week 2 — Linux Basics (Quick)**
- Linux is a family of open source and community-developed operating systems.
- Linux is a UNIX-style OS written in C and Assembly by Linus Torvalds and the Linux community.

### More Detailed Resources (Do Refer!)
- Linux File System Directories
- Linux File Permissions
- Linux File Commands (Important)
- Shell Scripting

---

# **Week 3 — ROS 2 Workspace & Colcon**

## Prerequisites
### Configure ROS 2 environment
Temporary:
```bash
source /opt/ros/humble/setup.bash
```
Permanent:
```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### Install colcon
```bash
sudo apt install python3-colcon-common-extensions
```

## Workspace Basics
A workspace is a directory containing ROS 2 packages.

colcon creates:
- `build/`
- `install/`
- `log/`

### Create a workspace
```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
```

### Build
```bash
colcon build --symlink-install
```

### Source
```bash
source install/setup.bash
```

---

# **Week 4 — ROS 2 Package**

## Creating a package
```bash
ros2 pkg create <pkg-name> --dependencies [deps]
```

### C++
```bash
ros2 pkg create <pkg-name> --dependencies [deps] --build-type ament_cmake
```

### Python
```bash
ros2 pkg create <pkg-name> --dependencies [deps] --build-type ament_python
```

---

# **Week 5 — ROS 2 Nodes**

A node is a participant in the ROS 2 graph. Nodes can:
- publish / subscribe topics
- offer / call services
- run action clients / servers
- provide parameters

## Commands
```bash
ros2 run <package_name> <executable_name>
ros2 node list
ros2 node info /node_name
```

---

# **Week 6 — ROS 2 Launch**

Launch files automate running many nodes.

## Run
```bash
ros2 launch <package_name> <launch_file_name>
```

What students must learn:
- LaunchDescription
- Node
- arguments
- parameters
- remappings

---

# **Week 7 — ROS 2 Services**

Services are call-and-response communication.

## Commands
```bash
ros2 service list
ros2 service list -t
ros2 service type /service
ros2 interface show <srv_type>
ros2 service call /service <srv_type> "{yaml: args}"
```

---

# **Week 8 — ROS 2 Actions**

Actions are for long running tasks: goal, feedback, result.

## Commands
```bash
ros2 action list
ros2 action list -t
ros2 action info /action
ros2 interface show <action_type>
ros2 action send_goal /action <action_type> "{goal_yaml}" --feedback
```

---

# **Week 9 — Robotics Simulation Overview (RViz, Gazebo, URDF, Xacro)**

## RViz
- visualize robot model
- visualize TF frames
- visualize sensor data

## Gazebo
### Gazebo Classic
```bash
ros2 launch gazebo_ros gazebo.launch.py
```

### New Gazebo (gz / ignition)
```bash
ros2 launch ros_gz_sim gz_sim.launch.py
```

## URDF
- unified robot description format

## Xacro
- XML macros for cleaner URDF

---

# **(Intermission) TurtleBot3 & Simulation (Extra Practice)**

## What is a TurtleBot3?
TurtleBot3 is a small, affordable, programmable, ROS-based mobile robot for education and research.

### Key Features
- Modular hardware (Burger/Waffle/Waffle Pi)
- Open-source ROS 2 packages
- Compact and customizable

### Software Architecture
- ROS 2 nodes: driver, teleop, sensors
- Launch files for bringup and simulation

---

## Install TurtleBot3 Simulation (Humble)

### Create workspace & clone repos
```bash
mkdir -p ~/turtlebot3_ws/src && cd ~/turtlebot3_ws/src

git clone -b humble-devel https://github.com/ROBOTIS-GIT/DynamixelSDK.git
git clone -b humble-devel https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git
git clone -b humble-devel https://github.com/ROBOTIS-GIT/turtlebot3.git
git clone -b humble-devel https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git
```

### Install dependencies & build
```bash
cd ~/turtlebot3_ws
sudo rosdep init  # skip if already done
rosdep update
rosdep install --from-paths src --ignore-src --rosdistro humble -y
colcon build --symlink-install
```

### Setup environment variables
Add to `~/.bashrc`:
```bash
echo 'source /opt/ros/humble/setup.bash' >> ~/.bashrc
echo 'source ~/turtlebot3_ws/install/setup.bash' >> ~/.bashrc
echo 'export TURTLEBOT3_MODEL=burger' >> ~/.bashrc
echo 'export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:~/turtlebot3_ws/src/turtlebot3_simulations/turtlebot3_gazebo/models' >> ~/.bashrc
```

Apply:
```bash
source ~/.bashrc
```

### Choose model
```bash
export TURTLEBOT3_MODEL=waffle
```

### Run
Empty world:
```bash
ros2 launch turtlebot3_gazebo empty_world.launch.py
```

Turtlebot3 world:
```bash
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py
```

House:
```bash
ros2 launch turtlebot3_gazebo turtlebot3_house.launch.py
```

Teleop:
```bash
export TURTLEBOT3_MODEL=burger
ros2 run turtlebot3_teleop teleop_keyboard
```

---

## TurtleBot3 Gazebo Fortress Simulation (Optional Advanced)

### Dependencies
```bash
sudo apt update
sudo apt install ros-humble-desktop ros-humble-gazebo-ros-pkgs
```

### Ignition bridge packages
```bash
sudo apt update && sudo apt install -y \
ros-humble-ros-ign ros-humble-ros-ign-bridge \
ros-humble-ros-ign-gazebo ros-humble-ros-ign-gazebo-demos \
ros-humble-ros-ign-image ignition-fortress
```

### Build & source
```bash
sudo apt-get install ros-${ROS_DISTRO}-dynamixel-sdk
rosdep install --from-paths src --ignore-src -r -y
colcon build
source install/setup.bash
```

---

# **MAIN PROJECT — Build the Robot Arm in This Repo**

## Repository Target
We will use the final integrated workspace:
- `Section9_Build/arduinobot_ws`

Inside `Section9_Build/arduinobot_ws/src` you will find:
- `arduinobot_description` (URDF/Xacro/meshes)
- `arduinobot_controller` (ros2_control plugin + controller config)
- `arduinobot_bringup` (launch)
- `arduinobot_moveit` (MoveIt config)
- `arduinobot_firmware` (Arduino)

---

# **Step-by-Step Build (Students must do)**

## Step 1 — Clone and build
```bash
git clone https://github.com/priverse001/Manipulators_ROS2.git
cd Manipulators_ROS2/Section9_Build/arduinobot_ws
colcon build
source install/setup.bash
```

---

## Step 2 — Complete URDF/Xacro (Students Fill Templates)
Edit:
- `.../arduinobot.urdf.xacro`
- `.../arduinobot_gazebo.xacro`
- `.../arduinobot_ros2_control.xacro`

Template:
```xml
<?xml version="1.0"?>
<robot xmlns:xacro="http://www.ros.org/wiki/xacro" name="arduinobot">

  <xacro:arg name="is_ignition" default="true"/>
  <xacro:arg name="is_sim" default="true"/>

  <xacro:include filename="$(find arduinobot_description)/urdf/arduinobot_gazebo.xacro" />
  <xacro:include filename="$(find arduinobot_description)/urdf/arduinobot_ros2_control.xacro" />

  <xacro:property name="PI" value="3.14159265359" />

  <!-- TODO: Add all links and joints -->

</robot>
```

---

## Step 3 — ros2_control overview
ros2_control consists of:
- controller_manager
- controllers
- hardware interface plugin

In this repo:
- sim uses gz/ign ros2_control
- real uses `arduinobot_controller/ArduinobotInterface`

---

## Step 4 — Hardware Interface Plugin Template
Students implement serial communication.

Path:
- `.../include/.../arduinobot_interface.hpp`
- `.../src/arduinobot_interface.cpp`

Template header:
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

}  // namespace arduinobot_controller

#endif
```

---

## Step 5 — Controllers YAML
Path:
- `.../config/arduinobot_controllers.yaml`

Template:
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

## Step 6 — Run Simulation
```bash
cd Manipulators_ROS2/Section9_Build/arduinobot_ws
source install/setup.bash
ros2 launch arduinobot_bringup simulated_robot.launch.py
```

---

## Step 7 — Run Real Robot
1) Upload Arduino code from `arduinobot_firmware`.

2) Confirm port:
```bash
ls /dev/ttyACM*
```

3) Run:
```bash
ros2 launch arduinobot_bringup real_robot.launch.py
```

---

## Step 8 — MoveIt 2
Students must learn:
- planning groups
- controllers integration
- RViz planning

---

## Step 9 — Alexa Integration (Optional)
Students must learn:
- ngrok
- Alexa intents
- mapping voice -> ROS

---

# ✅ Final Checklist
- [ ] ROS 2 + dependencies installed
- [ ] Workspace builds
- [ ] TurtleBot3 sim runs (practice)
- [ ] URDF loads without errors
- [ ] Simulation starts for robot arm
- [ ] Real robot communicates over serial
- [ ] MoveIt planning works
- [ ] Alexa integration works (optional)

---

# References
- https://docs.ros.org/en/humble/Tutorials.html
- https://docs.ros.org/en/humble/Concepts/Basic/About-Nodes.html
- https://docs.ros.org/en/humble/Concepts/Basic/About-Services.html
- https://docs.ros.org/en/humble/Concepts/Basic/About-Actions.html
