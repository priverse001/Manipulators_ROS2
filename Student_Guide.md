# **Robotics Summer Camp — Manipulators (ROS 2) Student Guide**

> **Repo:** `priverse001/Manipulators_ROS2`

Hope you all have completed the ROS installation. If not, view **Week 0 – Software Installation** first. We aim to deliver a beginner‑level understanding of ROS 2 and guide you step‑by‑step to build the complete **Arduinobot** project (simulation + real robot).

**NOTE:** Always prefer the **official ROS 2 documentation** for any confusion. It’s the most reliable source:
- https://docs.ros.org/en/humble/Tutorials.html

---

# **Week 1 — Introduction to ROS 2**

## **Introduction**
In this project (and most robotics projects), you will use **ROS 2** and **Python/C++**. Spend some time learning the basics first.

If you are not convinced about ROS 2, watch:
- Bloomberg — *Building a Robot Operating System for the Future* (YouTube)

ROS 2 is the successor to ROS 1 and provides:
- Real‑time capabilities
- Better security
- Multi‑platform support (Linux, Windows, macOS)

---

## **Learning Resources (Must Read)**
- **Linux Resources (optional)**
- **ROS 2 Workspace**
- **ROS 2 Package**
- **ROS 2 Nodes**
- **ROS 2 Launch**
- **ROS 2 Topics**
- **ROS 2 Services**
- **ROS 2 Actions**
- **Robotics Simulation Overview**

> Always refer to **Google** or **ROS Wiki** if anything is unclear.

---

# **Week 2 — Linux Basics (Quick)**
- Linux is an open‑source UNIX‑style operating system.
- In this camp we use **Ubuntu 22.04 (Jammy Jellyfish)**.

### More Detailed Resources (Do Refer!)
- Linux File System Directories
- Linux File Permissions
- Linux File Commands (Important)
- Shell Scripting

---

# **Week 3 — ROS 2 Workspace & Colcon**

## **Prerequisites**
### Configure Environment
You must source ROS 2 before working:
```bash
source /opt/ros/humble/setup.bash
```

### Install colcon
```bash
sudo apt install python3-colcon-common-extensions
```

## **Workspace Basics**
A ROS 2 workspace contains a `src` folder and is built using `colcon`.

### Create a Workspace
```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
```

### Build
```bash
colcon build --symlink-install
```

### Source the Workspace
```bash
source install/setup.bash
```

---

# **Week 4 — ROS 2 Package Basics**
Create your own package:
```bash
ros2 pkg create <pkg-name> --dependencies [deps]
```

### C++ Package
```bash
ros2 pkg create <pkg-name> --dependencies [deps] --build-type ament_cmake
```

### Python Package
```bash
ros2 pkg create <pkg-name> --dependencies [deps] --build-type ament_python
```

---

# **Week 5 — ROS 2 Nodes**
Nodes are individual processes in the ROS graph.

### Useful Commands
```bash
ros2 run <package> <executable>
ros2 node list
ros2 node info /node_name
```

---

# **Week 6 — ROS 2 Launch**
Launch files let you run multiple nodes at once.

### Run a Launch File
```bash
ros2 launch <package> <launch_file.py>
```

---

# **Week 7 — ROS 2 Topics, Services, Actions**
### Topics
- Continuous streams of data.

### Services
- Request/response.

### Actions
- Long‑running goals with feedback.

---

# **Week 8 — Simulation & Visualization**
## RViz
- Visualize robot model, sensors, TF, planning.

## Gazebo (Classic / Ignition)
- Simulate physics.
- Use **ros_gz_bridge** for topic bridging in Ignition.

---

# **Week 9 — URDF & Xacro**
URDF describes robot structure. Xacro helps reduce repetition.

---

# **PROJECT: ARDUINOBOT — Full Build Guide**

## ✅ Goal
Build the full ROS 2 robot arm (simulation + real hardware + MoveIt 2 + Alexa).

---

## **Step 1 — Install Project Dependencies**
```bash
sudo apt-get update && sudo apt-get install -y \
  ros-humble-joint-state-publisher-gui \
  ros-humble-xacro \
  ros-humble-ros2-control \
  ros-humble-ros2-controllers \
  ros-humble-moveit* \
  ros-humble-ros-gz-* \
  ros-humble-*-ros2-control \
  libserial-dev \
  python3-pip

pip install pyserial
```

---

## **Step 2 — Clone Workspace**
```bash
git clone https://github.com/priverse001/Manipulators_ROS2.git
cd Manipulators_ROS2/Section9_Build/arduinobot_ws
```

---

## **Step 3 — URDF/Xacro Template (Students Fill)**
Create/complete:
- `arduinobot.urdf.xacro`
- `arduinobot_gazebo.xacro`
- `arduinobot_ros2_control.xacro`

### **Template: `arduinobot.urdf.xacro`**
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

  <!-- TODO: Add all remaining links and joints -->

</robot>
```

---

## **Step 4 — ros2_control Plugin Template**
Students must implement the **hardware interface** that communicates with Arduino.

### `arduinobot_interface.hpp`
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

---

## **Step 5 — Controllers YAML Template**
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

## **Step 6 — Build & Run (Simulation)**
```bash
cd Section9_Build/arduinobot_ws
colcon build
source install/setup.bash
ros2 launch arduinobot_bringup simulated_robot.launch.py
```

---

## **Step 7 — Build & Run (Real Robot)**
1. Upload Arduino firmware (`arduinobot_firmware`).
2. Connect USB (`/dev/ttyACM0`).
3. Launch:
```bash
ros2 launch arduinobot_bringup real_robot.launch.py
```

---

## **Step 8 — MoveIt 2**
- Generate MoveIt config
- Load robot description and controllers
- Plan in RViz

---

## **Step 9 — Alexa Integration (Optional)**
- Setup ngrok
- Create Alexa skill
- Map voice intents to robot commands

---

# ✅ Student Checklist
- [ ] ROS 2 installed and sourced
- [ ] Workspace builds
- [ ] URDF loads without errors
- [ ] Gazebo spawns robot
- [ ] ros2_control plugin loads
- [ ] MoveIt plans motion
- [ ] Arduino executes commands
- [ ] Alexa voice commands work

---

# References
- https://docs.ros.org/en/humble/Tutorials.html
- https://docs.ros.org/en/humble/Concepts/Basic/About-Nodes.html
- https://docs.ros.org/en/humble/Concepts/Basic/About-Services.html
- https://docs.ros.org/en/humble/Concepts/Basic/About-Actions.html
