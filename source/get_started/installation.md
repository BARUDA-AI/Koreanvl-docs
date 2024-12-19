## 설치

### 1. 레포지토리 복제

```bash
git clone https://github.com/OpenGVLab/InternVL.git
```

위 명령어를 실행하여 InternVL 레포지토리를 복제합니다.


### 2. 가상 환경 생성 및 활성화

```bash
conda create -n internvl python=3.9 -y
conda activate internvl
```

위 명령어로 가상 환경 `internvl`을 만들고 활성화합니다. 파이썬 버전은 3.9를 사용합니다.


### 3. 의존 라이브러리 설치

```bash
pip install -r requirements.txt
```

`requirements.txt` 파일에 명시된 의존 라이브러리를 설치합니다. 기본적으로 `requirements.txt` 파일은 다음과 같은 하위 의존성을 포함합니다.

* `requirements/internvl_chat.txt`
* `requirements/streamlit_demo.txt`
* `requirements/classification.txt`
* `requirements/segmentation.txt`

만약 텍스트 분류 또는 검색 평가를 위한 `clip_benchmark` 기능이 필요하다면 별도로 설치해야 합니다.

```bash
pip install -r requirements/clip_benchmark.txt
```


### 추가 설치 (선택사항)

- `flash-attn==2.3.6` 설치

```bash
pip install flash-attn==2.3.6 --no-build-isolation
```

또는 소스 코드에서 빌드할 수도 있습니다.

```bash
git clone https://github.com/Dao-AILab/flash-attention.git
cd flash-attention
git checkout v2.3.6
python setup.py install
```

- `mmcv-full==1.6.2` 설치 (분할 작업 선택사항)

```bash
pip install -U openmim
mim install mmcv-full==1.6.2
```

- `apex` 설치 (분할 작업 선택사항)

```bash
git clone https://github.com/NVIDIA/apex.git
git checkout 2386a912164b0c5cfcd8be7a2b890fbac5607c82  # https://github.com/NVIDIA/apex/issues/1735
pip install -v --disable-pip-version-check --no-cache-dir --no-build-isolation --config-settings "--build-option=--cpp_ext" --config-settings "--build-option=--cuda_ext" ./
```

만약 `ModuleNotFoundError: No module named 'fused_layer_norm_cuda'` 오류가 발생한다면 apex의 CUDA 확장 프로그램이 성공적으로 설치되지 않은 것입니다. apex를 제거하면 코드는 기본 PyTorch 버전의 RMSNorm을 사용합니다. 

**참고 이미지:** [https://github.com/OpenGVLab/InternVL/assets/23737120/c04a989c-8024-49fa-b62c-2da623e63729](https://github.com/OpenGVLab/InternVL/assets/23737120/c04a989c-8024-49fa-b62c-2da623e63729) width=70%


위와 같은 과정을 통해 InternVL을 설치할 수 있습니다. 
