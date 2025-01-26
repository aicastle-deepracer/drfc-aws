# Head to Head 레이스 실행 (베타)

AWS의 가상 서킷에서 진행되는 토너먼트 방식의 경주와 유사하게, Head to Head 경주를 진행할 수 있습니다. 이는 "토너먼트 모드"를 대체합니다.

## 소개

이 개념은 두 모델이 서로 경주하는 것으로, 하나는 보라색 자동차, 다른 하나는 주황색 자동차입니다. 한 자동차는 기본 설정된 모델에 의해 구동되며, 두 번째 자동차는 `DR_EVAL_OPP_S3_MODEL_PREFIX`에 있는 모델에 의해 구동됩니다.

## 설정

### run.env

`run.env` 파일을 다음과 같은 매개변수로 설정하세요:
* `DR_RACE_TYPE`는 `HEAD_TO_MODEL`이어야 합니다.
* `DR_EVAL_OPP_S3_MODEL_PREFIX`는 보조 모델의 S3 접두어입니다.
* `DR_EVAL_OPP_CAR_NAME`은 이 모델의 표시 이름입니다.

메트릭, 추적 및 비디오는 각 모델의 접두어에 저장됩니다.

## 실행

`dr-start-evaluation`을 사용하여 경주를 실행하세요. 한 번의 경주가 진행됩니다.

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