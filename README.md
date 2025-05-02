# Animation Pose Extract Model 

애니매이션 포즈 추출하기 위한 모델 실행코드 입니다.

## 요구사항
- Python 3.11 또는 3.12 / CUDA 12.x

## 프로젝트 구조
```
Pose_Ext_Model/
├── API_WHAM.ipynb						# 실행 예제코드 모음
├── Dockerfile							# 도커 이미지 정의(미완성)
├── LICENSE								
├── Makefile
├── README.md
├── checkpoints
│   ├── dpvo.pth
│   ├── hmr2a.ckpt
│   ├── vitpose-h-multi-coco.pth
│   ├── wham_vit_bedlam_w_3dpw.pth.tar
│   ├── wham_vit_w_3dpw.pth.tar
│   ├── yolov8m.pt
│   └── yolov8x.pt
├── configs
│   ├── DPVO
│   │   └── default.yaml
│   ├── VIT
│   │   ├── coco.py
│   │   ├── default_runtime.py
│   │   └── td-hm_ViTPose-small_8xb64-210e_coco-256x192.py
│   ├── config.py
│   ├── constants.py
│   └── yamls
│       ├── demo.py
│       ├── demo.yaml
│       └── model_base.yaml
├── dataset
│   └── body_models
│       ├── J_regressor_coco.npy
│       ├── J_regressor_feet.npy
│       ├── J_regressor_h36m.npy
│       ├── J_regressor_wham.npy
│       ├── coco_aug_dict.pth
│       ├── smpl
│       │   ├── SMPL_FEMALE.pkl
│       │   ├── SMPL_MALE.pkl
│       │   └── SMPL_NEUTRAL.pkl
│       ├── smpl_mean_params.npz
│       └── smplx2smpl.pkl
├── demo.py
├── fetch_demo_data.sh
├── output
│   └── demo
│       ├── slam_results.pth
│       ├── tiktok_video
│       │   ├── output.mp4
│       │   ├── slam_results.pth
│       │   ├── tiktok_video.mp4_smpl_output.npz
│       │   └── tracking_results.pth
│       └── tracking_results.pth
├── requirements.txt
├── run_demo.sh
├── setup.py
├── tiktok_video.mp4
├── tools								# 특정 데이터셋용 eval 코드
│   ├── evaluate_3dpw.py
│   ├── evaluate_emdb.py
│   └── evaluate_rich.py
├── wham
│   ├── config
│   │   └── default.py					# 포즈 추출 모델 학습 설정
│   ├── constants.py
│   ├── data
│   │   ├── __init__.py
│   │   ├── _custom.py
│   │   ├── _dataset.py
│   │   ├── _dataset_eval.py
│   │   ├── dataloader.py
│   │   └── normalizer.py
│   ├── eval
│   │   └── eval_utils.py
│   ├── models
│   │   ├── __init__.py
│   │   ├── layers
│   │   │   ├── __init__.py
│   │   │   ├── modules.py
│   │   │   └── utils.py
│   │   ├── preproc
│   │   │   ├── backbone
│   │   │   │   │
│   │   │   │   ├── hmr2.py
│   │   │   │   ├── pose_transformer.py
│   │   │   │   ├── smpl_head.py
│   │   │   │   ├── t_cond_mlp.py
│   │   │   │   ├── utils.py
│   │   │   │   └── vit.py
│   │   │   ├── detector.py
│   │   │   ├── extractor.py
│   │   │   └── slam.py
│   │   ├── smpl.py
│   │   ├── smplify
│   │   │   ├── __init__.py
│   │   │   ├── losses.py
│   │   │   └── smplify.py
│   │   └── wham.py
│   ├── utils
│   │   ├── data_utils.py
│   │   ├── imutils.py
│   │   ├── kp_utils.py
│   │   ├── transforms.py
│   │   └── utils.py
│   └── vis
│       ├── renderer.py
│       ├── run_vis.py
│       └── tools.py

```

## 설치 및 진행
### 데이터셋 다운로드
1. SMPL 튜닝데이터
```bash
# Auxiliary SMPL-related data
wget "https://drive.google.com/uc?id=1pbmzRbWGgae6noDIyQOnohzaVnX_csUZ&export=download&confirm=t" -O 'dataset/body_models.tar.gz'
tar -xvf dataset/body_models.tar.gz -C dataset/
rm -rf dataset/body_models.tar.gz
```
2. checkpoints 다운로드
```bash
# Checkpoints
mkdir checkpoints
gdown "https://drive.google.com/uc?id=1i7kt9RlCCCNEW2aYaDWVr-G778JkLNcB&export=download&confirm=t" -O 'checkpoints/wham_vit_w_3dpw.pth.tar'
gdown "https://drive.google.com/uc?id=19qkI-a6xuwob9_RFNSPWf1yWErwVVlks&export=download&confirm=t" -O 'checkpoints/wham_vit_bedlam_w_3dpw.pth.tar'
gdown "https://drive.google.com/uc?id=1J6l8teyZrL0zFzHhzkC7efRhU0ZJ5G9Y&export=download&confirm=t" -O 'checkpoints/hmr2a.ckpt'
gdown "https://drive.google.com/uc?id=1kXTV4EYb-BI3H7J-bkR3Bc4gT9zfnHGT&export=download&confirm=t" -O 'checkpoints/dpvo.pth'
wget "https://github.com/ultralytics/assets/releases/download/v8.1.0/yolov8m.pt" -O 'checkpoints/yolov8m.pt'
```

### 로컬 개발 환경

1. Python 3.11 또는 3.12 설치 확인
```bash
python --version
# Python 3.11.x 또는 3.12.x가 출력되어야 함
```

2. Conda or venv 가상환경 생성 및 활성화
```bash
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
---
conda create -n xxxx python=3.12
```

3. torch 설치
``` bash
pip install torch==2.6.0 torchvision==0.21.0 torchaudio==2.6.0 --index-url https://download.pytorch.org/whl/cu124

```
4. MM 관련 라이브러리 빌드 (10분 이상 시간 걸림)
```bash
git clone https://github.com/open-mmlab/mmdetection.git
cd mmdetection
pip install -v -e .

git clone -b v2.1.0 --single-branch https://github.com/open-mmlab/mmcv.git
cd mmcv
pip install -v -e .

python .dev_scripts/check_installation.py 
# CUDA avaliable: True, CUDA 버전 출력되어야 함
```

5. 의존성 설치
```bash
pip install -r requirements.txt
```
6. 모션 추출 실행
```bash
python demo.py --video tiktok_video.mp4 --visualize --save_npy
```
7. 출력 shape, dtpe
```
type(npyfile)
>>> numpy.lib.npyio.NpzFile
npyfile.files
>>> ['arr_0'] 
# 0: 각 객체별 ID
pose		(877, 72)
trans		(877, 3)
betas		(877, 10)
frame_id	(877,) : 0~876

```
### DockerFile
아직 미완성
