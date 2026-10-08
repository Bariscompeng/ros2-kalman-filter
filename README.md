<div align="center">

# 📈 ROS 2 Kalman Filter

**A linear Kalman filter node for position / velocity state estimation in ROS 2, with replayable sensor data and visual evaluation.**

![ROS 2](https://img.shields.io/badge/ROS_2-22314E?style=for-the-badge&logo=ros&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-013243?style=for-the-badge&logo=numpy&logoColor=white)
![Matplotlib](https://img.shields.io/badge/Matplotlib-11557c?style=for-the-badge)

<img src="my_kalman_pkg/g%C3%B6rseller/kalman_animation.gif" width="600" alt="Kalman filter estimate animation"/>

</div>

---

## ✨ Overview

Noisy position and velocity measurements are published as ROS 2 topics; the filter node fuses them with a **constant-velocity motion model** and publishes the estimated state. Results are logged to CSV and compared against the raw measurements with plots and an animation.

```mermaid
flowchart LR
    CSV[(measurements.csv)] --> PUB[csv_publisher]
    PUB -->|/sensor/position| KF[kalman_node]
    PUB -->|/sensor/velocity| KF
    KF -->|/state/position_est| OUT[estimates.csv]
    KF -->|/state/velocity_est| OUT
    OUT --> VIS[plot_csv_comparison · animate_kalman]
```

## 🧮 Model

State **x = [position, velocity]ᵀ**, sampling period **Δt = 0.1 s**

| Matrix | Value | Meaning |
|---|---|---|
| **A** | `[[1, 0.1], [0, 1]]` | constant-velocity transition |
| **H** | `I₂` | both states measured directly |
| **Q** | `diag(1e-4, 1e-4)` | process noise |
| **R** | `diag(1e-2, 1e-2)` | measurement noise |
| **P₀** | `diag(1, 1)` | initial covariance |

All parameters live in [`filter_params.py`](my_kalman_pkg/my_kalman_pkg/filter_params.py) for easy tuning.

## 📊 Results

| Correct tuning | Mis-tuned filter |
|---|---|
| <img src="my_kalman_pkg/g%C3%B6rseller/dogru_karsilastirma.png" width="380"/> | <img src="my_kalman_pkg/g%C3%B6rseller/hatal%C4%B1_karsilastirma.png" width="380"/> |

A recorded **rosbag2** of a full run is included for reproducible playback.

## 🚀 Getting Started

```bash
cd ~/ros2_ws/src && git clone https://github.com/Bariscompeng/ros2-kalman-filter.git
cd ~/ros2_ws && colcon build --packages-select my_kalman_pkg
source install/setup.bash

ros2 run my_kalman_pkg kalman_node     # terminal 1
ros2 run my_kalman_pkg csv_publisher   # terminal 2
```

## 📁 Structure

```
my_kalman_pkg/
├── my_kalman_pkg/
│   ├── kalman_node.py          # predict / update loop
│   ├── filter_params.py        # A, H, Q, R, P0
│   ├── csv_publisher.py        # replays measurements as topics
│   ├── plot_csv_comparison.py  # measurement vs estimate plots
│   └── animate_kalman.py       # GIF animation
├── data/measurements.csv
└── rosbag2_…/                  # recorded run
```

---

<div align="center">
Built by <a href="https://github.com/Bariscompeng">Barış Coşkun</a>
</div>
