# Kookmin Autonomous Driving Competition — Code Guide

This repository organizes selected **Team K.A.I.** ROS 2 source packages from the [original competition repository](https://github.com/steveandy-sudo/kookmin_autonomous_competition_teamKAI). It is a compact reading copy for a research portfolio. The original repository remains the record of team development and the source for a complete vehicle workspace.

**2026 9th Kookmin University Autonomous Driving Competition:** the five-member team's results were preliminary 2nd place, finals 13th place with course completion, and a Special Award in the parking event. These are **team results**. This repository does not attribute every included line to one person.

## Start here

| Area | Package | What to inspect |
| --- | --- | --- |
| Data collection and learning | [`il_data_tools`](src/il_data_tools/README.md) | [Recorder](src/il_data_tools/il_data_tools/common_recorder_node.py), [training](src/il_data_tools/scripts/train_policy.py), [inference](src/il_data_tools/il_data_tools/policy_inference_node.py) |
| Object detection and rule input | [`my_rule`](src/my_rule/) | [Detection node](src/my_rule/my_rule/object_detection_node.py), [drive manager](src/my_rule/my_rule/drive_manager_node.py) |
| Final driving integration | [`xycar_map_nav`](src/xycar_map_nav/README.md) | [Hybrid driver](src/xycar_map_nav/xycar_map_nav/sequential_hybrid_driver.py), [mission supervisor](src/xycar_map_nav/xycar_map_nav/mission_supervisor.py), [race-lap policy](src/xycar_map_nav/xycar_map_nav/race_lap_policy.py) |
| Parking event | [`xycar_parking_nav`](src/xycar_parking_nav/README.md) | [Mission manager](src/xycar_parking_nav/xycar_parking_nav/mission_manager.py), [waypoint route](src/xycar_parking_nav/xycar_parking_nav/waypoint_route_manager.py), [motor safety adapter](src/xycar_parking_nav/xycar_parking_nav/cmd_vel_adapter.py) |

The parking package came from the original repository's **`빠킹` branch**; the other three came from **`main`**. [Source map](docs/SOURCE_MAP.md) records the precise commits and what was excluded. A [file manifest](docs/SOURCE_MANIFEST.csv) pairs every copied file with its source path and SHA-256 digest.

## Contribution boundary

Junghun Hwang's documented work includes the imitation-learning workflow, the Roboflow and unified YOLO model workflow, learning/rule switching, and Mission Manager / Final Driver integration and vehicle tuning. Parking waypoint definition and reverse recovery are also documented contributions. Team packages are included to show the integration context. Lane perception, the full avoidance stack, all cone algorithms, all controllers, and the complete parking package are **not** presented as individual implementations.

The exact authorship of each file has not been verified. Git commit authors and package names alone are not sufficient evidence of who wrote each part. See the [portfolio case study](https://steveandy-sudo.github.io/projects/kookmin-ai-edge/) for the narrative and contribution limits.

## Scope and running the code

This is a **source reference**, not a standalone vehicle build. ROS 2 Humble, Nav2, Xycar hardware packages, message packages, camera/LiDAR drivers, and other packages in the [team workspace](https://github.com/steveandy-sudo/kookmin_autonomous_competition_teamKAI) are needed for full execution. The original [driving README](https://github.com/steveandy-sudo/kookmin_autonomous_competition_teamKAI/blob/main/README.md) and [parking README](src/xycar_parking_nav/README.md) describe their respective environments.

Model weights (`.pt`, `.onnx`, `.pth`), recorded data, and other large outputs are intentionally excluded. The source code has not been modified. Commands that require the omitted weights or hardware will not run from this repository alone. No real-car run is claimed for this extracted copy.

## 한국어 안내

본선 주행 코드는 원본 `main`, 주차 코드는 `빠킹` 브랜치에서 가져왔습니다. 네 패키지를 분야별로 바로 찾을 수 있게 모았으며, 원본 출처와 파일별 검증값을 기록했습니다. 대회 성적은 팀 성과이고, 포함된 모든 코드를 개인 구현으로 주장하지 않습니다.
