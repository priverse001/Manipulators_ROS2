# **Robotics Summer Camp 2026 — Manipulators Track (ROS 2 + Arduino + MoveIt 2 + Alexa)**

Welcome to the **Manipulators Summer Camp**.

This guide is written like camp notes: it includes **concept explanations**, **commands**, **what you must learn**, **mini-checkpoints**, and finally the **full project build steps** for the robot arm in this repository.

> **Golden rule:** Whenever you are stuck, search the official ROS docs first:
> - ROS 2 Humble Tutorials: https://docs.ros.org/en/humble/Tutorials.html

---

# **Phase 1 — ROS 2 Foundations + TurtleBot3 Simulation (Warm‑up Track)**

## **Week 0 — Software Installation (Mandatory)**

### 0.1 Operating System
- Use **Ubuntu 22.04** (native recommended; VM possible but Gazebo can be heavy).

### 0.2 Install ROS 2 Humble
Install ROS 2 Humble using the official Debian packages method.

Verify:
```bash
ros2 --help
ros2 doctor --report
```

### 0.3 Configure ROS environment (important)
Temporary (per terminal):
```bash
source /opt/ros/humble/setup.bash
```

Permanent (recommended):
```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### 0.4 Install required dependencies for this camp
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

### 0.5 Install build tools + serial libraries
```bash
sudo apt-get update && sudo apt-get install -y \
  build-essential \
  cmake \
  git \
  python3-pip \
  libserial-dev

pip install pyserial
```

### 0.6 Install tools
- VS Code (recommended)
- Arduino IDE (mandatory for real robot)

✅ **Checkpoint:** `ros2 doctor --report` works.

---

## **Week 1 — ROS 2 Basics (Big Picture)**

### What is ROS 2?
ROS 2 is a framework for building robot software as **multiple small programs (nodes)** that communicate via:
- Topics (streaming data)
- Services (request/response)
- Actions (long-running tasks)
- Parameters (runtime configuration)

### What you must learn this week
- Workspaces + colcon
- Packages (C++ + Python)
- Nodes
- Launch files
- Topics, services, actions
- Simulation basics (RViz + Gazebo)
- URDF/Xacro basics

✅ **Checkpoint:** You can explain the difference between *topic vs service vs action*.

---

## **Week 2 — Linux Basics (Quick, but essential)**

You must be comfortable with:
- Navigation: `cd`, `ls`, `pwd`
- File ops: `cp`, `mv`, `rm`, `mkdir`, `touch`
- Searching: `grep`, `find`
- Permissions: `chmod`, `chown`
- Process checks: `ps`, `top`, `htop`
- Serial devices: `/dev/ttyACM0`, `/dev/ttyUSB0`

✅ **Checkpoint:**
```bash
ls -l
chmod +x script.sh
grep -R "some_text" .
```

---

## **Week 3 — ROS 2 Workspace + colcon**

### What is a workspace?
A ROS 2 workspace is a folder that contains ROS packages in `src/`.

colcon generates:
- `build/` → build files
- `install/` → installed artifacts
- `log/` → logs

### Install colcon
```bash
sudo apt install python3-colcon-common-extensions
```

### Create and build a workspace
```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
colcon build --symlink-install
source install/setup.bash
```

✅ **Checkpoint:**
```bash
echo $AMENT_PREFIX_PATH
```

---

## **Week 4 — ROS 2 Packages (C++ and Python)**

### Create a package
```bash
ros2 pkg create <pkg-name> --dependencies <deps>
```

C++:
```bash
ros2 pkg create my_cpp_pkg --build-type ament_cmake --dependencies rclcpp
```

Python:
```bash
ros2 pkg create my_py_pkg --build-type ament_python --dependencies rclpy
```

### What students must understand
- `package.xml` declares dependencies
- C++ uses `CMakeLists.txt`
- Python uses `setup.py`, `setup.cfg`
- launch/config/urdf must be installed to `share/<pkg>`

✅ **Checkpoint:**
```bash
ros2 pkg list | head
ros2 pkg prefix rclcpp
```

---

## **Week 5 — ROS 2 Nodes**

### What is a node?
A node is a program that does one job (sensor reading, control, planning, etc.).

### Commands you MUST know
```bash
ros2 run <package> <executable>
ros2 node list
ros2 node info /node_name
```

### Practice (turtlesim)
Install if needed:
```bash
sudo apt-get install ros-humble-turtlesim
```

Run:
```bash
ros2 run turtlesim turtlesim_node
```

✅ **Checkpoint:**
```bash
ros2 node list
```

---

## **Week 6 — ROS 2 Launch**

### Why launch?
Launch files start multiple nodes with one command.

Run:
```bash
ros2 launch <package> <launch_file.py>
```

Students must learn:
- LaunchDescription
- Node actions
- parameters
- remappings
- namespaces

✅ **Checkpoint:** You can run a launch file from an installed package.

---

## **Week 7 — Topics / Services / Actions**

### Topics
Concept:
- Publishers send messages
- Subscribers receive continuously

Commands:
```bash
ros2 topic list
ros2 topic info /topic
ros2 topic echo /topic
ros2 topic pub /topic <type> "{yaml: value}"
```

### Services
Commands:
```bash
ros2 service list
ros2 service list -t
ros2 service type /service
ros2 interface show <srv_type>
ros2 service call /service <srv_type> "{yaml: args}"
```

### Actions
Commands:
```bash
ros2 action list
ros2 action list -t
ros2 action info /action
ros2 interface show <action_type>
ros2 action send_goal /action <action_type> "{yaml: goal}" --feedback
```

✅ **Checkpoint:** Using turtlesim, demonstrate:
- one topic echo
- one service call
- one action goal

---

## **Week 8 — Simulation & Visualization Overview**

### RViz
Use RViz for:
- robot model visualization
- TF frames
- MoveIt planning scene

### Gazebo
Gazebo is used for physics simulation.

Gazebo Classic:
```bash
ros2 launch gazebo_ros gazebo.launch.py
```

New Gazebo (gz):
```bash
ros2 launch ros_gz_sim gz_sim.launch.py
```

### RQT
```bash
ros2 run rqt_gui rqt
ros2 run rqt_graph rqt_graph
ros2 run rqt_plot rqt_plot
ros2 run rqt_console rqt_console
```

✅ **Checkpoint:** Use `rqt_graph` to visualize nodes and topics.

---

## **Week 9 — URDF & Xacro**

### URDF
URDF describes:
- links
- joints
- collisions
- inertials

### Xacro
Xacro adds:
- macros
- properties
- includes

✅ **Checkpoint:** You can run xacro expansion on a simple robot.

---

# **TurtleBot3 Module (Phase 1 Final Lab)**

This lab is intentionally placed **right before the manipulator project**. It gives confidence in:
- cloning multiple repos
- rosdep
- colcon
- Gazebo simulation
- teleoperation

## What is a TurtleBot3?
TurtleBot3 is a small, affordable, programmable, ROS-based mobile robot.

### Key Features
- Modular hardware: Burger / Waffle / Waffle Pi
- ROS 2 packages support SLAM, navigation, simulation
- Easy to run in Gazebo

---

## Install TurtleBot3 Simulation (ROS 2 Humble)

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

### Choose a model
```bash
export TURTLEBOT3_MODEL=waffle
```

### Run simulations
Empty world:
```bash
ros2 launch turtlebot3_gazebo empty_world.launch.py
```

TurtleBot3 world:
```bash
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py
```

House world:
```bash
ros2 launch turtlebot3_gazebo turtlebot3_house.launch.py
```

### Teleop
In a new terminal:
```bash
export TURTLEBOT3_MODEL=burger
ros2 run turtlebot3_teleop teleop_keyboard
```

Controls:
- `w/x`: increase/decrease linear velocity
- `a/d`: increase/decrease angular velocity
- `s`: stop
- `Ctrl+C`: exit

✅ **Checkpoint:** Drive the robot around the map using teleop.

---

## TurtleBot3 Gazebo Fortress Simulation (Optional Advanced)

### Install dependencies
```bash
sudo apt update
sudo apt install ros-humble-desktop ros-humble-gazebo-ros-pkgs
```

### Install bridge packages
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

# **Phase 2 — Main Project: Build the Robot Arm in This Repo**

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
- [ ] TurtleBot3 sim runs (Phase 1)
- [ ] URDF loads without errors
- [ ] Simulation starts for robot arm
- [ ] Real robot communicates over serial
- [ ] MoveIt planning works
- [ ] Alexa integration works (optional)

---

# References (Official)
- ROS 2 Tutorials: https://docs.ros.org/en/humble/Tutorials.html
- Nodes concept: https://docs.ros.org/en/humble/Concepts/Basic/About-Nodes.html
- Topics concept: https://docs.ros.org/en/humble/Concepts/Basic/About-Topics.html
- Services concept: https://docs.ros.org/en/humble/Concepts/Basic/About-Services.html
- Actions concept: https://docs.ros.org/en/humble/Concepts/Basic/About-Actions.html
- Launch system: https://docs.ros.org/en/humble/Tutorials/Intermediate/Launch/Launch-Main.html
- URDF tutorials: https://docs.ros.org/en/humble/Tutorials/Intermediate/URDF/URDF-Main.html
- ros2_control: https://control.ros.org/
