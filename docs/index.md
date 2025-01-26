# 소개

AWS 또는 Azure에서 DeepRacer 훈련 환경을 빠르고 쉽게 설정할 수 있는 방법을 제공합니다. Azure [N-Series Virtual Machines](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-gpu) 또는 [AWS EC2 Accelerated Computing 인스턴스](https://aws.amazon.com/ec2/instance-types/?nc1=h_ls#Accelerated_Computing), 혹은 로컬 데스크탑이나 서버에서 실행할 수 있습니다.

DeepRacer-For-Cloud (DRfC)는 Alex(https://github.com/alexschultz/deepracer-for-dummies)의 작업을 확장하여 시작되었으며, 이는 Chris(https://github.com/crr0004/deepracer)가 만든 뛰어난 작업을 감싼 래퍼입니다. 2세대 Deepracer Console 도입과 함께 이 저장소는 분리되었습니다. 이 저장소에는 훈련을 실행하는 데 필요한 스크립트가 포함되어 있으며, Docker Hub에서 제공하는 사전 빌드된 도커 이미지를 사용합니다. 모든 내부 빌드 기능은 [Deepracer Build](https://github.com/aws-deepracer-community/deepracer) 저장소로 이동되었습니다.

## 주요 기능

DRfC는 최고의 모델을 만들 수 있도록 다양한 기능을 제공합니다:

* **사용자 친화적**
  - 지속적으로 업데이트되는 커뮤니티 [Robomaker](https://github.com/aws-deepracer-community/deepracer-simapp) 및 [Sagemaker](https://github.com/aws-deepracer-community/deepracer-sagemaker-container) 컨테이너를 기반으로 하여 광범위한 CPU 및 GPU 환경을 지원.
  - 다양한 스크립트(`dr-*`)를 통해 훈련을 손쉽게 실행 가능.
  - AWS DeepRacer Console 모델을 감지하고, 로컬에서 훈련한 모델을 해당 콘솔로 업로드 가능.
  
* **모드**
  - 타임 트라이얼(Time Trial)
  - 객체 회피(Object Avoidance)
  - 헤드 투 봇(Head-to-Bot)

* **훈련**
  - Sagemaker당 여러 Robomaker 인스턴스(N:1)를 활용하여 훈련 진행 속도 개선.
  - 하드웨어가 지원하는 경우 여러 훈련 세션을 병렬로 실행 가능(각 세션은 N:1 구조).
  - 여러 노드를 연결(Swarm 모드)하여 여러 컴퓨터/인스턴스의 자원을 결합 가능.

* **평가**
  - 훈련과 독립적으로 평가 실행.
  - S3에 평가 실행을 MP4 파일로 저장 가능.

* **로깅**
  - 훈련 메트릭 및 추적 파일을 S3에 저장.
  - AWS CloudWatch 통합 선택 가능.
  - Robomaker 내부 로그 파일 노출 선택 가능.

* **기술**
  - 여러 노드를 연결하는 데 사용되는 Docker Swarm과 OpenGL을 지원하는 Docker Compose를 모두 지원.

## 지원

* 일반적인 지원은 [AWS DeepRacing Community](https://deepracing.io/)에 가입하는 것을 권장합니다. 커뮤니티 Slack에는 #dr-training-local 채널이 있어 적극적인 지원을 받을 수 있습니다.
* 코드 문제를 발견하거나 문서 업데이트가 필요한 경우 GitHub 이슈를 생성하세요.

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