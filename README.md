# Ohm Reactive Tutorial

A ROS 2 tutorial package for implementing and evaluating **reactive navigation** behaviors — in particular wall-following — using a 2D LiDAR sensor.

Maintained by Prof. Dr. Christian Pfitzner, [TH Nürnberg](https://www.th-nuernberg.de).

---

## Overview

Reactive navigation refers to the class of robot motion strategies that directly map sensor readings to velocity commands, without maintaining a global map or path plan. This package provides a minimal ROS 2 skeleton so that students can implement and experiment with such behaviors quickly.

The included template (`wall_follower`) subscribes to a laser scan topic, processes the range measurements, and publishes velocity commands to drive a robot along a wall. The core control logic is intentionally left empty as a starting point for implementation.

---

## Package Structure

```
ohm_reactive_tutorial/
├── CMakeLists.txt        # Build configuration (ament_cmake)
├── package.xml           # ROS 2 package manifest
└── src/
    └── wall_follower.cpp # Wall-follower node template
```

---

## Dependencies

| Dependency       | Purpose                                    |
|------------------|--------------------------------------------|
| `rclcpp`         | ROS 2 C++ client library                   |
| `sensor_msgs`    | `sensor_msgs/msg/LaserScan` message type   |
| `geometry_msgs`  | `geometry_msgs/msg/Twist` message type     |
| `ament_cmake`    | Build system                               |

---

## Building

Place this package inside your ROS 2 workspace (e.g. `~/ros2_ws/src/`) and build with `colcon`:

```bash
cd ~/ros2_ws
colcon build --packages-select ohm_reactive_tutorial
source install/setup.bash
```

---

## Running

After building and sourcing the workspace, launch the wall-follower node:

```bash
ros2 run ohm_reactive_tutorial wall_follower
```

### Simulator

A compatible mecanum-wheel robot simulator is available here:  
<https://github.com/autonohm/ohm_mecanum_sim>

Start the simulator first, then run the wall-follower node in a second terminal.

---

## ROS 2 Interface

### Node: `wall_follow_node`

| Direction | Topic              | Message Type                        | Description                          |
|-----------|--------------------|-------------------------------------|--------------------------------------|
| Subscribe | `/robot1/laser`    | `sensor_msgs/msg/LaserScan`         | Incoming 2D laser scan measurements  |
| Publish   | `/robot1/cmd_vel`  | `geometry_msgs/msg/Twist`           | Linear and angular velocity commands |

---

## Implementation Guide

The control logic lives inside the `lidarCallback` method in `src/wall_follower.cpp`. The callback is invoked every time a new laser scan arrives.

Suggested implementation steps:

1. **Extract relevant range values** — e.g. the distance to the wall on one side of the robot from `msg->ranges`.
2. **Define a setpoint** — the desired distance to keep from the wall.
3. **Compute the error** — difference between the measured distance and the setpoint.
4. **Implement a controller** — a PI controller is a good starting point:
   - Proportional term reacts immediately to the current error.
   - Integral term eliminates steady-state offset over time.
5. **Map controller output to velocity** — set `cmd_vel_msg.angular.z` proportional to the controller output while keeping `cmd_vel_msg.linear.x` at a constant forward speed.

### Useful `LaserScan` fields

| Field          | Type               | Description                                      |
|----------------|--------------------|--------------------------------------------------|
| `angle_min`    | `float`            | Start angle of the scan (radians)                |
| `angle_max`    | `float`            | End angle of the scan (radians)                  |
| `angle_increment` | `float`         | Angular step between measurements (radians)      |
| `ranges`       | `vector<float>`    | Distance measurements (metres), one per step     |
| `range_min`    | `float`            | Minimum valid range value                        |
| `range_max`    | `float`            | Maximum valid range value                        |

---

## License

License not yet declared — see `package.xml`.
