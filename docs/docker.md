# Docker 설정에 대하여

DRfC는 Docker를 `swarm`과 `compose` 두 가지 모드로 실행할 수 있으며, 이 동작은 `system.env` 파일의 `DR_DOCKER_STYLE`을 통해 설정됩니다.

## Swarm 모드

Docker Swarm 모드는 기본 모드입니다. Docker Swarm은 여러 호스트를 연결하여 부하를 분산할 수 있게 해줍니다. 이는 특히 여러 Robomaker 작업자를 실행하려는 경우 유용하며, 두 대의 컴퓨터가 각각 DeepRacer를 실행하기에 충분한 성능이 아닐 때 로컬에서도 유용할 수 있습니다.

Swarm 모드에서 DRfC는 `docker stack`을 사용하여 스택을 생성합니다. 운영 중에는 `docker stack ls`를 통해 실행 중인 스택을 확인할 수 있으며, `docker stack <id> ls`를 통해 실행 중인 서비스를 확인할 수 있습니다.

DRfC는 관리자에만 설치됩니다. (처음 설치된 호스트) Swarm 작업자는 '단순'하며 DRfC를 설치할 필요가 없습니다.

### 주요 기능

* 사용자가 동일한 네트워크에 여러 컴퓨터를 연결할 수 있습니다. (AWS에서는 인스턴스가 동일한 VPC에 연결되어야 하며, 인스턴스 간 통신이 허용되어야 합니다.)
* [여러 Robomaker 작업자 지원](multi_worker.md)
* [여러 병렬 실험 실행 지원](multi_run.md)

### 제한 사항

* Sagemaker 컨테이너는 관리자에서만 실행할 수 있습니다.
* Docker 이미지는 Docker Hub에서 다운로드됩니다. 로컬에서 빌드된 이미지는 Docker Hub에 없는 고유한 태그가 있는 경우에만 허용됩니다. 여러 Docker 노드가 있는 경우 모든 노드에 이미지가 사용 가능해야 합니다.

### 작업자 연결

* 관리자에서 `docker swarm join-token manager`를 실행합니다.
* 작업자에서 관리자에 표시된 명령어 `docker swarm join --token <token> <ip>:<port>`를 실행합니다.

### 포트

Docker Swarm은 서비스의 모든 복제본 앞에 자동으로 로드 밸런서를 배치합니다. 이는 훈련 중인 DeepRacer의 비디오 스트림을 제공하는 ROS Web View가 로드 밸런싱되어 하나의 포트(`8080`)를 공유함을 의미합니다. 여러 작업자(여러 호스트에 걸쳐 있는 경우에도)가 있는 경우 F5를 눌러 순환할 수 있습니다.

## Compose 모드

Compose 모드에서 DRfC는 `docker compose`를 사용하여 서비스를 생성합니다. 운영 중에는 `docker service ls`를 통해 실행 중인 스택을 확인할 수 있으며, `docker service ps`를 통해 실행 중인 서비스를 확인할 수 있습니다.

### 주요 기능

* [여러 Robomaker 작업자 지원](multi_worker.md)
* [여러 병렬 실험 실행 지원](multi_run.md)
* [Robomaker를 위한 GPU 가속 OpenGL 지원](opengl.md)

### 제한 사항

* 작업 부하는 여러 호스트에 걸쳐 분산될 수 없습니다.

### 포트

Docker Compose를 사용하는 경우, 각기 다른 Robomaker 작업자는 ROS Web View와 VNC를 위한 고유한 포트를 필요로 합니다. Docker는 이를 동적으로 할당합니다. `docker ps`를 사용하여 어떤 컨테이너에 어떤 포트가 할당되었는지 확인할 수 있습니다.

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