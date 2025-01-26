# 자동차 관찰하기

훈련 및 평가 중 자동차를 관찰하는 방법에는 여러 가지가 있습니다. 포트와 '기능'은 도커 모드(스웜 vs. 컴포즈)뿐만 아니라 훈련과 평가 간에도 다릅니다.

## 뷰어를 사용한 훈련

DRfC에는 최대 6명의 작업자로부터 비디오 스트림을 하나의 웹페이지에 표시할 수 있는 내장 뷰어가 있습니다.

뷰어는 `dr-start-viewer`로 시작할 수 있으며 `http://localhost:8100` 또는 `http://127.0.0.1:8100`에서 사용할 수 있습니다. 훈련이 재시작될 경우 `dr-update-viewer`를 사용하여 뷰어를 업데이트해야 하며, 이는 새로운 컨테이너에 연결해야 하기 때문입니다.

`dr-start-training`에 `-v` 플래그를 사용하여 뷰어를 자동으로 시작/업데이트할 수도 있습니다.

## ROS 스트림 뷰어

ROS 스트림 뷰어는 ROS의 내장 기능으로, ROSImg 메시지를 발행하는 ROS의 모든 주제를 스트리밍합니다. 뷰어는 자동으로 시작됩니다.

### 포트

| 도커 모드  | 훈련         | 평가      | 설명
| -------- | -------- | -------- | -------- | 
| 스웜      | 8080 + `DR_RUN_ID` |  8180 + `DR_RUN_ID` | 기본값 8080/8180. 여러 작업자가 하나의 포트를 공유하며, F5를 눌러 순환할 수 있습니다.
| 컴포즈 | 8080-8089 | 8080-8089 | 각 작업자가 고유한 포트를 가집니다.

### 주제

| 주제  | 설명         | 
| -------- | -------- | 
| `/racecar/camera/zed/rgb/image_rect_color`      | 차량 내 비디오 스트림. 추론에 사용됩니다. | 
| `/racecar/main_camera/zed/rgb/image_rect_color`      | 차량을 따라가는 카메라. 오버레이 없는 스트림 | 
| `/sub_camera/zed/rgb/image_rect_color`      | 트랙의 상단 뷰 | 
| `/racecar/deepracer/kvs_stream`      | 차량을 따라가는 카메라. 오버레이가 있는 스트림. 훈련과 평가에서 다른 오버레이 | 
| `/racecar/deepracer/main_camera_stream`      | `kvs_stream`과 동일하며, MP4 제작에 사용되는 주제. `DR_EVAL_SAVE_MP4=True`일 경우 평가에서만 활성화 | 

## 평가를 파일로 저장하기

평가 중(`dr-start-evaluation`), `DR_EVAL_SAVE_MP4=True`일 경우 S3 버킷의 MP4 폴더에 세 개의 MP4 파일이 생성됩니다. 이 파일들은 차량 내 카메라, 상단 카메라, 차량을 따라가는 카메라를 포함합니다.


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