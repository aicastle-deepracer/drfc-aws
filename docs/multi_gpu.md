# 하나의 컴퓨터에서 여러 GPU 사용

경우에 따라 여러 개의 GPU가 장착된 컴퓨터를 사용할 수 있습니다. 이는 일반 그래픽용 GPU(예: GTX 10 시리즈, RTX 20 시리즈)와 Tesla K40, K80 또는 M40과 같은 데이터 센터 GPU가 있는 워크스테이션에서 흔히 볼 수 있습니다.

이러한 환경에서는 DeepRacer가 모든 작업을 임의의 GPU에 할당하려고 하여 메모리 부족 문제가 발생할 수 있습니다.

## 사용 가능한 GPU 확인

Tensorflow를 사용하여 `utils/cuda-check.sh`를 실행하면 사용 가능한 장치에 대한 개요를 확인할 수 있습니다.

출력 예시는 다음과 같습니다:
```
2020-07-04 12:25:55.179580: I tensorflow/core/platform/cpu_feature_guard.cc:141] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 FMA
2020-07-04 12:25:55.547206: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1411] Found device 0 with properties: 
name: GeForce GTX 1650 major: 7 minor: 5 memoryClockRate(GHz): 1.68
pciBusID: 0000:04:00.0
totalMemory: 3.82GiB freeMemory: 3.30GiB
2020-07-04 12:25:55.732066: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1411] Found device 1 with properties: 
name: Tesla M40 24GB major: 5 minor: 2 memoryClockRate(GHz): 1.112
pciBusID: 0000:81:00.0
totalMemory: 22.41GiB freeMemory: 22.30GiB
2020-07-04 12:25:55.732141: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1490] Adding visible gpu devices: 0, 1
2020-07-04 12:25:56.745647: I tensorflow/core/common_runtime/gpu/gpu_device.cc:971] Device interconnect StreamExecutor with strength 1 edge matrix:
2020-07-04 12:25:56.745719: I tensorflow/core/common_runtime/gpu/gpu_device.cc:977]      0 1 
2020-07-04 12:25:56.745732: I tensorflow/core/common_runtime/gpu/gpu_device.cc:990] 0:   N N 
2020-07-04 12:25:56.745743: I tensorflow/core/common_runtime/gpu/gpu_device.cc:990] 1:   N N 
2020-07-04 12:25:56.745973: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1103] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 195 MB memory) -> physical GPU (device: 0, name: GeForce GTX 1650, pci bus id: 0000:04:00.0, compute capability: 7.5)
2020-07-04 12:25:56.750352: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1103] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:1 with 1147 MB memory) -> physical GPU (device: 1, name: Tesla M40 24GB, pci bus id: 0000:81:00.0, compute capability: 5.2)
2020-07-04 12:25:56.774305: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1490] Adding visible gpu devices: 0, 1
2020-07-04 12:25:56.774408: I tensorflow/core/common_runtime/gpu/gpu_device.cc:971] Device interconnect StreamExecutor with strength 1 edge matrix:
2020-07-04 12:25:56.774425: I tensorflow/core/common_runtime/gpu/gpu_device.cc:977]      0 1 
2020-07-04 12:25:56.774436: I tensorflow/core/common_runtime/gpu/gpu_device.cc:990] 0:   N N 
2020-07-04 12:25:56.774446: I tensorflow/core/common_runtime/gpu/gpu_device.cc:990] 1:   N N 
2020-07-04 12:25:56.774551: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1103] Created TensorFlow device (/device:GPU:0 with 195 MB memory) -> physical GPU (device: 0, name: GeForce GTX 1650, pci bus id: 0000:04:00.0, compute capability: 7.5)
2020-07-04 12:25:56.774829: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1103] Created TensorFlow device (/device:GPU:1 with 1147 MB memory) -> physical GPU (device: 1, name: Tesla M40 24GB, pci bus id: 0000:81:00.0, compute capability: 5.2)
['/device:GPU:0', '/device:GPU:1']
```
이 경우 CUDA 장치 #0은 GTX 1650이고 CUDA 장치 #1은 Tesla M40입니다.

### 장치 선택

Sagemaker와 Robomaker의 CUDA 할당을 제어하려면 `system.env` 파일에서 다음 변수를 설정하세요:

```
DR_ROBOMAKER_CUDA_DEVICES=0
DR_SAGEMAKER_CUDA_DEVICES=1
```

숫자는 컨테이너가 사용할 GPU의 CUDA 번호입니다.

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