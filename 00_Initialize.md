# [0] Initialize

## 0.1. 리전 선택
- AWS 콘솔에 로그인 후 EC2 및 S3 등을 사용할 리전을 선택합니다.
> 서울의 경우 `ap-northeast-2`를 선택합니다.

## 0.2. S3 버킷 생성
- [S3](https://console.aws.amazon.com/s3/bucket) 에서 버킷을 생성합니다. 
    - 버킷 이름 : `drfc-<your-name>`
        > 참고로 버킷 이름은 유일해야 합니다.
    - 버킷 버전 관리 : `활성화`

- 버킷의 ARN (예 : `arn:aws:s3:::drfc-<your-name>`)을 복사 후 저장합니다. (나중에 사용)
- 버킷 정책설정 (옵션)
    - DeepRacer 콘솔에서 모델을 가져오려면 아래의 버킷 정책이 필요합니다.
    - 버킷 선택 > 속성 > 버킷 정책 편집
    - 아래의 정책 (JSON)을 붙여넣고 버킷 이름을 `drfc-<your-name>`으로 수정합니다.
    ```json
    {
        "Version": "2012-10-17",
        "Id": "AwsDeepracerServiceAccess",
        "Statement": [
            {
                "Sid": "Stmt1737257399614",
                "Effect": "Allow",
                "Principal": {
                    "Service": "deepracer.amazonaws.com"
                },
                "Action": [
                    "s3:GetObjectAcl",
                    "s3:GetObject",
                    "s3:PutObject",
                    "s3:PutObjectAcl"
                ],
                "Resource": [
                    "arn:aws:s3:::drfc-<your-name>",
                    "arn:aws:s3:::drfc-<your-name>/*"
                ]
            }
        ]
    }
    ```


## 0.3. DRfC AMI 생성
- `ap-northest-2` 서울 리전의 경우 에이아이캐슬에서 사전에 생성한 공개 AMI를 이용할 수 있으므로 건너 뛰어도 됩니다.

### 0.3.1. 인스턴스 시작
- [EC2 콘솔](https://console.aws.amazon.com/ec2/home) > 인스턴스 > 인스턴스 시작
- AMI : [Ubuntu 공식 AMI](https://cloud-images.ubuntu.com/locator/ec2/)에서 자신의 리전에 해당하는 `20.04 LTS` 버전의 `ami-******`를 복사하여 검색한 후 이에 해당하는 커뮤니티 AMI 를 선택
- 인스턴스 유형 : `c5.2xlarge` (cpu 계열) 또는 `g4dn.2xlarge` (gpu 계열)
- 키 페어 : 새 키 페어 생성 (이름, 유형, 형식 무관)
- 방화벽(보안 그룹) : 새 보안 그룹 생성  - SSH 트래픽 허용
- 스토리지 : 40GB

### 0.3.2. SSH 접속 및 초기 설정
- SSH 접속 : 인스턴스 선택 > 연결 > EC2 인스턴스 연결 > 사용자 이름(ubuntu) > 연결
- DRfC 필수 구성요소 설치
    ```bash
    # DRfC git 레파지토리 클론
    git clone https://github.com/aws-deepracer-community/deepracer-for-cloud.git

    # 클라우드 설정
    cd deepracer-for-cloud && ./bin/prepare.sh
    ```
- 재부팅
    ```bash
    sudo reboot
    ```
- SSH 재접속 : 인스턴스 선택 > 연결 > EC2 인스턴스 연결 > 사용자 이름(ubuntu) > 연결
- DRfC 초기 설정
    - cpu 계열
        ```bash
        cd ~/deepracer-for-cloud
        bin/init.sh -c aws -a cpu
        sed -i 's/dr-logs-sagemaker -w 15/dr-logs-sagemaker -w 30/g' scripts/training/start.sh
        sed -i 's/^#\s*//' system.env
        sed -i 's/^#\s*//' run.env
        sed -i 's/^#\s*//' defaults/template-run.env
        sed -i 's/^#\s*//' defaults/template-system.env
        sed -i 's/^#\s*//' defaults/template-worker.env
        ```
    - gpu 계열
        ```bash
        cd ~/deepracer-for-cloud
        bin/init.sh -c aws -a gpu
        sed -i 's/dr-logs-sagemaker -w 15/dr-logs-sagemaker -w 30/g' scripts/training/start.sh
        sed -i 's/^#\s*//' system.env
        sed -i 's/^#\s*//' run.env
        sed -i 's/^#\s*//' defaults/template-run.env
        sed -i 's/^#\s*//' defaults/template-system.env
        sed -i 's/^#\s*//' defaults/template-worker.env
        source bin/activate.sh
        source utils/setup-xorg.sh
        # sudo apt install x11vnc -y
        
        ```

- 유틸리티 설치
    - tmux 설치
        ```bash
        sudo apt install tmux
        ```
    - nmon 설치
        ```bash
        sudo apt install nmon
        ```
    - netstat 설치
        ```bash
        sudo apt install net-tools
        ```
    - gpustat 설치 (gpu 계열만)
        ```bash
        sudo apt install gpustat
        ```

### 0.3.3. AMI 생성 및 인스턴스 종료
- 인스턴스 선택 > 작업 > 이미지 및 템플릿 > 이미지 생성 > 이미지 이름( 예: `DRfC-ami`) > 이미지 생성

## 0.4. EC2 Role 생성
### 0.4.1. S3 정책 생성
- [IAM Policy 생성](https://us-east-1.console.aws.amazon.com/iam/home#/policies/create)에서 정책을 생성합니다.
- 아래의 정책 (JSON)을 붙여넣고 정책 이름은 `drfc-s3-policy`로 설정 후 생성합니다. 
    > Resource 부분의 `drfc-<your-name>`를 위에서 생성한 S3 버킷 이름으로 수정합니다.
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "s3:*",
                "Resource": [
                    "arn:aws:s3:::drfc-<your-name>",
                    "arn:aws:s3:::drfc-<your-name>/*",
                ]
            }
        ]
    }
    ```

### 0.4.2. EC2 Role 생성
- [IAM Role 생성](https://us-east-1.console.aws.amazon.com/iam/home#/roles/create) 에서 역할을 생성합니다.
    - 엔터티 : AWS 서비스
    - 사용 사례 : EC2
    - 권한 정책 : `drfc-s3-policy`
    - 역할 이름 : `drfc-ec2-role`

## 0.5. EC2 인스턴스 실행

### 0.5.1. 보안그룹 생성
- [VPC 보안그룹 생성](https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home#SecurityGroups) 에서 `보안 그룹 생성`을 클릭하여 생성합니다.
- 보안 그룹 이름 : `drfc-sg`
- 설명 : `deepracer-for-cloud`
- 인바운드 규칙
    | 유형 | 프로토콜 | 포트 범위 | 소스 | 설명 |
    | --- | --- | --- | --- | --- |
    | SSH | TCP | 22 | Anywhere-IPv4 | SSH |
    | 사용자 지정 TCP | TCP | 8080-8100 | Anywhere-IPv4 | Web Monitoring |
    | 사용자 지정 TCP | TCP | 5900-5910 | Anywhere-IPv4 | VNC |
    | 사용자 지정 TCP | TCP | 8888 | Anywhere-IPv4 | Jupyter Notebook |

- 아웃바운드 규칙
    - 유형 : 모든 트래픽
    - 프로토콜 : 전체
    - 포트 범위 : 전체
    - 대상 : Anywhere-IPv4

### 0.5.2. 키페어 생성
- [키페어 생성](https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home#KeyPairs) 에서 키페어를 생성합니다.
    - 키페어 이름 : `drfc-key`
    - 키페어 유형 : `RSA`
    - 프라이빗 키 파일 형식 : `pem`
    - 키페어 다운로드 : `drfc-key.pem` (로컬에 저장)

### 0.5.3. EC2 인스턴스 생성
- [EC2 인스턴스 생성](https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home#Instances) 에서 `인스턴스 시작`을 통해 생성합니다.
    - 이름 : (예: `drfc-instance`)
    - AMI : 위에서 생성한 DRfC 이미지 선택 
        > `ap-northeast-2` 서울 리전의 경우 사전에 생성한 공개 AMI 사용  

        | AMI 이름 | AMI ID | 설명 | 리전 |
        | --- | --- | --- | --- |
        | DeepRacer for Cloud - cpu (25.01.23) | `ami-0079d681e1ea0716f` | c5 (cpu 계열) | ap-northeast-2 |
        | DeepRacer for Cloud - gpu (25.01.23) | `ami-086920da914efa8c0` | g4dn (gpu 계열) | ap-northeast-2 |
    - 인스턴스 유형 : 아래 중 선택 (cpu 계열은 c5, gpu 계열은 g4dn)
        | type | vCPU | Memory | GPU | RoboMaker 권장 수 |
        | --- | --- | --- | --- | --- |
        | c5.2xlarge | 8 | 16 GB | - | 1 |
        | c5.4xlarge | 16 | 32 GB | - | 3 |
        | c5.9xlarge | 36 | 72 GB | - | 8 |
        | g4dn.2xlarge | 8 | 32 GB | T4 16GB | 1 |
        | g4dn.4xlarge | 16 | 64 GB | T4 16GB | 3 |
        | g4dn.8xlarge | 32 | 128 GB | T4 16GB | 8 |
        > SageMaker는 약 4 vCPU를 사용하고 RoboMaker는 1개 당 약 4 vCPU를 사용합니다.  
    - 키 페어 : `drfc-key` (위에서 생성한 키페어)
    - 네트워크 설정
        - 기존 보안 그룹 선택 > `drfc-sg` (위에서 생성한 보안그룹)
    - 스토리지 구성 : 40 GB gp2
    - 고급 세부 정보
        - IAM 인스턴스 프로파일 : `drfc-ec2-role` (위에서 생성한 역할)
        - 종료 방지 (옵션) : 실수로 인스턴스를 종료하는 것을 방지하고 싶다면 `활성화`를 선택합니다.
        - 구매 옵션 (옵션) : 저렴한 가격에 인스턴스를 시작하고 싶다면 `스팟 인스턴스`를 선택합니다. 단, 사용 중에 인스턴스가 중지될 수 있습니다.
            > Spot 가격 : [Link 1](https://instances.vantage.sh/) | [Link 2](https://aws.amazon.com/ko/ec2/spot/pricing)
- 인스턴스 생성 후 ARN을 복사하여 저장합니다.

### 0.5.4. 탄력적 IP 할당 (옵션)
- IP 주소가 바뀌는 것을 방지하고자 하는 경우 탄력적 IP를 할당합니다.
- [탄력적 IP 주소](https://console.aws.amazon.com/ec2/home#Addresses:)에서 `탄력적 IP 주소 할당`을 클릭하여 할당합니다.
- 생성된 IP를 선택하고 `작업` > `연결`을 클릭하여 위에서 생성한 인스턴스를 선택합니다.


## 0.6. IAM 유저 생성

### 0.6.1. EC2 정책 생성
- [IAM Policy 생성](https://us-east-1.console.aws.amazon.com/iam/home#/policies/create)에서 정책을 생성합니다.
- 아래의 정책 (JSON)을 붙여넣고 정책 이름은 `drfc-ec2-policy`로 설정 후 생성합니다. 
    > `<your-region>`, `<your-account-id>`, `<instance-id>`는 위에서 복사한 인스턴스 ARN을 참고하여 수정합니다.  
    > 만약 모든 인스턴스에 대한 권한을 부여하려면 `"Resource": "*"`로 수정합니다.
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:StartInstances",
                "ec2:StopInstances",
                "ec2:TerminateInstances"
            ],
            "Resource": "arn:aws:ec2:<your-region>:<your-account-id>:instance/<instance-id>"
        }
    ]
}
```

### 0.6.2. Cloudtrail 모니터링 권한
- [IAM Policy 생성](https://us-east-1.console.aws.amazon.com/iam/home#/policies/create)에서 정책을 생성합니다.
- 아래의 정책 (JSON)을 붙여넣고 정책 이름은 `drfc-cloudtrail-policy`로 설정 후 생성합니다.
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowCloudTrailLookupEvents",
            "Effect": "Allow",
            "Action": [
                "cloudtrail:LookupEvents"
            ],
            "Resource": "*"
        }
    ]
}
```

### 0.6.3. IAM 유저 생성
- [IAM User 생성](https://us-east-1.console.aws.amazon.com/iam/home#/users/create)에서 유저를 생성합니다.
- 유저 이름 : `drfc-user`
- AWS Management Console에 대한 사용자 액세스 권한 제공 : `활성화`
- 권한 설정 (필수)
    - `drfc-s3-policy`
    - `drfc-ec2-policy`
    - `drfc-cloudtrail-policy`
- 권한 설정 (옵션): DeepRacer 콘솔을 이용하고자 하는 경우
    - `AWSDeepRacerAccountAdminAccess`
    - `AWSDeepRacerFullAccess`
    - `AWSDeepRacerCloudFormationAccessPolicy`
    - `AWSDeepRacerRoboMakerAccessPolicy`
    - `AWSDeepRacerServiceRolePolicy`

### 0.6.4. IAM 유저 액세스 키 생성
- [`drfc-user` 사용자 클릭 > 보안 자격 증명 > 액세스 키 만들기](https://us-east-1.console.aws.amazon.com/iam/home#/users/details/drfc-user/create-access-key)
    - 사용 사례: Command Line Interface (CLI)
    - 확인 : 체크
- 생성된 액세스 키 ID (`<your-access-key-id>`) 와 비밀 액세스 키 (`<your-secret-access-key>`)를 저장합니다.


## 0.7. 로컬 세팅
### 0.7.1. 키페어 저장
- 위에서 생성한 키페어를 (예: `drfc-key.pem`)를 작업 폴더에 저장.

### 0.7.2. `config.yml` 저장
-  `config.yml`를 아래와 같이 작성 후 작업 폴더에 저장.
```yaml
region: <your-region>
s3-bucket: drfc-<your-name>
instance-key-path: drfc-key.pem
instance-id: <instance-id>

ec2-user:   # ubuntu
start-utc:  # 2025-01-14T01:13:11Z
```

### 0.7.3. `.env` 저장
- `.env`를 아래와 같이 작성 후 작업 폴더에 저장.
```bash
AWS_ACCESS_KEY_ID=<your-access-key-id>
AWS_SECRET_ACCESS_KEY=<your-secret-access-key>
```