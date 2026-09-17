# 자율주행 및 주차 시스템 — Team K.A.I.

**2026년 제9회 국민대학교 자율주행 경진대회 · AI-엣지 챌린지**

[English](README.md) · [프로젝트 포트폴리오](https://steveandy-sudo.github.io/projects/kookmin-ai-edge/) · [원본 팀 저장소](https://github.com/steveandy-sudo/kookmin_autonomous_competition_teamKAI)

XYCAR 경진대회 차량을 대상으로 카메라 인지, 주행 판단, 차량 제어, 지도 기반 주차를 개발한 ROS 2 프로젝트입니다. 본 저장소에는 주행 및 주차 관련 패키지를 모으고, 주요 구현과 원본 코드의 출처를 정리했습니다.

| 기간 | 역할 | 팀 구성 | 개발 플랫폼 |
| --- | --- | --- | --- |
| 2026년 4월–8월 | 황정헌 · 팀장 | 5인 | XYCAR · ROS 2 Humble · Camera · LiDAR |

## 대회 결과

| 부문 | 팀 성과 |
| --- | --- |
| 예선 | 2위 |
| 본선 | 코스 완주 · 13위 |
| 주차경기 | 특별상 |

## 본인 기여

팀장으로서 모델 개발, 미션 로직, 차량 통합 과정에 참여했습니다.

- **모방학습 파이프라인:** 주행 데이터 수집, 모델 학습, TorchScript 변환, XYCAR 실시간 추론으로 이어지는 과정을 구현했습니다.
- **객체 검출:** Roboflow 데이터셋 작업을 수행하고 신호등·차량·장애물·콘을 검출하는 통합 YOLO 모델을 학습했습니다.
- **주행 판단 및 통합:** 학습·규칙 기반 주행 전환과 Mission Manager / Final Driver 통합을 구현했습니다. 신호등·미션 상태 처리, 팀 모듈 연동, 실차 파라미터 튜닝에 참여했습니다.
- **주차:** 지도 기반 웨이포인트를 정의하고 후진 복구 로직을 구현했습니다.

수록된 패키지에는 팀원들의 기여도 포함됩니다. 위 목록은 공동 시스템 내 본인 역할이며, 별도 차선 모델, 전체 회피·콘 주행 알고리즘, 모든 제어기, 주차 시스템 전체를 개인 구현으로 주장하지 않습니다.

## 본선 주행 시스템

개발 과정에서 모방학습과 규칙 기반 주행을 다뤘습니다. 학습 기반 주행에서는 조향 진동이 남아 있었고, **대회에는 규칙 기반 주행을 선택했습니다.** 학습 패키지는 개발 과정을 보여주는 코드이며, 본선에서 해당 모델로 주행했다는 의미는 아닙니다.

| 영역 | 패키지 | 주요 코드 |
| --- | --- | --- |
| 데이터 수집·학습·추론 | `il_data_tools` | [데이터 수집](src/il_data_tools/il_data_tools/common_recorder_node.py) · [학습](src/il_data_tools/scripts/train_policy.py) · [TorchScript 변환](src/il_data_tools/scripts/export_policy_torchscript.py) · [추론](src/il_data_tools/il_data_tools/policy_inference_node.py) |
| 객체 검출·규칙 판단 입력 | `my_rule` | [객체 검출](src/my_rule/my_rule/object_detection_node.py) · [주행 관리자](src/my_rule/my_rule/drive_manager_node.py) |
| 미션 판단·차량 명령 통합 | `xycar_map_nav` | [통합 주행](src/xycar_map_nav/xycar_map_nav/sequential_hybrid_driver.py) · [랩별 정책](src/xycar_map_nav/xycar_map_nav/race_lap_policy.py) · [주행 명령 제어](src/xycar_map_nav/xycar_map_nav/space_drive_gate.py) |

통합 주행 코드는 차선 추종과 신호등·지름길·콘·장애물 대응 로직을 연결합니다. [실차 시작 스크립트](src/xycar_map_nav/scripts/run_complete_space_hybrid.sh)에서 팀 모듈을 실행하는 구성을 확인할 수 있습니다.

## 주차 시스템

주차 패키지는 **출발 → 후진주차 → 평행주차 → 출발지 복귀** 미션을 다룹니다. 제공된 정적 지도, LiDAR 기반 위치추정, 웨이포인트 설정, 전진·후진 처리, 모터 명령 전 검사를 포함합니다. 이 중 본인 기여는 웨이포인트 정의와 후진 복구 로직입니다.

| 구성 | 주요 코드 |
| --- | --- |
| 웨이포인트·미션 설정 | [웨이포인트](src/xycar_parking_nav/config/parking_waypoints.yaml) · [미션 설정](src/xycar_parking_nav/config/parking_waypoint_mission.yaml) |
| 미션 실행 | [웨이포인트 관리자](src/xycar_parking_nav/xycar_parking_nav/waypoint_route_manager.py) · [미션 관리자](src/xycar_parking_nav/xycar_parking_nav/mission_manager.py) |
| 차량 명령 처리 | [명령 어댑터](src/xycar_parking_nav/xycar_parking_nav/cmd_vel_adapter.py) |
| 설치·운용 | [패키지 문서](src/xycar_parking_nav/README.md) · [웨이포인트 주행 실행 구성](src/xycar_parking_nav/launch/parking_waypoint_drive.launch.py) |

**본선 주행과 주차 코드를 하나의 저장소에 모았으며, 실행 구성은 각각 유지했습니다.** 본선 주행에서 주차로 자동 전환하는 기능을 이 저장소에서 새로 구현하거나 검증한 것은 아닙니다.

## 저장소 구성

```text
src/
├── il_data_tools/        데이터 수집·학습·추론
├── my_rule/              객체 검출·규칙 판단 입력
├── xycar_map_nav/         주행 통합·미션 로직
└── xycar_parking_nav/     주차 미션·웨이포인트·차량 명령 처리
docs/
├── SOURCE_MAP.md          원본 브랜치·커밋·수집 범위
└── SOURCE_MANIFEST.csv    파일별 원본 경로·검증값
```

## 실행 환경과 검증 범위

ROS 2 Humble을 대상으로 작성된 패키지입니다. 전체 실행에는 해당 팀 작업공간의 차량 드라이버, ROS 메시지 패키지, 모델 파일, 하드웨어 설정 등이 필요합니다. 이 저장소에는 일부 소스 패키지를 수록했으며 모델 가중치와 주행 데이터셋은 포함하지 않았습니다.

주행 환경은 [가져온 원본 버전의 설치 안내](https://github.com/steveandy-sudo/kookmin_autonomous_competition_teamKAI/blob/0ae216c6255e25404560948f3e6d5479a7a7ba8f/README.md), 주차 환경은 [주차 패키지 문서](src/xycar_parking_nav/README.md)를 참고할 수 있습니다. 정리한 사본은 원본과의 일치 여부 및 파이썬 문법을 확인했으며, 독립적인 ROS 2 빌드와 차량 실행은 검증하지 않았습니다.

주행 패키지는 원본 `main`, 주차 패키지는 `빠킹` 브랜치에서 가져왔습니다. 정확한 커밋과 출처는 [원본 기록](docs/SOURCE_MAP.md)에 정리했습니다.
