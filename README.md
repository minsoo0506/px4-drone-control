# px4-drone-control

PX4 SITL과 ROS 2를 기반으로 단일 드론의 목표 지점 이동과 비행 상태 기록을 구현하는 시뮬레이션 프로젝트.

## 기술 스택

| 구분 | 기술 | 역할 |
| --- | --- | --- |
| 개발 환경 | Ubuntu 24.04 LTS | 시뮬레이션과 ROS 2 실행 환경 |
| 언어 | C++ | 임무 상태 관리 및 기체 제어 노드 |
| 미들웨어 | ROS 2 Jazzy | PX4 메시지 송수신 및 노드 실행 |
| 비행 소프트웨어 | PX4 SITL | 비행 상태 추정과 위치 제어 |
| 시뮬레이션 | Gazebo | 가상 기체와 비행 환경 |
| 지상 관제 | QGroundControl | 기체 연결과 비행 상태 확인 |
| 연동 | `px4_msgs`, Micro XRCE-DDS Agent | PX4와 ROS 2 간 메시지 연결 |

위 조합은 목표 환경이다. PX4 릴리스와 `px4_msgs`, 통신 구성 요소의 호환 버전은 환경 구성 후 확정한다.

## 구현 범위

- 가상 드론 한 대의 연결, 비행 모드, 지역 위치 및 고도 확인
- ROS 2 노드를 통한 Offboard 모드 전환과 위치 목표 전송
- 이륙, 목표 지점 이동, 출발점 복귀 및 착륙 상태 관리
- 실제 위치와 목표 위치를 비교한 도착 판정
- 비행 단계, 위치, 성공 및 실패 사유 기록
- 위치 정보 무효, 시간 초과, 명령 거절 등 기본 실패 상황 감지

구현 대상은 단일 기체와 장애물이 없는 시뮬레이션 환경이다. 실제 드론 비행, 충돌 회피와 다중 기체 제어는 현재 범위에 포함하지 않는다.

## 동작 과정

1. PX4 SITL과 Gazebo를 실행하고 QGroundControl에서 기체 연결을 확인한다.
2. 임무 노드가 기체 상태와 유효한 지역 위치를 수신하고 출발점을 저장한다.
3. Offboard 제어 신호를 송신하고 모드 전환과 Arm 상태를 확인한다.
4. 출발점 기준 5m 고도로 상승한 뒤 북쪽 5m의 목표 지점으로 이동한다.
5. 목표 도착을 확인하고 출발점 상공으로 복귀한다.
6. 착륙을 요청하고 실제 착륙 상태를 확인한 뒤 결과를 기록한다.

위치는 PX4의 local NED 좌표계를 기준으로 처리한다. 좌표와 도착 판정, 오류 처리는 구현 과정에서 검증한다.

## 시스템 구성

```text
Gazebo ↔ PX4 SITL ↔ Micro XRCE-DDS Agent ↔ ROS 2 임무 노드
            ↕                                  ↓
     QGroundControl                       위치·상태 로그
```

## 참고 자료

- [PX4 시뮬레이션](https://docs.px4.io/main/en/simulation/)
- [PX4 ROS 2 사용자 가이드](https://docs.px4.io/main/en/ros2/user_guide)
- [PX4 ROS 2 Offboard 예제](https://docs.px4.io/main/en/ros2/offboard_control)
