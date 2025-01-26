# 환경 변수 및 명령어

## 환경 변수

스크립트는 `system.env` 파일에 상수 구성 값이 포함되어 있고, `run.env` 파일에 실행에 필요한 특정 값이 포함되어 있어야 한다고 가정합니다. 어떤 값이 어떤 파일에 들어가는지는 크게 중요하지 않습니다.

| 변수 | 설명 |
|------|------|
| `DR_RUN_ID` | 여러 개의 독립적인 학습 작업이 있고 단일 DRfC 인스턴스만 있는 경우 사용됩니다. 이는 고급 구성이며 일반적으로 기본값 `0`으로 두는 것이 좋습니다.|
| `DR_WORLD_NAME` | 사용할 트랙을 정의합니다.|
| `DR_RACE_TYPE` | 유효한 옵션은 `TIME_TRIAL`, `OBJECT_AVOIDANCE`, `HEAD_TO_BOT`입니다.|
| `DR_CAR_COLOR` | 유효한 옵션은 `Black`, `Grey`, `Blue`, `Red`, `Orange`, `White`, `Purple`입니다.|
| `DR_CAR_NAME` | 자동차의 표시 이름; 업로드 시 Deepracer 콘솔에 표시됩니다.|
| `DR_ENABLE_DOMAIN_RANDOMIZATION` | `True`로 설정하면 각 에피소드마다 다른 환경 색상과 조명을 순환합니다. 이는 일반적으로 모델을 시뮬레이터에 밀접하게 맞추는 대신 더 견고하고 일반화되도록 만드는 데 사용됩니다.|
| `DR_UPLOAD_S3_PREFIX` | 대상 위치의 접두사입니다. (일반적으로 `DeepRacer-SageMaker-RoboMaker-comm-`로 시작합니다.)|
| `DR_EVAL_NUMBER_OF_TRIALS` | 평가 시뮬레이션을 위해 완료할 랩 수입니다.|
| `DR_EVAL_IS_CONTINUOUS` | `False`로 설정하면 자동차가 트랙을 벗어나거나 충돌할 경우 평가 시도가 종료됩니다. `True`로 설정하면 해당 매개변수에 구성된 페널티 시간을 받지만 평가 시도를 계속합니다.|
| `DR_EVAL_OFF_TRACK_PENALTY` | 평가 중 트랙을 벗어났을 때 추가되는 페널티 시간(초)입니다. `DR_EVAL_IS_CONTINUOUS`가 `True`로 설정된 경우에만 적용됩니다.|
| `DR_EVAL_COLLISION_PENALTY` | 평가 중 충돌 시 추가되는 페널티 시간(초)입니다. `DR_EVAL_IS_CONTINUOUS`가 `True`로 설정된 경우에만 적용됩니다.|
| `DR_EVAL_SAVE_MP4` | 평가 실행의 MP4를 저장하려면 `True`로 설정합니다.|
| `DR_EVAL_REVERSE_DIRECTION` | 자동차가 트랙을 거꾸로 주행하도록 하려면 `True`로 설정합니다.|
| `DR_TRAIN_CHANGE_START_POSITION` | 훈련 세션 동안 레이서가 시작 위치를 라운드 로빈 방식으로 변경할지 여부를 결정합니다. (초기 훈련 시 `True`로 설정하는 것이 좋습니다.)|
| `DR_TRAIN_ALTERNATE_DRIVING_DIRECTION` | `True` 또는 `False`. `True`로 설정하면 자동차가 각 에피소드마다 시계 방향과 반시계 방향으로 번갈아 주행합니다.|
| `DR_TRAIN_START_POSITION_OFFSET` | 첫 번째 에피소드에서 훈련을 시작할 위치를 제어하는 데 사용됩니다.|
| `DR_TRAIN_ROUND_ROBIN_ADVANCE_DISTANCE` | 라운드 로빈 방식으로 각 에피소드에서 얼마나 진행할지를 설정합니다. 0.05는 트랙의 5%입니다. 일반적으로 트랙 주위에 고르게 분포되도록 총 에피소드 수와 일치하는 짝수로 유지하는 것이 좋습니다. 예를 들어, 반복당 20개의 에피소드가 있다면, 0.05, 0.10, 0.20이 좋습니다.|
| `DR_TRAIN_MULTI_CONFIG` | `True` 또는 `False`. 여러 작업자 훈련 실행에서 각 작업자에 대해 다른 run.env 구성을 사용하려는 경우 사용됩니다. 설정 방법에 대한 자세한 내용은 다중 구성 문서를 참조하십시오.|
| `DR_TRAIN_MIN_EVAL_TRIALS` | 각 훈련 반복 사이에 실행되는 최소 평가 시도 수입니다. 정책 훈련이 진행되는 동안 평가가 계속되며 이 숫자보다 많을 수 있습니다. 이는 최소값을 설정하며, 특히 GPU SageMaker 컨테이너를 사용할 때 훈련 속도를 높이는 데 유용합니다.|
| `DR_TRAIN_REVERSE_DIRECTION` | 자동차가 트랙을 거꾸로 주행하도록 하려면 `True`로 설정합니다.|
| `DR_TRAIN_BEST_MODEL_METRIC` | "최고" 모델로 유지할 모델을 제어하는 데 사용할 수 있습니다. 평가 완료 비율이 가장 높은 모델을 선택하려면 `progress`로 설정하고, 평가 보상이 가장 높은 모델을 선택하려면 `reward`로 설정합니다.|
| `DR_TRAIN_MAX_STEPS_PER_ITERATION` | 학습에 사용할 반복당 최대 단계 수를 제어하는 데 사용할 수 있습니다. 초과 단계는 메모리 부족 상황을 피하기 위해 버려지며 기본값은 10000입니다.|
| `DR_LOCAL_S3_PRETRAINED` | 이전 세션에서 생성된 모델을 기반으로 훈련 또는 평가를 수행할지 여부를 결정합니다. 이는 `s3://{DR_LOCAL_S3_BUCKET}/{LOCAL_S3_PRETRAINED_PREFIX}`에 저장되며, `{DR_LOCAL_S3_PROFILE}`에 보관된 자격 증명으로 접근할 수 있습니다.|
| `DR_LOCAL_S3_PRETRAINED_PREFIX` | S3 버킷 내 사전 훈련된 모델의 접두사입니다.|
| `DR_LOCAL_S3_MODEL_PREFIX` | S3 버킷 내 모델의 접두사입니다.|
| `DR_LOCAL_S3_BUCKET` | 세션 동안 사용될 S3 버킷의 이름입니다.|
| `DR_LOCAL_S3_CUSTOM_FILES_PREFIX` | S3 버킷 내 구성 파일의 접두사입니다.|
| `DR_LOCAL_S3_TRAINING_PARAMS_FILE` | 훈련 중 구성에 대해 robomaker 컨테이너로 전송되는 매개변수를 보유한 YAML 파일의 이름입니다. 파일 이름은 `s3://{DR_LOCAL_S3_BUCKET}/{LOCAL_S3_PRETRAINED_PREFIX}`에 상대적입니다.|
| `DR_LOCAL_S3_EVAL_PARAMS_FILE` | 평가 중 구성에 대해 robomaker 컨테이너로 전송되는 매개변수를 보유한 YAML 파일의 이름입니다. 파일 이름은 `s3://{DR_LOCAL_S3_BUCKET}/{LOCAL_S3_PRETRAINED_PREFIX}`에 상대적입니다.|
| `DR_LOCAL_S3_MODEL_METADATA_KEY` | `model_metadata.json` 파일이 저장된 위치입니다.|
| `DR_LOCAL_S3_HYPERPARAMETERS_KEY` | `hyperparameters.json` 파일이 저장된 위치입니다.|
| `DR_LOCAL_S3_REWARD_KEY` | `reward_function.py` 파일이 저장된 위치입니다.|
| `DR_LOCAL_S3_METRICS_PREFIX` | 메트릭이 저장될 위치입니다.|
| `DR_OA_NUMBER_OF_OBSTACLES` | 객체 회피를 위한 트랙상의 장애물 수입니다.|
| `DR_OA_MIN_DISTANCE_BETWEEN_OBSTACLES` | 장애물 간 최소 거리(미터)입니다.|
| `DR_OA_RANDOMIZE_OBSTACLE_LOCATIONS` | `True`로 설정하면 각 에피소드 후 장애물 위치가 무작위로 변경됩니다.|
| `DR_OA_IS_OBSTACLE_BOT_CAR` | `True`로 설정하면 장애물이 상자 대신 정지된 자동차로 나타납니다.|
| `DR_OA_OBJECT_POSITIONS` | 트랙상의 상자 위치입니다. 진행도(0에서 1 사이의 비율)와 안쪽 또는 바깥쪽 차선(-1 또는 1)으로 구성된 튜플입니다. 예: `"0.23,-1;0.46,1"`|
| `DR_H2B_IS_LANE_CHANGE` | `True`로 설정하면 봇 자동차가 구성에 따라 차선을 변경합니다.|
| `DR_H2B_LOWER_LANE_CHANGE_TIME` | 자동차가 차선을 변경하기 전 최소 시간(초)입니다.|
| `DR_H2B_UPPER_LANE_CHANGE_TIME` | 자동차가 차선을 변경하기 전 최대 시간(초)입니다.|
| `DR_H2B_LANE_CHANGE_DISTANCE` | 자동차가 차선을 변경하는 데 걸리는 거리(미터)입니다.|
| `DR_H2B_NUMBER_OF_BOT_CARS` | 트랙상의 봇 자동차 수입니다.|
| `DR_H2B_MIN_DISTANCE_BETWEEN_BOT_CARS` | 봇 자동차 간 최소 거리입니다.|
| `DR_H2B_RANDOMIZE_BOT_CAR_LOCATIONS` | `True`로 설정하면 각 에피소드 후 봇 자동차 위치가 무작위로 변경됩니다.|
| `DR_H2B_BOT_CAR_SPEED` | 봇 자동차의 속도(미터/초)입니다.|
| `DR_CLOUD` | `azure`, `aws`, `local`, `remote` 중 하나로 설정하여 스토리지가 어떻게 구성될지를 결정합니다.|
| `DR_AWS_APP_REGION` | (AWS 전용) 다른 AWS 리소스(Kinesis 등)의 지역입니다.|
| `DR_UPLOAD_S3_PROFILE` | AWS DeepRacer에 모델을 업로드하는 데 필요한 '실제' S3 자격 증명을 보유한 AWS CLI 프로필입니다.|
| `DR_UPLOAD_S3_BUCKET` | 모델이 업로드될 AWS DeepRacer 버킷의 이름입니다. (일반적으로 `aws-deepracer-`로 시작합니다.)|
| `DR_LOCAL_S3_PROFILE` | 사용할 자격 증명을 보유한 AWS 프로필의 이름입니다. AWS IAM 역할이 사용되지 않는 한 `~/.aws/credentials`에 저장됩니다.|
| `DR_GUI_ENABLE` | Robomaker에서 Gazebo GUI를 활성화하거나 비활성화합니다.|
| `DR_KINESIS_STREAM_NAME` | Kinesis 스트림 이름입니다. AWS KVS 서비스에 실제로 게시하는 경우 사용됩니다. 원하지 않으면 비워 두십시오.|
| `DR_KINESIS_STREAM_ENABLE` | 'Kinesis Stream'을 활성화하거나 비활성화합니다. `True`로 설정하면 AWS KVS 스트림(이름이 None이 아닌 경우)과 `/racecar/deepracer/kvs_stream` 주제로 게시됩니다. 자동차 경주를 보고 싶다면 `True`로 설정하십시오.|
| `DR_SAGEMAKER_IMAGE` | 훈련에 사용할 SageMaker 이미지를 결정합니다.|
| `DR_ROBOMAKER_IMAGE` | 훈련 또는 평가에 사용할 Robomaker 이미지를 결정합니다.|
| `DR_MINIO_IMAGE` | 사용할 Minio 이미지를 결정합니다.|
| `DR_COACH_IMAGE` | 훈련에 사용할 코치 이미지를 결정합니다.|
| `DR_WORKERS` | 훈련에 사용할 Robomaker 작업자 수입니다. 이 기능에 대한 추가 문서를 참조하십시오.|
| `DR_ROBOMAKER_MOUNT_LOGS` | `$DR_DIR/data/logs/robomaker/$DR_LOCAL_S3_MODEL_PREFIX`에 로그를 마운트하려면 `True`로 설정합니다.|
| `DR_ROBOMAKER_MOUNT_SIMAPP_DIR` | 수정된 Robomaker 번들의 경로입니다. 예: `/home/ubuntu/deepracer-simapp/bundle`.|
| `DR_CLOUD_WATCH_ENABLE` | 로그 파일을 AWS CloudWatch로 전송합니다.|
| `DR_CLOUD_WATCH_LOG_STREAM_PREFIX` | CloudWatch 로그 스트림 이름에 접두사를 추가합니다.|
| `DR_DOCKER_STYLE` | 유효한 옵션은 `Swarm`과 `Compose`입니다. OpenGL 최적화 컨테이너에는 Compose를 사용하십시오.|
| `DR_HOST_X` | Robomaker 내부에서 X-윈도우 서버를 시작하는 대신 호스트 X-윈도우 서버를 사용합니다. OpenGL 이미지에 필요합니다.|
| `DR_WEBVIEWER_PORT` | 모든 Robomaker 작업자를 한 번에 스트리밍할 수 있는 웹 뷰어 프록시의 포트입니다.|
| `CUDA_VISIBLE_DEVICES` | 다중 GPU 구성에서 사용됩니다. 이 기능에 대한 추가 문서를 참조하십시오.|
| `DR_TELEGRAF_HOST` | 실시간 메트릭을 보낼 호스트 이름입니다. 이를 활성화하려면 주석을 해제하십시오. Telegraf/InfluxDB/Grafana compose 스택이 이미 실행 중이어야 하며(`dr-start-metrics` 사용), 일반적으로 telegraf 컨테이너로 메트릭을 보내기 위해 `telegraf`로 설정해야 합니다.|
| `DR_TELEGRAF_PORT` | 실시간 메트릭을 보낼 UDP 포트를 정의합니다. 일반적으로 8092로 설정되어야 합니다.

## 명령어

| 명령어 | 설명 |
|--------|------|
| `dr-update` | 모든 스크립트와 환경 변수를 다시 로드합니다.|
| `dr-update-env` | `system.env`와 `run.env`에서 모든 환경 변수를 로드합니다.|
| `dr-upload-custom-files` | `custom_files/`에서 변경된 구성 파일을 `s3://{DR_LOCAL_S3_BUCKET}/custom_files`로 업로드합니다.|
| `dr-download-custom-files` | `s3://{DR_LOCAL_S3_BUCKET}/custom_files`에서 변경된 구성 파일을 `custom_files/`로 다운로드합니다.|
| `dr-start-training` | 현재 구성에 따라 로컬 VM에서 훈련 세션을 시작합니다.|
| `dr-increment-training` | 구성을 업데이트하여 현재 모델 접두사를 사전 훈련된 것으로 설정하고 일련 번호를 증가시킵니다.|
| `dr-stop-training` | 현재 로컬 훈련 세션을 중지합니다. 로그 파일을 업로드합니다.|
| `dr-start-evaluation` | 현재 구성에 따라 로컬 VM에서 평가 세션을 시작합니다.|
| `dr-stop-evaluation` | 현재 로컬 평가 세션을 중지합니다. 로그 파일을 업로드합니다.|
| `dr-start-loganalysis` | 포트 8888에서 사용할 수 있는 Jupyter 로그 분석 컨테이너를 시작합니다.|
| `dr-stop-loganalysis` | Jupyter 로그 분석 컨테이너를 중지합니다.|
| `dr-start-viewer` | 모든 Robomaker 스트림을 스트리밍할 수 있는 NGINX 프록시를 시작합니다. 원격으로 접근 가능합니다.|
| `dr-stop-viewer` | NGINX 프록시를 중지합니다.|
| `dr-logs-sagemaker` | 실행 중인 SageMaker 컨테이너의 로그를 표시합니다.|
| `dr-logs-robomaker` | 실행 중인 Robomaker 컨테이너의 로그를 표시합니다.|
| `dr-list-aws-models` | AWS DeepRacer S3 버킷에 현재 저장된 모델을 나열합니다.|
| `dr-set-upload-model` | 선택한 모델의 접두사와 이름으로 `run.env`를 업데이트합니다.|
| `dr-download-model` | '실제' S3 위치에서 선택한 로컬 접두사로 파일을 다운로드합니다.|
| `dr-upload-model` | `DR_LOCAL_S3_MODEL_PREFIX`에 정의된 모델을 `DR_UPLOAD_S3_PREFIX`에 정의된 AWS DeepRacer S3 접두사로 업로드합니다.<ul><li>f - 강제 업로드, 업로드 진행 여부에 대한 확인 질문 없음</li><li>w - 지정된 버킷/접두사에서 업로드 전에 대상 AWS DeepRacer 모델 구조를 삭제</li><li>d - 드라이런 모드, 대상에 대한 쓰기 또는 삭제 작업을 수행하지 않음</li><li>b - 기본값인 마지막 체크포인트 대신 최상의 체크포인트 업로드</li><li>p prefix - 지정된 S3 접두사로 모델 업로드</li><li>i - 접두사를 모델 이름으로 사용하여 모델 가져오기</li><li>I name - 특정 모델 이름으로 모델 가져오기</li></ul>
|
| `dr-upload-car-zip` | 물리적 자동차에 맞게 포맷된 파일 생성. <br>실행 중인 sagemaker 컨테이너에서 파일을 복사하고, 적절한 .tar.gz 파일로 포맷한 후 `s3://DR_LOCAL_S3_BUCKET/DR_LOCAL_S3_PREFIX`에 업로드합니다. <ul><li>이 방법의 한 가지 제한 사항은 최신 체크포인트만 사용하며, "최상의" 체크포인트나 이전 체크포인트를 사용할 수 있는 옵션이 없다는 것입니다.</li> <li>또 다른 제한 사항은 이 명령이 실행될 때 sagemaker 컨테이너가 실행 중이어야 한다는 것입니다.</li></ul>|
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