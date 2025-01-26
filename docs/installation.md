# 초기 설치

## 요구 사항

필요에 따라, 그리고 클라우드 플랫폼의 특정 요구 사항에 따라 VM을 원하는 대로 구성할 수 있습니다. CPU 전용 시스템과 GPU 시스템 모두 지원됩니다.

**AWS**:
* GPU가 활성화된 학습을 위해 G3, G4, P2 또는 P3 유형의 EC2 인스턴스 - 권장 사항은 g4dn.2xlarge입니다.
* CPU 학습을 위해 C5 또는 M6 유형 - 권장 사항은 c5.2xlarge입니다.
* Ubuntu 20.04
* 최소 30GB, 권장 40GB의 OS 디스크.
* 연결된 임시 드라이브
* GPU 사용 시 최소 8GB GPU-RAM.
* 최소 6 VCPUs 권장
* S3 버킷. EC2 인스턴스와 동일한 지역에 있는 것이 좋습니다.
* 내부 `sagemaker-local` 도커 네트워크는 기본적으로 `192.168.2.0/24`에서 실행됩니다. AWS IPC가 이 서브넷과 겹치지 않도록 하세요.

**Azure**:
* NVIDIA 그래픽 어댑터가 포함된 N-Series VM - 권장 사항은 NC6_Standard입니다.
* Ubuntu 20.04
* 시작하기에 충분한 표준 30GB OS 드라이브.
* 로그 분석 컨테이너를 사용하려면 추가로 32GB 데이터 디스크를 추가하는 것이 좋습니다.
* 최소 8GB GPU-RAM
* 최소 6 VCPUs 권장
* 액세스 키 인증을 위해 구성된 하나의 Blob 컨테이너가 있는 스토리지 계정.

**로컬**:
* 현대적이고 비교적 강력한 Intel 기반 시스템.
* Ubuntu 20.04, 다른 Linux 배포판도 작동할 가능성이 높습니다.
* 4코어 CPU, 8 vCPUs에 해당; 많을수록 좋습니다.
* Sagemaker가 GPU를 실행하기 위해 최소 8GB RAM이 있는 NVIDIA 그래픽 어댑터. Robomaker가 활성화된 GPU 인스턴스는 각각 약 1GB가 필요합니다.
* 시스템 RAM + GPU RAM은 최소 32GB여야 합니다.
* Windows Subsystem for Linux 2를 사용하여 Windows에서 DRfC Ubuntu 20.04를 실행할 수 있습니다. [Windows에 설치](windows.md)를 참조하세요.

## 설치

패키지에는 새 가상 머신을 위한 턴키 설정을 허용하는 준비 및 설정 스크립트가 포함되어 있습니다.

```shell
git clone https://github.com/aws-deepracer-community/deepracer-for-cloud.git
```

**클라우드 설정을 위해** 다음을 실행하세요:

```shell
cd deepracer-for-cloud && ./bin/prepare.sh
```

이 작업은 추가 드라이브를 파티션하고 모든 필수 구성 요소를 설치하여 VM을 준비합니다. 재부팅 후 `./bin/init.sh`를 계속 실행하여 전체 저장소를 설정하고 핵심 Docker 이미지를 다운로드합니다. 환경에 따라 최대 30분이 소요될 수 있습니다. 스크립트는 완료되면 `DONE` 파일을 생성합니다. 설치 스크립트는 로그인 시 모든 설정이 적용되도록 `.profile`을 조정합니다. 그렇지 않으면 `source bin/activate.sh`로 활성화를 실행하세요.

**로컬 설치의 경우** `bin/prepare.sh` 스크립트를 실행하지 않는 것이 좋습니다. 원하는 것보다 더 많은 변경을 할 수 있습니다. 대신 모든 필수 구성 요소가 설정되었는지 확인하고 `bin/init.sh`를 직접 실행하세요. 자세한 내용은 [다음 기사](https://awstip.com/deepracer-for-cloud-drfc-local-setup-3c6418b2c75a)를 참조하세요.

초기화 스크립트는 몇 가지 매개변수를 받습니다:

| 변수 | 설명 |
|----------|-------------|
| `-c <cloud>` | 구성할 클라우드 버전을 설정하고, `system.env`의 `DR_CLOUD` 매개변수를 자동으로 업데이트합니다. 옵션은 `azure`, `aws` 또는 `local`입니다. 기본값은 `local`입니다. |
| `-a <arch>` | 구성할 아키텍처를 설정합니다. `cpu` 또는 `gpu` 중 하나입니다. 기본값은 `gpu`입니다. |

## 환경 설정

초기화 스크립트는 환경(`Azure`, `AWS` 또는 `Local`)을 자동으로 감지하고 결과를 `system.env`의 `DR_CLOUD` 매개변수에 저장하려고 시도합니다. 또한 `-c <cloud>` 매개변수를 전달하여 이를 재정의할 수 있습니다. 예를 들어, 클라우드에서 minio 기반 `local` 모드를 실행하려는 경우입니다. 모드 간의 주요 차이점은 인증 메커니즘과 구성된 스토리지 유형에 기반합니다. 다음 장에서는 각 환경 유형을 개별적으로 검토합니다.

### AWS

AWS에서는 S3에 대한 인증을 두 가지 방법으로 설정할 수 있습니다: [IAM 역할](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)을 사용한 통합 로그인 또는 액세스 키를 사용하는 방법입니다.

#### IAM 역할

IAM 역할을 사용하려면:
* EC2 인스턴스와 동일한 지역에 빈 S3 버킷.
* 다음 권한이 있는 IAM 역할:
  * *새로운* S3 버킷과 DeepRacer 버킷 모두에 액세스.
  * AmazonVPCReadOnlyAccess
  * Kinesis로 스트리밍하려면 AmazonKinesisVideoStreamsFullAccess
  * CloudWatch
* 정의된 IAM 역할이 할당된 EC2 인스턴스.
* `system.env`를 다음과 같이 구성:
  * `DR_LOCAL_S3_PROFILE=default`
  * `DR_LOCAL_S3_BUCKET=<bucketname>`
  * `DR_UPLOAD_S3_PROFILE=default`
  * `DR_UPLOAD_S3_BUCKET=<your-aws-deepracer-bucket>`
* 구성이 적용되도록 `dr-update` 실행.

#### 수동 설정

IAM 사용자를 사용한 액세스의 경우:
* EC2 인스턴스와 동일한 지역에 빈 S3 버킷.
* 액세스 키가 설정된 실제 AWS IAM 사용자:
  * 사용자는 *새로운* 버킷과 전용 DeepRacer S3 버킷에 액세스할 수 있는 권한이 있어야 합니다.
  * `aws configure`를 사용하여 기본 프로필에 이를 구성.
* `system.env`를 다음과 같이 구성:
  * `DR_LOCAL_S3_PROFILE=default`
  * `DR_LOCAL_S3_BUCKET=<bucketname>`
  * `DR_UPLOAD_S3_PROFILE=default`
  * `DR_UPLOAD_S3_BUCKET=<your-aws-deepracer-bucket>`
* 구성이 적용되도록 `dr-update` 실행.

### Azure

Minio는 Azure Blob Storage를 S3 버킷으로 노출하는 게이트웨이 기능을 중단했습니다. Azure 모드는 이제 로컬 모드와 동일한 방식으로 minio를 설정합니다. awscli(`aws`)를 사용하여 파일을 수동으로 이동하려면 `aws $DR_LOCAL_PROFILE_ENDPOINT_URL s3 ...`를 사용하세요. 이는 구성에 맞게 `--profile` 및 `--endpoint-url` 매개변수를 설정합니다.

### 로컬

로컬 모드는 `docker/volumes` 디렉토리에 데이터를 호스팅하는 minio 서버를 실행합니다. 다른 점은 Azure 설정과 명령 호환성이 있다는 것입니다. 데이터는 Minio를 통해 액세스 가능하며 네이티브 S3를 통해서는 아닙니다. 로컬 모드에서 스크립트 세트는 다음을 요구합니다:

* `aws configure --profile minio`로 Minio 자격 증명을 구성하세요. 기본 구성은 `minio` 프로필을 사용하여 MINIO를 구성합니다. 사용자 이름이나 비밀번호는 자유롭게 선택할 수 있지만, 사용자 이름은 최소 3자, 비밀번호는 최소 8자여야 합니다.
* AWS DeepRacer에 모델을 업로드할 수 있도록 `aws configure`로 구성된 실제 AWS IAM 사용자.
* `system.env`를 다음과 같이 구성:
  * `DR_LOCAL_S3_PROFILE=default`
  * `DR_LOCAL_S3_BUCKET=<bucketname>`
  * `DR_UPLOAD_S3_PROFILE=default`
  * `DR_UPLOAD_S3_BUCKET=<your-aws-deepracer-bucket>`
* 구성이 적용되도록 `dr-update` 실행.

## 첫 실행

첫 실행을 위해 다음 최종 단계가 필요합니다. 이는 모든 기본값으로 학습 실행을 생성합니다.

* `custom_files/`에 사용자 정의 파일을 정의하세요 - 샘플은 `defaults`에서 찾을 수 있으며 복사해야 합니다:
  * `hyperparameters.json` - 학습 하이퍼파라미터 정의
  * `model_metadata.json` - 액션 공간 및 센서 정의
  * `reward_function.py` - 보상 함수 정의
* `dr-upload-custom-files`로 파일을 버킷에 업로드하세요. 필요하면 minio도 시작됩니다.
* `dr-start-training`으로 학습 시작

잠시 후 화면에 sagemaker 로그가 표시됩니다.

## 문제 해결

여기서는 발생할 수 있는 특정 문제에 대한 문제 해결 힌트를 제공합니다.

### 로컬 학습 문제 해결

| 문제 | 문제 해결 힌트 |
|------------- | ---------------------|
"Sagemaker가 실행 중이 아닙니다"라는 메시지가 표시됨 | `docker -ps a`를 실행하여 컨테이너가 실행 중인지 또는 오류로 인해 중지되었는지 확인하세요. 새로 설치한 후 실행 중인 경우 시스템을 다시 시작해 보세요. 특정 컨테이너의 도커 오류 확인 | `docker logs -f <containerid>` 실행
`./bin/init.sh -c local -a cpu` 실행 시 "Error response from daemon: could not choose an IP address to advertise since this system has multiple addresses on interface <your_interface> ..."라는 메시지가 표시됨 | 여러 IP 주소가 있으며 `./bin/init.sh` 내에서 하나를 지정해야 함을 의미합니다.<br> 사용하고자 하는 IP가 상관없다면, 다음 명령어를 실행하여 첫 번째 IP를 얻을 수 있습니다: ```ifconfig \| grep $(route \| awk '/^default/ {print $8}') -a1 \| grep -o -P '(?<=inet ).*(?= netmask)```.<br> `./bin/init.sh`를 편집하고 `docker swarm init` 라인을 찾아 `docker swarm init --advertise-addr <your_IP>`로 변경하세요.<br> `./bin/init.sh -c local -a cpu`를 다시 실행하세요.
`dr-*` 명령어가 없음 | `source bin/activate.sh` 실행.


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