# Autonomous Driving and Parking — Team K.A.I.

**2026 9th Kookmin University Autonomous Driving Competition · AI-Edge Challenge**

[한국어](README.ko.md) · [Project portfolio](https://steveandy-sudo.github.io/projects/kookmin-ai-edge/)

This project developed ROS 2 software for an XYCAR competition vehicle, covering camera-based perception, driving decisions, vehicle control, and map-based parking. This repository brings selected driving and parking packages together, with entry points to the implementations and a record of their source versions.

| Period | Role | Team | Platform |
| --- | --- | --- | --- |
| April–August 2026 | Junghun Hwang — Team Lead | 5 members | XYCAR · ROS 2 Humble · Camera · LiDAR |

## Competition results

| Event | Team result |
| --- | --- |
| Preliminary round | 2nd place |
| Final round | 13th place; course completed |
| Parking event | Special Award |

## My contributions

As team lead, I worked across model development, mission logic, and vehicle integration:

- **Imitation-learning pipeline:** developed driving-data collection, model training, TorchScript export, and real-time inference for XYCAR.
- **Object detection:** managed the Roboflow dataset workflow and trained a unified YOLO model for traffic lights, vehicles, obstacles, and cones.
- **Driving decisions and integration:** implemented learning/rule switching and Mission Manager / Final Driver integration, including traffic-light and mission-state handling; participated in team module integration and vehicle parameter tuning.
- **Parking:** defined map-based waypoints and implemented reverse recovery logic.

The selected packages show how these contributions fit into the team's perception, control, driving, and parking systems.

## Driving system

Development covered both imitation learning and rule-based driving. Steering oscillation remained in the learning-based approach, and the team selected **rule-based driving for the competition**.

| Area | Package | Reading entry points |
| --- | --- | --- |
| Data collection and learning | `il_data_tools` | [Recorder](src/il_data_tools/il_data_tools/common_recorder_node.py) · [Training](src/il_data_tools/scripts/train_policy.py) · [TorchScript export](src/il_data_tools/scripts/export_policy_torchscript.py) · [Inference](src/il_data_tools/il_data_tools/policy_inference_node.py) |
| Detection and rule inputs | `my_rule` | [Object detection](src/my_rule/my_rule/object_detection_node.py) · [Drive manager](src/my_rule/my_rule/drive_manager_node.py) |
| Mission and vehicle integration | `xycar_map_nav` | [Integrated driver](src/xycar_map_nav/xycar_map_nav/sequential_hybrid_driver.py) · [Lap policy](src/xycar_map_nav/xycar_map_nav/race_lap_policy.py) · [Drive gate](src/xycar_map_nav/xycar_map_nav/space_drive_gate.py) |

The integrated driving code combines lane following with traffic-light, shortcut, cone, and obstacle-handling logic. The [vehicle startup script](src/xycar_map_nav/scripts/run_complete_space_hybrid.sh) shows how the team modules are connected.

### Learning architecture and evaluation

The canonical-image policy in the source combines a **ResNet18 image encoder and a 1D LiDAR encoder**. Their features feed a regression head that predicts normalized steering. Speed selection, steering limits, smoothing, and sensor-timeout handling are implemented in the surrounding ROS 2 node.

The dataset builder groups recordings by **driving session** before assigning train, validation, and test sets; generated views retain their source session identifier. The offline evaluator reports steering-command MAE and RMSE, together with errors by session and mission label. This makes the intended experiment structure readable alongside the implementation.

See the [learning architecture and evaluation record](docs/EVALUATION.md) for the architecture, code entry points, and the status of the archived experiment artifacts. The [project portfolio](https://steveandy-sudo.github.io/projects/kookmin-ai-edge/) includes competition, parking, practice, and offline object-detection excerpts.

## Parking system

The parking package covers **Start → reverse parking → parallel parking → return to Start**. It includes a provided static map, LiDAR-based localization, waypoint configuration, forward/reverse motion handling, and checks before motor commands are issued. My contribution focused on waypoint definition and reverse recovery within this team system.

| Part | Reading entry points |
| --- | --- |
| Route and mission configuration | [Waypoints](src/xycar_parking_nav/config/parking_waypoints.yaml) · [Mission configuration](src/xycar_parking_nav/config/parking_waypoint_mission.yaml) |
| Mission execution | [Waypoint route manager](src/xycar_parking_nav/xycar_parking_nav/waypoint_route_manager.py) · [Mission manager](src/xycar_parking_nav/xycar_parking_nav/mission_manager.py) |
| Vehicle command handling | [Command adapter](src/xycar_parking_nav/xycar_parking_nav/cmd_vel_adapter.py) |
| Setup and operation | [Package documentation](src/xycar_parking_nav/README.md) · [Waypoint driving launch](src/xycar_parking_nav/launch/parking_waypoint_drive.launch.py) |

Driving and parking retain separate launch paths, with package-specific mission configuration.

## Repository structure

```text
src/
├── il_data_tools/        Data collection, learning, and inference
├── my_rule/              Object detection and rule inputs
├── xycar_map_nav/         Driving integration and mission logic
└── xycar_parking_nav/     Parking mission, waypoints, and command handling
docs/
├── EVALUATION.md          Learning architecture, evaluation method, and evidence
├── SOURCE_MAP.md          Original branches, commits, and collection scope
└── SOURCE_MANIFEST.csv    File-level source records and checksums
```

## Environment and reproducibility

The packages target ROS 2 Humble. Full execution requires the corresponding team workspace, vehicle drivers, ROS message packages, model weights, and hardware configuration. This repository contains selected source packages; model weights and driving datasets are not included.

Package setup is documented for [learning](src/il_data_tools/README.md), [driving integration](src/xycar_map_nav/README.md), and [parking](src/xycar_parking_nav/README.md). The [source record](docs/SOURCE_MAP.md) identifies the imported revisions and their workspace context.

**Evidence status:** competition outcomes describe the team event result; the learning package describes an experimental driving approach. The archived model report and model README refer to different checkpoints, so numerical learning-performance claims await a matching checkpoint, dataset split, and run record.
