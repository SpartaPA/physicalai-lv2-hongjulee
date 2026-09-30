
## SSH, OpenCR 펌웨어, 다이나믹셀의 목표값 입력과 응답

### 장비·환경
* 라즈베리파이 4
* OS: Ubuntu Server 22.04 ARM64
* Arduino CLI
* OpenCR 보드 패키지: 1.5.3 / FQBN
* Dynamixel: XM430-W350 모델 1020, ID 12, Protocol 2.0
* SSH 원격 접속

### 실행 방법

업로드
```
UPLOADER="$BASE/uploader-src/arduino/opencr_develop/opencr_ld/opencr_ld"
set -o pipefail
"$UPLOADER" "$PORT" 115200 \
  "$BASE/output/opencr_position_p.ino.bin" 1 \
  2>&1 | tee "$BASE/upload.log"
```

실행확인
```
python3 -m serial.tools.miniterm "$PORT" 115200 --eol LF -e
crw-rw---- 1 root dialout 166, 0 Sep 23 12:43 /dev/ttyACM0
--- Miniterm on /dev/ttyACM0  115200,8,N,1 ---
--- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---
```



