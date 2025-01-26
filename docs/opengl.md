# Robomaker를 위한 GPU 가속 OpenGL

Robomaker의 성능을 향상시키는 한 가지 방법은 GPU 가속 OpenGL을 활성화하는 것입니다. OpenGL은 GPU RAM이 충분하지 않거나 Tensorflow를 지원하기에는 너무 오래된 경우에도 Gazebo의 성능을 크게 향상시킬 수 있습니다.

## 데스크탑

Unity를 실행하는 Ubuntu 데스크탑에서는 추가적인 단계가 거의 필요하지 않습니다.

* 최신 Nvidia 드라이버가 설치되어 실행 중인지 확인하세요.
* nvidia-docker가 설치되어 있는지 확인하세요. 스크립트를 직접 실행하지 않으려면 `bin/prepare.sh`를 검토하세요.
* `system.env`에서 다음 설정을 사용하여 DRfC를 구성하세요:
    * `DR_HOST_X=True`; 도커 컨테이너 내에서 시작하는 대신 로컬 X 서버를 사용합니다.
    * `DR_DISPLAY`; 실행 중인 X 서버의 값으로 설정합니다. 설정되지 않은 경우 `DISPLAY`가 사용됩니다.

`dr-start-training`/`dr-start-evaluation`을 실행하기 전에 `DR_DISPLAY`/`DISPLAY`와 `XAUTHORITY`가 정의되어 있는지 확인하세요.

`nvidia-smi`에서 `gzserver`를 찾아 OpenGL이 작동하는지 확인하세요.

`DR_GUI_ENABLE=True`인 경우 Gazebo UI, rviz 및 rqt가 별도의 창에서 열립니다. (여러 작업자가 있는 경우 혼잡할 수 있습니다...)

### 데스크탑에 원격 연결

SSH를 통해 훈련이나 평가를 시작하려면 (예: 이동 중에 훈련을 증가시키기 위해) 몇 가지 단계를 수행해야 합니다:
* 실제로 로컬 머신에 로그인되어 있는지 확인하세요 (데스크탑 세션이 실행 중임).
* SSH 터미널에서:
    * `DR_DISPLAY`가 `system.env`에 구성되어 있는지 확인하세요. 그렇지 않으면 `export DISPLAY=:1`을 실행하세요. [*]
    * `export XAUTHORITY=/run/user/$(id -u)/gdm/Xauthority`를 실행하여 X가 X 매직 쿠키의 위치를 알 수 있도록 합니다.
    * 일반적으로 `source bin/activate.sh`를 실행하세요.
    * `dr-start-training` 또는 `dr-start-evaluation` 명령을 실행하세요.

*참고*: `DISPLAY`를 설정하면 특정 명령어 (예: `dr-logs-sagemaker`)가 SSH 터미널이 아닌 데스크탑의 터미널 창에서 시작됩니다. 이를 피하기 위해 `DR_DISPLAY`를 사용하는 것이 좋습니다.

## 헤드리스 서버

GPU가 있는 헤드리스 서버, 예를 들어 EC2 인스턴스나 디스플레이가 없는 GPU가 있는 로컬 컴퓨터 (예: Tesla K40, K80, M40)에도 적용됩니다.

이 경우에도 로그인되어 있지 않은 데스크탑 컴퓨터에 적용됩니다. 충돌을 피하기 위해 모니터 케이블을 분리하세요.

* Nvidia 드라이버와 nvidia-docker가 설치되어 있는지 확인하세요. 스크립트를 직접 실행하지 않으려면 `bin/prepare.sh`를 검토하세요.
* 호스트에 X 서버를 설정하세요. `utils/setup-xorg.sh`는 기본 설치 스크립트입니다.
* `system.env`에서 다음 설정을 사용하여 DRfC를 구성하세요:
    * `DR_HOST_X=True`; 도커 컨테이너 내에서 시작하는 대신 로컬 X 서버를 사용합니다.
    * `DR_DISPLAY`; 헤드리스 X 서버가 시작될 X 디스플레이입니다. (기본값은 `:99`이며, 다른 X 서버와 충돌할 수 있으므로 `:0` 또는 `:1` 사용을 피하세요.)

`utils/start-xorg.sh`로 X 서버를 시작하세요.

`DR_GUI_ENABLE=True`인 경우 VNC 서버가 포트 5900에서 시작되어 Gazebo UI와 상호작용할 수 있습니다.

`nvidia-smi`에서 `gzserver`를 찾아 OpenGL이 작동하는지 확인하세요.

## Windows 11의 WSL2

OpenGL은 Windows 11의 WSL2에서도 지원됩니다. 기본적으로 Ubuntu 22.04에서는 Xwayland 서버가 시작됩니다.

OpenGL 가속을 활성화하려면 다음 단계를 수행하세요:
* `sudo apt install x11-xserver-utils`로 x11-server-utils를 설치하세요.
* `system.env`에서 다음 설정을 사용하여 DRfC를 구성하세요:
    * `DR_HOST_X=True`; 도커 컨테이너 내에서 시작하는 대신 로컬 X 서버를 사용합니다.
    * `DR_DISPLAY=:0`; Xwayland는 기본적으로 :0에서 시작됩니다.

Gazebo UI와 상호작용하려면 `system.env`에서 `DR_DOCKER_STYLE=compose`와 `DR_GUI_ENABLE=True`를 설정하세요.

## 문서

* [인트로](index.md)
* [초기 설치](installation.md)
* [AWS 콘솔에 모델 업로드](upload.md)
* [환경 변수 및 명령어](reference.md)
* [다중 Robomaker 작업자 사용](multi_worker.md)
* [다중 병렬 실행](multi_run.md)
* [Robomaker를 위한 GPU 가속 OpenGL](opengl.md)
* [하나의 컴퓨터에서 여러 GPU 사용](multi_gpu.md)
* [Windows에 설치하기](windows.md)
* [Head to Head 레이스 실행](head-to-head.md)
* [자동차 관찰하기](video.md)
* [Docker 설정에 대하여](docker.md)
* [실시간 지표](metrics.md)