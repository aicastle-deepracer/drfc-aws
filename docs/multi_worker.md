# 다중 Robomaker 작업자 사용

훈련을 가속화하는 한 가지 방법은 여러 Robomaker 작업자를 실행하여 하나의 Sagemaker 인스턴스로 데이터를 공급하는 것입니다.

작업자의 수는 `system.env`의 `DR_WORKERS`를 원하는 작업자 수로 설정하여 구성할 수 있습니다. 결과적으로 에피소드 수(하이퍼파라미터 `num_episodes_between_training`)는 작업자 수에 따라 나누어집니다. 이론적으로 최대 작업자 수는 `num_episodes_between_training`과 같습니다.

훈련은 일반적으로 시작할 수 있습니다.

## 몇 명의 작업자가 필요합니까?

하나의 Robomaker 작업자는 2-4개의 vCPU가 필요합니다. 테스트 결과 `c5.4xlarge` 인스턴스는 성능 저하 없이 3개의 작업자와 Sagemaker를 실행할 수 있습니다. OpenGL 이미지를 사용하면 작업자당 필요한 vCPU 수가 줄어듭니다.

평가가 실행되는 위치와 관련된 문제를 피하려면 `( num_episodes_between_training / DR_WORKERS) * DR_TRAIN_ROUND_ROBIN_ADVANCE_DIST = 1.0`을 보장하십시오.

예: 3명의 작업자가 있을 때 `num_episodes_between_training: 30` 및 `DR_TRAIN_ROUND_ROBIN_ADVANCE_DIST=0.1`로 설정합니다.

참고: Sagemaker는 한 번의 반복에서 10,000단계(3층 CNN)에 도달하면 경험 수집을 중지합니다. 완료된 에피소드당 600-1000단계의 긴 트랙의 경우 이는 반복당 작업자 수와 에피소드 수의 상한을 정의합니다.

## 각 작업자에 대해 다른 매개변수로 훈련하기

작업자 간에 다른 구성(예: 다른 트랙(WORLD_NAME))을 사용하는 것도 가능합니다. 이를 활성화하려면 run.env 내에서 DR_TRAIN_MULTI_CONFIG=True로 설정한 다음, 메인 deepracer-for-cloud 디렉토리에 있는 defaults/template-worker.env의 복사본을 worker-2.env, worker-3.env 등의 형식으로 만드십시오. (따라서 run.env와 함께 worker-2.env, worker-3.env 등이 있어야 합니다. run.env는 여전히 작업자 1에 사용됩니다.) 원하는 변경 사항으로 작업자 환경 파일을 수정하십시오. 이는 world_name뿐만 아니라 더 많은 변경 사항일 수 있습니다. 이러한 추가 작업자 환경 파일은 여러 작업자로 훈련할 때만 사용됩니다.

## 스트림 시청하기

스트림을 시청하고 싶고 `compose` 모드에 있는 경우, `utils/start-local-browser.sh` 스크립트를 사용하여 모든 작업자의 KVS 스트림을 한 번에 스트리밍하는 HTML을 동적으로 생성할 수 있습니다.

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