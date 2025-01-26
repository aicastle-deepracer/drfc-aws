# Windows에 설치하기

## 사전 준비

Windows에서 NVIDIA GPU / CUDA를 지원하는 Ubuntu 하위 시스템을 설치하는 기본 단계는 [Cuda on WSL 사용자 가이드](https://docs.nvidia.com/cuda/wsl-user-guide/index.html)에서 찾을 수 있습니다. WSL과 호환되는 [NVIDIA CUDA 지원 드라이버](https://developer.nvidia.com/cuda/wsl/download)가 업데이트되어 있는지 확인하세요.

이후의 지침은 기본 Ubuntu 배포판을 사용하는 기본적인 WSL이 작동하고 있다고 가정합니다.

## 추가 단계

일반적인 `bin/prepare.sh` 스크립트는 Ubuntu WSL 설치에서는 작동하지 않으므로 대체 단계가 필요합니다.

### 필요한 패키지 추가

다음 명령어로 추가 패키지를 설치하세요:

```
sudo apt-get install jq awscli python3-boto3 docker-compose
```

### Docker 및 NVIDIA-Docker 설치 및 구성

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update && sudo apt-get install -y --no-install-recommends docker-ce docker-ce-cli containerd.io

distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

cat /etc/docker/daemon.json | jq 'del(."default-runtime") + {"default-runtime": "nvidia"}' | sudo tee /etc/docker/daemon.json
sudo usermod -a -G docker $(id -un)
```

### DRfC 설치

이제 `bin/init.sh -a gpu -c local`을 실행하여 DRfC를 설정하고 일반적인 DRfC 시작 지침을 따를 수 있습니다.

## 알려진 문제

* `init.sh`는 Nvidia 드라이버와 WSL2 Linux 커널의 차이로 인해 GPU를 감지할 수 없습니다. `system.env`에서 GPU 이미지를 수동으로 설정해야 합니다.
* Ubuntu를 시작할 때 Docker가 자동으로 시작되지 않습니다. `sudo service docker start`로 수동으로 시작하세요.

  Windows 작업 스케줄러를 사용하여 서비스를 자동으로 시작하도록 구성할 수도 있습니다.

  *1)* /etc/init-wsl에 새 파일을 만듭니다 (sudo vi /etc/init-wsl) 다음 내용을 포함합니다.

  ```
  #!/bin/sh
  service start docker
  ```

  *2)* 스크립트를 실행 가능하게 만듭니다 `sudo chmod +x /etc/init-wsl`

  *3)* Windows 10에서 작업 스케줄러를 엽니다.

  - 왼쪽에서 **작업 스케줄러 라이브러리** 옵션을 클릭하고 오른쪽에서 **작업 만들기**를 클릭합니다.

  - **일반** 탭에서 이름 **WSL 시작**을 입력하고 **사용자가 로그인했는지 여부에 관계없이 실행** 및 **가장 높은 권한으로 실행** 옵션을 선택합니다.

  - **트리거** 탭에서 새로 만들기를 클릭하고 작업 시작: **시작 시** > 확인

  - **작업** 탭에서 새로 만들기를 클릭하고 작업: **프로그램 시작**

    프로그램/스크립트: **wsl**

    인수 추가: **-u root /etc/init-wsl**

  - 확인을 클릭하여 종료합니다.

  *4)* 수동으로 작업을 실행하여 확인하거나 Windows를 재부팅한 후 Docker가 자동으로 시작되어야 합니다.

* 비디오 스트림이 localhost 주소를 사용하여 로드되지 않을 수 있습니다. Windows 브라우저에서 HTML 비디오 스트림에 액세스하려면 WSL VM의 IP 주소를 사용해야 할 수 있습니다. WSL 터미널에서 'ip addr' 명령어를 사용하여 IP 주소를 확인하고 **eth0** 아래의 **inet**을 찾습니다 (예: ip = 172.29.38.21). 그런 다음 Windows 브라우저(edge, chrome 등)에서 **ip:8080**으로 이동합니다 (예: 172.29.38.21:8080).


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