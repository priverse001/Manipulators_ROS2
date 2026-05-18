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

# **Week 1 — ROS 2 Workspace**

## Prerequisites
### Configure ROS 2 Environment
You need ROS 2 available in every terminal.

Temporary (per terminal):
```bash
source /opt/ros/humble/setup.bash
```

Permanent (recommended):
```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### Install colcon
```bash
sudo apt install python3-colcon-common-extensions
```

## Workspace Basics
A ROS 2 workspace is a directory that usually contains a `src` directory.

colcon generates:
- `build/` (intermediate build artifacts)
- `install/` (final installed artifacts)
- `log/` (build logs)

## Create a workspace
```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
```

## Build the workspace
```bash
colcon build --symlink-install
```

## Source the environment
```bash
source install/setup.bash
```

## Tips
- To ignore a package: create an empty `COLCON_IGNORE` file in it.

---

# **Week 2 — ROS 2 Packages**

## Create a package
```bash
ros2 pkg create <pkg-name> --dependencies [deps]
```

## C++ package
```bash
ros2 pkg create <pkg-name> --dependencies [deps] --build-type ament_cmake
```

## Python package
```bash
ros2 pkg create <pkg-name> --dependencies [deps] --build-type ament_python
```

---

# **Week 3 — ROS 2 Nodes**

## What is a node?
A node is a participant in the ROS 2 graph. Nodes communicate via:
- topics
- services
- actions
- parameters

## Commands
```bash
ros2 run <package_name> <executable_name>
ros2 node list
ros2 node info /node_name
```

---

# **Week 4 — ROS 2 Launch**

Launch is used to start multiple nodes with one command.

## Run
```bash
ros2 launch <package_name> <launch_file_name>
```

## What students must learn
- LaunchDescription
- Node
- parameters
- remappings
- namespaces

---

# **Week 5 — ROS 2 Services**

Services are request/response.

## Commands
```bash
ros2 service list
ros2 service list -t
ros2 service type /service_name
ros2 interface show <srv_type>
ros2 service call /service_name <srv_type> "{yaml: args}"
```

---

# **Week 6 — ROS 2 Actions**

Actions are for long running tasks.

## Commands
```bash
ros2 action list
ros2 action list -t
ros2 action info /action_name
ros2 interface show <action_type>
ros2 action send_goal /action_name <action_type> "{yaml: goal}" --feedback
```

---

# **Week 7 — Simulation + Visualization Overview**

> This section is a quick overview of tools. You must explore each deeper while doing the project.

## RViz
RViz is used for:
- viewing robot model
- TF frames
- sensor data
- MoveIt planning scene

## Gazebo
Gazebo simulates physics.

### Gazebo Classic (Gazebo 11)
```bash
ros2 launch gazebo_ros gazebo.launch.py
```

### New Gazebo (Ignition / gz)
```bash
ros2 launch ros_gz_sim gz_sim.launch.py
```

### RQT tools
```bash
ros2 run rqt_gui rqt
ros2 run rqt_graph rqt_graph
ros2 run rqt_plot rqt_plot
ros2 run rqt_console rqt_console
```

---

# **Week 8 — URDF + Xacro**

## URDF
URDF describes:
- links
- joints
- collision
- inertia

## Xacro
Xacro helps:
- reuse macros
- define properties
- split robot into multiple files

---

# **MAIN PROJECT — Build the Robot in This Repo**

## Repository Target
We will use the final integrated workspace:

- `Section9_Build/arduinobot_ws`

Inside `Section9_Build/arduinobot_ws/src` you will find:
- `arduinobot_description` (URDF/Xacro/meshes)
- `arduinobot_controller` (ros2_control plugin + controller config)
- `arduinobot_bringup` (launch)
- `arduinobot_moveit` (MoveIt config)
- `arduinobot_firmware` (Arduino)
- others (examples, msgs, remote)

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

## Step 2 — Understand + Complete URDF/Xacro
You must edit the robot description:
- `Section9_Build/arduinobot_ws/src/arduinobot_description/urdf/arduinobot.urdf.xacro`
- `.../arduinobot_gazebo.xacro`
- `.../arduinobot_ros2_control.xacro`

### Student Template (fill TODOs)
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

  <!-- TODO: Add all links (base, arms, gripper) and joints -->

</robot>
```

---

## Step 3 — ros2_control (what students must understand)
ros2_control consists of:
- **controller_manager** (loads controllers)
- **controllers** (like JointTrajectoryController)
- **hardware interface plugin** (SystemInterface) for real robot

### In this repo
- Simulation uses `ign_ros2_control` or `gz_ros2_control`
- Real robot uses `arduinobot_controller/ArduinobotInterface`

---

## Step 4 — Hardware Interface Plugin Template (students implement)
Path in repo:
- `Section9_Build/arduinobot_ws/src/arduinobot_controller/include/arduinobot_controller/arduinobot_interface.hpp`
- `Section9_Build/arduinobot_ws/src/arduinobot_controller/src/arduinobot_interface.cpp`

Students must:
- read port from URDF ros2_control params
- export command/state interfaces
- send serial commands to Arduino

### Template Header
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

## Step 5 — Controller YAML (students must understand)
Path in repo:
- `Section9_Build/arduinobot_ws/src/arduinobot_controller/config/arduinobot_controllers.yaml`

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
1) Upload Arduino code from `arduinobot_firmware` using Arduino IDE.

2) Confirm serial port:
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
- robot model in MoveIt
- planning groups
- controllers integration
- RViz MoveIt plugin

Run MoveIt launch from `arduinobot_moveit` (check its launch files).

---

## Step 9 — Alexa Integration
Students must learn:
- ngrok basics
- Alexa skill intents
- mapping intent → ROS command

---

# ✅ Final Checklist
- [ ] ROS 2 + dependencies installed
- [ ] Workspace builds with `colcon build`
- [ ] URDF loads without errors
- [ ] Simulation starts
- [ ] ros2_control controllers load
- [ ] Real robot communicates over serial
- [ ] MoveIt planning works
- [ ] Alexa integration works (optional)

---

# References
- https://docs.ros.org/en/humble/Tutorials.html
- https://docs.ros.org/en/humble/Concepts/Basic/About-Nodes.html
- https://docs.ros.org/en/humble/Concepts/Basic/About-Services.html
- https://docs.ros.org/en/humble/Concepts/Basic/About-Actions.html
