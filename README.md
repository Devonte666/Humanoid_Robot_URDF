# Humanoid Robotics — 双足人形机器人 URDF 模型

[![ROS](https://img.shields.io/badge/ROS-Noetic%20%7C%20Humble-blue)](https://ros.org/)
[![SolidWorks](https://img.shields.io/badge/Exported%20from-SolidWorks-red)](http://wiki.ros.org/sw_urdf_exporter)

这是一个**完整、物理准确**的双足人形机器人 URDF 模型，由 SolidWorks 模型导出并手动校准了动力学参数。该模型适用于 ROS/ROS2 环境下的运动学仿真、步态规划与控制算法开发。

---

### 仿真效果

<img src="https://github.com/user-attachments/assets/7926e808-2c8e-4229-89f4-2e234a10a9fb" width="350"/>

---

### 实物机器人

<img src="https://github.com/user-attachments/assets/4ffe2c51-a76b-4772-94db-2e0b9f142472" width="350"/>
---

## 📌 主要特性

### ✅ 精确的动力学参数

所有连杆的质量、质心位置（COM）和惯性张量（Inertia）均从 SolidWorks 的 CAD 模型中直接导出，保证物理仿真的真实性。例如：

- `base_link` 质量：**47.989 kg**
- 整腿质量分布精确至每个关节连杆（如左大腿 6.942 kg，右小腿 4.264 kg 等）

---

### ✅ 完整的人形下肢结构

包含左右腿各 6 个自由度（DoF）：

- 髋关节：yaw / roll / pitch
- 膝关节：pitch
- 踝关节：roll / pitch

---

### ✅ ROS 生态兼容

- 标准 URDF 格式，可直接用于：
  - `robot_state_publisher`
  - `joint_state_publisher`
  - `rviz`
- 可与 Gazebo、MoveIt 等工具无缝集成

---

### ✅ 可视化与碰撞网格

使用高质量 STL 文件提供精确的可视化和碰撞检测几何体。

---

# 📁 项目文件结构

```text
humanoid_robotics_12_18/
├── config/                     # Rviz 等工具的配置文件
├── launch/                     # ROS 启动文件（例如 display.launch, gazebo.launch）
├── meshes/                     # STL 网格文件（用于可视化与碰撞）
│   ├── base_link.STL
│   ├── Left_leg_yaw_link.STL
│   ├── Left_leg_roll_link.STL
│   └── ...（其余 14 个连杆的 STL）
├── textures/                   # 纹理文件（可选）
├── urdf/                       # 机器人描述文件
│   ├── humanoid_robotics_12_18.urdf   # 核心模型
│   ├── CMakeLists.txt
│   ├── export.log
│   └── package.xml
├── .gitignore
└── README.md
```

> 注意：所有 `.STL` 文件的路径已在 URDF 中设置为：
>
> ```text
> package://humanoid_robotics_12_18/meshes/...
> ```
>
> 因此将该功能包放入 ROS 工作空间后可直接使用。

---

# 🚀 快速开始

## 1️⃣ 前置条件

- Ubuntu 20.04 / 22.04
- ROS Noetic 或 ROS2 Humble

安装必要 ROS 包：

```bash
sudo apt install ros-<distro>-robot-state-publisher \
                 ros-<distro>-joint-state-publisher-gui
```

---

## 2️⃣ 将功能包放入工作空间

```bash
cd ~/catkin_ws/src     # 或 ~/ros2_ws/src
git clone https://github.com/你的用户名/humanoid_robotics_12_18.git
```

---

## 3️⃣ 编译（可选）

虽然 URDF 本身无需编译，但建议编译以完成 ROS 包索引。

```bash
cd ~/catkin_ws
catkin_make
source devel/setup.bash
```

---

## 4️⃣ 在 RViz 中查看模型

如果 `launch/display.launch` 不存在，可以手动创建：

```xml
<!-- launch/display.launch -->

<launch>
  <param
    name="robot_description"
    textfile="$(find humanoid_robotics_12_18)/urdf/humanoid_robotics_12_18.urdf" />

  <node
    name="robot_state_publisher"
    pkg="robot_state_publisher"
    type="robot_state_publisher" />

  <node
    name="joint_state_publisher_gui"
    pkg="joint_state_publisher_gui"
    type="joint_state_publisher_gui" />

  <node
    name="rviz"
    pkg="rviz"
    type="rviz"
    args="-d $(find humanoid_robotics_12_18)/config/urdf.rviz" />
</launch>
```

运行：

```bash
roslaunch humanoid_robotics_12_18 display.launch
```

你应该会看到完整的机器人模型，并且可以通过滑块控制每个关节的角度（关节限制已在 URDF 中定义）。

---

## 5️⃣ 在 Gazebo 中仿真（可选）

需要配置控制器。

一个简单示例：

```bash
roslaunch humanoid_robotics_12_18 gazebo.launch
```

> 提示：如果你尚未编写 Gazebo 启动文件，可以参考 ROS 官方 Gazebo 教程。

---

# ⚙️ 关节参数一览

| 关节名称 | 类型 | 极限 (rad) | 说明 |
|---|---|---|---|
| Left_leg_yaw_joint | revolute | [-0.1745, 0.6981] | 左髋偏航 |
| Left_leg_roll_joint | revolute | [-0.5236, 0.5236] | 左髋滚转 |
| Left_leg_pitch_joint | revolute | [-1.6581, 0.2618] | 左髋俯仰 |
| Left_knee_pitch_joint | revolute | [0.0873, 2.1817] | 左膝俯仰 |
| Left_ankle_roll_joint | revolute | [-0.6981, 0.4363] | 左踝滚转 |
| Left_ankle_pitch_joint | revolute | [-0.5236, 0.5236] | 左踝俯仰 |
| Right_leg_yaw_joint | revolute | [-0.6981, 0.1745] | 右髋偏航 |
| ...（其余关节对称） | ... | ... | ... |

完整关节列表请参考 URDF 文件。

---

# 📊 动力学参数示例（部分连杆）

| 连杆名 | 质量 (kg) | 质心位置 (m) | 主惯性矩 (ixx, iyy, izz) |
|---|---|---|---|
| base_link | 47.989 | [-0.006, 0.000, 0.254] | 1.680, 1.506, 0.749 |
| Left_leg_pitch_link | 6.942 | [-0.008, 0.004, -0.182] | 0.105, 0.104, 0.020 |
| Right_knee_pitch_link | 4.264 | [0.041, 0.000, -0.144] | 0.034, 0.036, 0.017 |
| Left_ankle_pitch_link | 1.474 | [0.040, 0.000, -0.076] | 0.003, 0.007, 0.008 |

所有惯性张量均基于质心坐标系，由 SolidWorks 的“由重心决定”选项提供。

---

# 🛠️ 后续开发方向

你可以基于该模型继续开发：

- 双足步态规划（Walking Gait Planning）
- MPC / ZMP 控制
- 强化学习步态训练
- MoveIt 运动规划
- Gazebo 动力学仿真
- Isaac Gym / Mujoco 强化学习环境
- ROS2 Control 控制器开发

---

# 📄 License

本项目采用 MIT License 开源。

```text
MIT License
```

---

# 🙌 致谢

- SolidWorks URDF Exporter
- ROS / ROS2 社区
- Gazebo 仿真平台
- RViz 可视化工具

---

# ⭐ Star History

如果这个项目对你有帮助，欢迎点一个 ⭐ Star 支持项目！
