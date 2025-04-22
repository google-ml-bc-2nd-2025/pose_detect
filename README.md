# pose_detect

1. dataset 폴더에 SMPL Neutral, Male, Female 저장
2. `fetch_demo_dataset.sh` 파일 실행해서 체크포인트 다운받기
3. GPU CUDA 12.x : pip install torch torchvision torchaudio && (`pip install -r requirements.txt` or `cat requirements.txt | xargs -n 1 pip install`)
4. `python demo.py --video tiktok_video.mp4 --run_smplify --save_pkl`
5. output폴더에 npz 파일 : SMPL 파라미터: ['pose', 'trans', 'betas', 'frame_ids']
6. 최종 shape 및 type (N: 프레임수)
'pose':
shape: (N, 72)
type: numpy.ndarray
내용: 각 프레임의 SMPL axis-angle 포즈 파라미터 (root + body)

'trans':
shape: (N, 3)
type: numpy.ndarray
내용: 각 프레임의 3D translation (카메라 좌표계)
'betas':
shape: (N, 10)
type: numpy.ndarray
내용: SMPL shape 파라미터 (시퀀스 전체에 대해 동일)
