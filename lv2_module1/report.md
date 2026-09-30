
# 1 — 원격 환경 구성과 모터 응답 확인


### 1-1. 원격 수행 환경 구성

라즈베리파이 4
OS: Ubuntu Server 22.04 ARM64

SSH 접속
```
pa8@pa8-Legion-Pro-5-16IAX10:~$ ssh pa8@pa11.local
pa8@pa11.local's password: 
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-1061-raspi aarch64)
```
Arduino CLI
```
pa8@pa11:~$ arm-none-eabi-g++ --version
arm-none-eabi-g++ (15:10.3-2021.07-4) 10.3.1 20210621 (release)

pa8@pa11:~$ "$BASE/bin/arduino-cli" version
arduino-cli  Version: 1.5.1
```
OpenCR 코어 1.5.3
```
opencr.tar.bz2: OK
```
CLI 설정과 라이브러리
```
Board Name   FQBN
OpenCR Board ROBOTIS:OpenCR:OpenCR
```
Ubuntu 컴파일러
```
compiler.path=/usr/bin/

arm-none-eabi-g++ 10.3.1 20210621 (release)
```
빌드 결과
```
test -s "$BASE/output/opencr_position_p.ino.bin"
file "$BASE/output/opencr_position_p.ino.elf"
arm-none-eabi-size "$BASE/output/opencr_position_p.ino.elf"
sha256sum "$BASE/output/opencr_position_p.ino.bin"
/home/pa8/pa-opencr-build/output/opencr_position_p.ino.elf: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), statically linked, with debug_info, not stripped
   text	   data	    bss	    dec	    hex	filename
 101836	   2948	  92992	 197776	  30490	/home/pa8/pa-opencr-build/output/opencr_position_p.ino.elf
6a3629a8070a5d1f55dce837cc90f073db4444b83da5c722133446608a8151e3  /home/pa8/pa-opencr-build/output/opencr_position_p.ino.bin
```
업로더
```
make: Leaving directory '/home/pa8/pa-opencr-build/uploader-src/arduino/opencr_develop/opencr_ld'
arduino/opencr_develop/opencr_ld/opencr_ld: ELF 64-bit LSB pie executable, ARM aarch64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, BuildID[sha1]=f65e9a7cc0cfd3e2f0705c840ded1561027c291b, for GNU/Linux 3.7.0, not stripped
```
포트 확인
```
lsusb
ls -l /dev/ttyACM*
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 004: ID 10c4:ea60 Silicon Labs CP210x UART Bridge
Bus 001 Device 003: ID 0483:5740 STMicroelectronics Virtual COM Port
Bus 001 Device 002: ID 2109:3431 VIA Labs, Inc. Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
crw-rw---- 1 root dialout 166, 0 Sep 11  2024 /dev/ttyACM0

PORT=/dev/ttyACM0
udevadm info --query=property --name="$PORT"
test -r "$PORT" && test -w "$PORT" && echo 'Port access OK'
DEVPATH=/devices/platform/scb/fd500000.pcie/pci0000:00/0000:00:00.0/0000:01:00.0/usb1/1-1/1-1.3/1-1.3:1.0/tty/ttyACM0
DEVNAME=/dev/ttyACM0
MAJOR=166
MINOR=0
SUBSYSTEM=tty
USEC_INITIALIZED=12567197
ID_BUS=usb
ID_VENDOR_ID=0483
ID_MODEL_ID=5740
ID_PCI_CLASS_FROM_DATABASE=Serial bus controller
ID_PCI_SUBCLASS_FROM_DATABASE=USB controller
ID_PCI_INTERFACE_FROM_DATABASE=XHCI
ID_VENDOR_FROM_DATABASE=STMicroelectronics
ID_MODEL_FROM_DATABASE=Virtual COM Port
ID_VENDOR=ROBOTIS
ID_VENDOR_ENC=ROBOTIS
ID_MODEL=OpenCR_Virtual_ComPort_in_FS_Mode
ID_MODEL_ENC=OpenCR\x20Virtual\x20ComPort\x20in\x20FS\x20Mode
ID_REVISION=0200
ID_SERIAL=ROBOTIS_OpenCR_Virtual_ComPort_in_FS_Mode_FFFFFFFEFFFF
ID_SERIAL_SHORT=FFFFFFFEFFFF
ID_TYPE=generic
ID_USB_INTERFACES=:020201:0a0000:
ID_USB_INTERFACE_NUM=00
ID_USB_DRIVER=cdc_acm
ID_USB_CLASS_FROM_DATABASE=Communications
ID_PATH=platform-fd500000.pcie-pci-0000:01:00.0-usb-0:1.3:1.0
ID_PATH_TAG=platform-fd500000_pcie-pci-0000_01_00_0-usb-0_1_3_1_0
ID_MM_CANDIDATE=1
DEVLINKS=/dev/serial/by-id/usb-ROBOTIS_OpenCR_Virtual_ComPort_in_FS_Mode_FFFFFFFEFFFF-if00 /dev/serial/by-path/platform-fd500000.pcie-pci-0000:01:00.0-usb-0:1.3:1.0
TAGS=:systemd:
CURRENT_TAGS=:systemd:
Port access OK
```


### 1-2. OpenCR 펌웨어 업로드

펌웨어 업로드
[[results/upload.log]]

업로드 이후 확인
```
ls -l /dev/ttyACM*
PORT=/dev/ttyACM0
python3 -m serial.tools.miniterm "$PORT" 115200 --eol LF -e
crw-rw---- 1 root dialout 166, 0 Sep 23 12:43 /dev/ttyACM0
--- Miniterm on /dev/ttyACM0  115200,8,N,1 ---
--- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---
```


### 1-3. 목표 입력과 응답 기록

목표 변경 이후의 측정 기록을 5행 이상 확보

[[results/실행A.log]]


종료 시 정지 여부
```
xSTOP: user
```

### 1-4. 결과 설명

모터 모델: XM430-W350
모터 ID: 12
게인: 5
목표각: 15
속도 상한: 10

목표값은 target_deg:15.000 이고, 제어 출력은 게인 5, 속도 10으로 설정, 기록을 보면 position = 0 deg.에서 점점 증가하여 4초정도 뒤에 position_deg:14.854정도로 수렴하는 것을 확인 할 수 있습니다.



# 문제 2. 오차와 피드백 해석

| 시점  | t_s   | target_deg | position_deg | error_deg |
| --- | ----- | ---------- | ------------ | --------- |
| 초기  | 2.100 | 15.000     | 0.000        | 15.000    |
| 중간  | 3.000 | 15.000     | 8.262        | 6.738     |
| 마지막 | 4.500 | 15.000     | 14.854       | 0.146     |
오차는 15.000° → 6.738° → 0.146°로 시간이 지날수록 감소
초반에는 오차가 커서 선형적으로 줄어들고, 오차가 0.146°에서 더 줄지 않고 P 제어만으로는 정상상태 오차로 보아야 함.

다이나믹셀 내장 엔코더 → (TTL 시리얼) → OpenCR → (USB 시리얼) → 라즈베리파이 (제어 계산 후 역방향으로 명령 전달)

target=+30°, position=+35°인 경우
error = target − position = 30 − 35 = −5°. 목표보다 현재 각도가 더 커서 오차 부호가 음수.
모터를 각도가 감소하는 방향(음의 방향)으로 움직이라는 명령이며, 결과적으로 현재 각도(35°)를 목표(30°) 쪽으로 낮춰 오차를 0으로 줄이는 방향의 보정. 

## 문제 3. P 게인 변경에 따른 응답 비교

| t_s       | A: position_deg | A: error_deg | B: position_deg      | B: error_deg | 목표 초과 여부         |
| --------- | --------------- | ------------ | -------------------- | ------------ | ---------------- |
| 2.100     | 0.000           | 15.000       | 0.088                | 14.912       | A·B 모두 아님        |
| 2.500     | 3.340           | 11.660       | 3.340                | 11.660       | A·B 모두 아님        |
| 2.900     | 7.207           | 7.793        | 7.207                | 7.793        | A·B 모두 아님        |
| 3.100     | 9.229           | 5.771        | 9.229                | 5.771        | A·B 모두 아님        |
| 3.500     | 13.008          | 1.992        | 13.008               | 1.992        | A·B 모두 아님        |
| 3.700     | 14.414          | 0.586        | 14.678               | 0.322        | A·B 모두 아님        |
| 3.900(정착) | 14.854          | 0.146        | 14.941(3.800에 이미 정착) | 0.059        | A·B 모두 아님(한계 안정) |
오차가 큰 초반에는 A와 B의 position_deg가 거의 완전히 일치합니다. 이는 이 구간에서 p_deg_s가 Kp=5든 8이든 모두 속도 상한에 포화. 초반 접근 속도는 Kp가 아니라 속도 상한이 결정하므로, 이 구간의 궤적 차이는 Kp 차이의 근거가 되지 못합니다.
차이는 오차가 줄어들어 포화가 풀리는 후반 구간에서 나타납니다. 포화가 풀리는 임계 오차는 A는 error<2.0°, B는 error<1.25°에서 순수 비례 구간으로 전환됩니다. 이 구간부터 Kp가 큰 B가 같은 오차에 대해 더 큰 속도 명령을 내므로 더 빨리 수렴합니다. 목표 초과는 A·B 어느 쪽에서도 발생하지 않았습니다.

게인의 종류 구분
위치 오차를 속도 명령으로 변환하는 제어 루프가 다이나믹셀 내부가 아니라 OpenCR에서 외부적으로 수행되고 있음을 알 수 있습니다. 다이나믹셀은 속도 제어 모드로 동작하며 위치→속도 변환은 OpenCR 쪽 소프트웨어가 담당합니다. 따라서 이번에 바꾼 Kp는 OpenCR 측 외부 위치 제어 게인이며, 다이나믹셀 내부 게인이 아닙니다.
I항은 오차를 시간에 대해 누적해 P 제어만으로는 남는 정상상태 오차를 점차 제거하는 역할을 하고, D항은 오차의 변화율에 반응해 급격한 접근을 억제함으로써 오버슈트와 진동을 줄이고 응답을 안정화하는 역할을 합니다.

## 문제 4. 제어와 통신의 역할 해석

PC (ROS2 노드) -- 발행 /motor/target(1회성 30.0) --> micro-ROS Agent -- USB 시리얼(micro-ROS) --> OpenCR(제어 루프, 10ms) -- TTL 다이나믹셀 프로토콜(속도/토크 명령) --> Dynamixel 
역방향 측정값 - Dynamixel -- TTL(내장 엔코더 위치) --> OpenCR -- 발행 /motor/state(100ms마다) --> micro-ROS Agent -- USB 시리얼 --> PC (ROS2 노드, 구독)

PC ROS2 노드는 /motor/target의 발행자(publisher)이자 /motor/state의 구독자(subscriber)로, 목표를 보내고 상태를 받는다. micro-ROS Agent는 목표·상태 어느 쪽도 직접 만들거나 소비하지 않고, PC의 DDS(XRCE-DDS)와 OpenCR의 시리얼 프레임을 상호 변환해 중계만 하는 브리지입니다. OpenCR은 /motor/target의 구독자이자 /motor/state의 발행자로, 10ms마다 제어 계산을 수행해 다이나믹셀에 명령을 내리고, 다이나믹셀로부터 읽은 위치를 100ms마다 상태로 발행합니다. 다이나믹셀은 OpenCR 명령을 받아 구동되는 액추에이터이자, 내장 엔코더로 측정한 현재 각도를 OpenCR에 돌려주는 센서 역할을 겸합니다.

제어 계산 주기 10ms, 상태 발행 주기 100ms이므로 100ms ÷ 10ms = 10회. 즉 상태값이 한 번 발행되는 사이에 OpenCR의 제어 계산은 10번 수행됩니다.

통신 단절 시 오래된 명령 처리 정책
OpenCR이 마지막으로 유효한 /motor/target을 받은 시각을 기록해두고, 정상 통신 주기 대비 충분히 긴 타임아웃동안 새 목표나 에이전트 응답이 없으면 그 명령을 더 이상 신뢰하지 않는 것으로 간주합니다. 이 경우 마지막 옛 목표를 계속 추종하지 않고, 현재 측정 위치를 그대로 유지하도록 전환해 정지시키는 것을 기본 정책으로 둡니다.
이유는 두 가지입니다. 첫째, OpenCR은 통신 상태와 무관하게 10ms 루프를 계속 돌리는 유일한 주체이므로 단절 감지와 안전 조치를 여기서 수행하는 것이 지연 없이 가능합니다. 둘째, 링크가 끊긴 동안에는 PC가 목표를 취소하거나 정정할 방법이 없으므로, 오래된 명령을 무기한 계속 따르는 것보다 현재 위치에서 정지하는 쪽이 예기치 못한 동작의 위험을 줄입니다. 통신이 복구되어 새 /motor/target이 수신되면 그 시점부터 정상 제어를 재개합니다.








