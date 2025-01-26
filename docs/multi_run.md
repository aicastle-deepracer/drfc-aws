# 다중 병렬 실행

한 컴퓨터에서 여러 실험을 병렬로 실행할 수 있습니다. 이는 `swarm` 모드와 `compose` 모드 모두에서 가능하며, `run.env`의 `DR_RUN_ID`로 제어됩니다.

이 기능은 컨테이너 이름에 고유한 접두사를 생성하여 작동합니다:
* Swarm 모드에서는 스택 이름을 정의하여 수행됩니다 (기본값: deepracer-0).
* Compose 모드에서는 프로젝트 이름을 추가하여 수행됩니다.

## 기능 사용 권장 방법

기본적으로 `run.env`는 DRfC가 활성화될 때 로드되지만, `source bin/activate.sh <파일명>`을 통해 별도의 구성을 로드할 수 있습니다.

이 기능을 사용하는 가장 좋은 방법은 실험마다 하나의 bash 셸을 사용하고, 각 셸에 별도의 구성을 로드하는 것입니다.

활성화 후에는 `dr-*` 명령어를 사용하여 각 실험을 독립적으로 제어할 수 있습니다.

로컬 또는 Azure를 사용하는 경우 S3 / Minio 인스턴스는 공유되며, 한 번만 실행됩니다.

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