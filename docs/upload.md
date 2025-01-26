# AWS 콘솔에 모델 업로드

2020년 7월 말부터 AWS DeepRacer 콘솔이 재설계되어, 이제 모델을 업로드하는 방식이 변경되었습니다. 이를 통해 모델을 평가하거나 AWS가 주최하는 서밋 또는 가상 리그 이벤트에 제출할 수 있습니다.

## 업로드 버킷 생성

`us-east-1`에 고유한 버킷을 생성하는 것이 권장됩니다. 이 버킷은 훈련 버킷, 로컬 또는 EC2 인스턴스에 가까운 AWS 지역 간의 '전송' 역할을 합니다.

버킷은 '객체가 공개될 수 있음'으로 정의되어야 하며, AWS는 가져오기 과정의 일환으로 버킷의 데이터에 접근할 수 있는 특정 IAM 정책을 생성할 것입니다.

## 업로드 버킷 구성

`system.env`에서 `DR_UPLOAD_S3_BUCKET`을 생성한 버킷의 이름으로 설정합니다.

`run.env`에서 `DR_UPLOAD_S3_PREFIX`를 원하는 접두사로 설정합니다.

## 모델 업로드

시스템을 구성한 후 `dr-upload-model`을 실행할 수 있습니다. 이는 `s3://DR_LOCAL_S3_BUCKET/DR_LOCAL_S3_PREFIX`의 필요한 부분을 `s3://DR_UPLOAD_S3_BUCKET/DR_UPLOAD_S3_PREFIX`로 복사합니다.

업로드가 완료되면 AWS DeepRacer 콘솔의 [모델 가져오기](https://console.aws.amazon.com/deepracer/home?region=us-east-1#models/importModel) 기능을 사용하여 모델을 모델 저장소에 로드할 수 있습니다.

## 알아야 할 사항

### 업로드 스위치
업로드 명령에는 여러 유용한 스위치가 있습니다:
  * f - 강제 업로드, 업로드 진행 여부에 대한 확인 질문 없음
  * w - 지정된 버킷/접두사에서 업로드 전에 대상 AWS DeepRacer 모델 구조를 삭제
  * d - 드라이런 모드, 대상에 대한 쓰기 또는 삭제 작업을 수행하지 않음
  * b - 기본값인 마지막 체크포인트 대신 최상의 체크포인트 업로드
  * p prefix - 지정된 S3 접두사로 모델 업로드
  * i - 접두사를 모델 이름으로 사용하여 모델 가져오기
  * I name - 특정 모델 이름으로 모델 가져오기

### 가져오기
가져오기 스위치(`-i` 또는 `-I`)를 사용하려면 몇 가지 사전 요구 사항이 있습니다.

* `pip install`로 설치할 Python 패키지:
  * pandas
  * deepracer-utils
* `python -m deepracer install-cli --force`로 boto3 서비스 `deepracer` 설치.
* Deepracer 서비스가 S3에 접근할 수 있는 IAM 역할 생성. `system.env`의 `DR_UPLOAD_S3_ROLE`에 ARN 선언.

### 모델 관리
모델을 어떻게 관리할지 결정해야 합니다. AWS로 업로드하면 로컬에서 생성된 모든 파일이 보존되지 않으므로 로컬 파일을 삭제하면 이전 모델로 돌아가 훈련을 재개하기 어려울 수 있습니다.

### 물리적 자동차에 맞게 포맷된 파일 생성 및 S3에 업로드
AWS 콘솔을 거치지 않고 DRfC에서 직접 물리적 자동차에서 실행하는 데 필요한 형식의 파일을 생성할 수도 있습니다. 이는 'dr-upload-car-zip'을 실행하여 수행됩니다. 이는 실행 중인 sagemaker 컨테이너에서 파일을 복사하고, 적절한 .tar.gz 파일로 포맷한 후 `s3://DR_LOCAL_S3_BUCKET/DR_LOCAL_S3_PREFIX`에 업로드합니다. 이 방법의 한 가지 제한 사항은 최신 체크포인트만 사용하며, "최상의" 체크포인트나 이전 체크포인트를 사용할 수 있는 옵션이 없다는 것입니다. 또 다른 제한 사항은 이 명령이 실행될 때 sagemaker 컨테이너가 실행 중이어야 한다는 것입니다.


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