# 모듈 3. 설정값에 따른 Mask와 검출 결과 변화

## 실행 환경

- **환경:** Ubuntu 24.04.5, ROS 2 Lyrical, Python 3.12.3, OpenCV 4.6.0
- **입력:** rosbag ~/bags/p5/p5_success, ~/bags/p5/p5_loss_return
  - 형식 컬러 640×360 `rgb8` + 정렬된 뎁스 `32FC1` + camera_info
- **실행 노드:** tracker_node, rviz_node, rosbag2_player, 모터 제어 노드는 실행하지 않음.

## 빌드

```bash
cd ~/git/Xmen && source /opt/ros/lyrical/setup.bash && colcon build --symlink-install --packages-select xmen_tracker
```

## 설정값 변경·적용 방법

- 설정 파일: 기본 검출 설정은 YAML 파일(xmen_tracker/config/detector.yaml)이고, tracker_node의 detector_config 파라미터로 경로를 지정한다.
- 설정값 변경: lv2_module3/results/configs의 config.yaml로 변경
- 적용 시점: 이 파라미터는 읽기 전용이라 노드가 시작할 때 한 번만 읽는다(tracker_node.py). 그래서 설정마다 노드를 새로 시작했다.

## 실행 명령

```bash
cd ~/git/physicalai-lv2-hongjulee/lv2_module3

export ROS_DOMAIN_ID=73 FASTDDS_BUILTIN_TRANSPORTS=LARGE_DATA
source /opt/ros/lyrical/setup.bash && source ~/git/Xmen/install/setup.bash
```
```bash
ros2 run xmen_tracker realsense_node
```
```bash
ros2 run xmen_tracker tracker_node --ros-args -p detector_config:=$HOME/git/physicalai-lv2-hongjulee/lv2_module3/results/configs/exp_A_S200.yaml
```
```bash
ros2 run xmen_tracker rviz_node --ros-args -p preview_transport:=raw
```
```bash
rviz2
```


### 이미지 토픽 뷰어

- 같은 실행 중 `rviz2`를 띄우고 Image 표시를 추가한다. 토픽은 /camera/color/image_raw, /tracking/mask, /tracking/image 중 하나를 고르고, Reliability를 Reliable로 둔다.
- 도메인: 실행 스크립트와 같은 터미널 환경(ROS_DOMAIN_ID=73, FASTDDS_BUILTIN_TRANSPORTS=LARGE_DATA)에서 띄워야 보인다.

## 결과 파일 위치

| 위치                                                                                                         | 내용                                                                   |
| ---------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| [results/topic_info/topics/](results/topic_info/topics/)                                                   | `ros2 topic list -t`, `ros2 topic info -v`, `ros2 node list/info` 결과 |
| results/(exp_A_1, exp_A_2, exp_B_1, exp_B_2)                                                               | config_applied.yaml, 전체 프레임 검출 기록, 노드 로그                             |
| [results/exp1_compare.png](results/exp1_compare.png), [results/exp2_compare.png](results/exp2_compare.png) | 실험별 A·B 비교 그림                                                        |

