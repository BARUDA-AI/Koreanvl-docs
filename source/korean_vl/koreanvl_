# LLaVA 코드베이스를 사용한 멀티모달 대화를 위한 InternVL

이 폴더에는 InternVL 1.0 논문의 섹션 4.4에 해당하는 InternVL-Chat V1.0의 구현이 포함되어 있습니다.

이 부분에서는 주로 LLaVA 코드베이스를 사용하여 MLLM을 생성하는 데 InternVL (InternViT-6B)을 평가합니다. 이 훌륭한 작업에 감사드립니다.
더 자세한 매뉴얼로 LLaVA-1.5의 원래 문서를 유지했습니다. 대부분의 경우 추가된 새 문서만 참조하면 됩니다.

참고: 여러 작업에서 환경을 통일하기 위해 LLaVA-1.5 코드에 몇 가지 호환성 수정을 하여 transformers==4.37.2를 지원하도록 했습니다 (원래는 4.31.0으로 고정됨). transformers==4.37.2를 설치해야 합니다.

## 설치

먼저 설치 가이드에 따라 몇 가지 기본 설치를 수행하십시오.

또한 이 코드베이스를 사용하려면 다음 단계를 실행해야 합니다.

다른 요구 사항 설치:

```bash
pip install --upgrade pip  # PEP 660 지원 활성화
pip install -e .
```

## 모델 준비
| 모델 이름 | 유형 | 매개변수 | 다운로드 | 크기 |
|---|---|---|---|---|
| InternViT-6B-224px | huggingface | 6B | 🤗 HF 링크 | 12GB |
| InternViT-6B-448px-V1-0 | huggingface | 6B | 🤗 HF 링크 | 12GB |
| vicuna-13b-v1.5 | huggingface | 13B | 🤗 HF 링크 | 13.5GB |
| vicuna-7b-v1.5 | huggingface | 7B | 🤗 HF 링크 | 26.1GB |

위의 모델 가중치를 다운로드하여 `pretrained/` 폴더에 넣으십시오.

```bash
cd pretrained/
# pip install -U huggingface_hub
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternViT-6B-224px --local-dir InternViT-6B-224px
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternViT-6B-448px-V1-0 --local-dir InternViT-6B-448px
huggingface-cli download --resume-download --local-dir-use-symlinks False lmsys/vicuna-13b-v1.5 --local-dir vicuna-13b-v1.5
huggingface-cli download --resume-download --local-dir-use-symlinks False lmsys/vicuna-7b-v1.5 --local-dir vicuna-7b-v1.5
```

디렉토리 구조는 다음과 같습니다.

```
pretrained
│── InternViT-6B-224px/
│── InternViT-6B-448px/
│── vicuna-13b-v1.5/
└── vicuna-7b-v1.5/
```

## 훈련

**InternViT-6B-224px + Vicuna-7B:**

```bash
# 사전 훈련
CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 sh scripts_internvl/pretrain_internvit6b_224to336_vicuna7b.sh
# 미세 조정
CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 sh scripts_internvl/finetune_internvit6b_224to336_vicuna7b.sh
```

**InternViT-6B-224px + Vicuna-13B:**

```bash
# 사전 훈련
CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 sh scripts_internvl/pretrain_internvit6b_224to336_vicuna13b.sh
# 미세 조정
CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 sh scripts_internvl/finetune_internvit6b_224to336_vicuna13b.sh
```

**InternViT-6B-448px + Vicuna-7B:**

```bash
# 사전 훈련
CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 sh scripts_internvl/pretrain_internvit6b_448_vicuna7b.sh
# 미세 조정
CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 sh scripts_internvl/finetune_internvit6b_448_vicuna7b.sh
```

**InternViT-6B-448px + Vicuna-13B:**

```bash
# 사전 훈련
CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 sh scripts_internvl/pretrain_internvit6b_448_vicuna13b.sh
# 미세 조정
CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 sh scripts_internvl/finetune_internvit6b_448_vicuna13b.sh
```

## 모델 주(Model Zoo)

| 방법 | 비전 인코더 | LLM | 해상도 | VQAv2 | GQA | VizWiz | SQA | TextVQA | POPE | MME | MMB | MMB<sub>CN</sub> | MMVet | 다운로드 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| LLaVA-1.5 | CLIP-L-336px | V-7B | 336 | 78.5 | 62.0 | 50.0 | 66.8 | 58.2 | 85.9 | 1510.7 | 64.3 | 58.3 | 30.5 | 🤗 HF 링크 |
| LLaVA-1.5 | CLIP-L-336px | V-13B | 336 | 80.0 | 63.3 | 53.6 | 71.6 | 61.3 | 85.9 | 1531.3 | 67.7 | 63.6 | 35.4 | 🤗 HF 링크 |
| InternVL-Chat-1.0 | IViT-6B-224px | V-7B | 336 | 79.3 | 62.9 | 52.5 | 66.2 | 57.0 | 86.4 | 1525.1 | 64.6 | 57.6 | 31.2 | 🤗 HF 링크 |
| InternVL-Chat-1.0 | IViT-6B-224px | V-13B | 336 | 80.2 | 63.9 | 54.6 | 70.1 | 58.7 | 87.1 | 1546.9 | 66.5 | 61.9 | 33.7 | 🤗 HF 링크 |
| InternVL-Chat-1.0 | IViT-6B-448px | V-13B | 448 | 82.0 | 64.1 | 60.1 | 71.6 | 64.8 | 87.2 | 1579.0 | 68.2 | 64.0 | 36.7 | 🤗 HF 링크 |

위의 모델 가중치를 다운로드하여 `pretrained/` 폴더에 넣으십시오.

```bash
cd pretrained/
# pip install -U huggingface_hub
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternVL-Chat-ViT-6B-Vicuna-7B --local-dir InternVL-Chat-ViT-6B-Vicuna-7B
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternVL-Chat-ViT-6B-Vicuna-13B --local-dir InternVL-Chat-ViT-6B-Vicuna-13B
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternVL-Chat-ViT-6B-Vicuna-13B-448px --local-dir InternVL-Chat-ViT-6B-Vicuna-13B-448px
```

디렉토리 구조는 다음과 같습니다.

```
pretrained
│── InternViT-6B-224px/
│── InternViT-6B-448px/
│── vicuna-13b-v1.5/
│── vicuna-7b-v1.5/
│── InternVL-Chat-ViT-6B-Vicuna-7B/
│── InternVL-Chat-ViT-6B-Vicuna-13B/
└── InternVL-Chat-ViT-6B-Vicuna-13B-448px/
```

## 데모

웹 데모를 배포하는 방법은 LLaVA-1.5와 동일합니다. 모델 경로만 변경하면 됩니다. 구체적인 단계는 다음과 같습니다.

**컨트롤러 시작**

```bash
python -m llava.serve.controller --host 0.0.0.0 --port 10000
```

**Gradio 웹 서버 시작**

```bash
python -m llava.serve.gradio_web_server --controller http://localhost:10000 --model-list-mode reload --port 10038
```

**모델 워커 시작**

```bash
# OpenGVLab/InternVL-Chat-ViT-6B-Vicuna-7B
python -m llava.serve.model_worker --host 0.0.0.0 --controller http://localhost:10000 --port 40000 --worker http://localhost:40000 --model-path ./pretrained/InternVL-Chat-ViT-6B-Vicuna-7B
# OpenGVLab/InternVL-Chat-ViT-6B-Vicuna-13B
python -m llava.serve.model_worker --host 0.0.0.0 --controller http://localhost:10000 --port 40001 --worker http://localhost:40001 --model-path ./pretrained/InternVL-Chat-ViT-6B-Vicuna-13B
```

위 단계를 완료한 후 http://localhost:10038 에서 웹 데모에 액세스하면 다음 페이지를 볼 수 있습니다. 여기에 배포된 모델은 InternVL 1.0의 두 모델인 InternVL-Chat-ViT-6B-Vicuna-7B와 InternVL-Chat-ViT-6B-Vicuna-13B입니다. LLaVA-1.5와의 유일한 차이점은 CLIP-ViT-300M이 InternViT-6B로 대체되었다는 것입니다.

더 효과적인 MLLM이 필요하면 InternVL2 시리즈 모델을 확인하십시오.
데모 배포에 대한 자세한 내용은 여기를 참조하십시오.

![alt text](./llava_webui.png)

## 테스트

모델을 테스트하는 방법은 LLaVA-1.5와 동일합니다. 스크립트의 경로만 변경하면 됩니다. 스크립트는 `scripts_internvl/`에 있습니다.

예를 들어 단일 GPU를 사용하여 MME를 테스트하려면

```bash
sh scripts_internvl/eval/mme.sh pretrained/InternVL-Chat-ViT-6B-Vicuna-7B/
```

## 인용

이 프로젝트가 연구에 유용하다고 생각되면 다음을 인용하십시오.

```
@article{chen2023internvl,
  title={InternVL: Scaling up Vision Foundation Models and Aligning for Generic Visual-Linguistic Tasks},
  author={Chen, Zhe and Wu, Jiannan and Wang, Wenhai and Su, Weijie and Chen, Guo and Xing, Sen and Zhong, Muyan and Zhang, Qinglong and Zhu, Xizhou and Lu, Lewei and Li, Bin and Luo, Ping and Lu, Tong and Qiao, Yu and Dai, Jifeng},
  journal={arXiv preprint arXiv:2312.14238},
  year={2023}
}
@article{chen2024far,
  title={How Far Are We to GPT-4V? Closing the Gap to Commercial Multimodal Models with Open-Source Suites},
  author={Chen, Zhe and Wang, Weiyun and Tian, Hao and Ye, Shenglong and Gao, Zhangwei and Cui, Erfei and Tong, Wenwen and Hu, Kongzhi and Luo, Jiapeng and Ma, Zheng and others},
  journal={arXiv preprint arXiv:2404.16821},
  year={2024}
}
```

## 🌋 LLaVA: 대규모 언어 및 비전 어시스턴트

GPT-4 수준의 기능을 갖춘 대규모 언어 및 비전 모델을 향한 시각적 지침 튜닝.

[프로젝트 페이지] [데모]  [데이터] [모델 주]

🤝커뮤니티 기여: [llama.cpp] [Colab] [🤗Space]

시각적 지침 튜닝을 통한 개선된 기준 [논문] <br>
Haotian Liu, Chunyuan Li, Yuheng Li, Yong Jae Lee

시각적 지침 튜닝 (NeurIPS 2023, Oral) [논문]<br>
Haotian Liu*, Chunyuan Li*, Qingyang Wu, Yong Jae Lee (*공동 기여)

## 릴리즈

[10/12] 🔥 저희 연구를 아낌없이 지원해주신 ETRI에서 제작한 한국어 LLaVA(Ko-LLaVA)를 확인하세요! [🤗 데모]

[10/12] LLaVA는 이제 4비트/5비트 양자화 지원을 통해 llama.cpp에서 지원됩니다!

[10/11] LLaVA-1.5의 훈련 데이터와 스크립트는 여기에 릴리즈되었으며 평가 스크립트는 여기에 릴리즈되었습니다!

[10/5] 🔥 LLaVA-1.5가 출시되었습니다! 원래 LLaVA에 간단한 수정만으로 11개 벤치마크에서 SoTA를 달성하고, 모든 공개 데이터를 활용하고, 단일 8-A100 노드에서 ~1일 만에 훈련을 완료하고, 수십억 규모의 데이터를 사용하는 Qwen-VL-Chat과 같은 방법을 능가합니다. 기술 보고서를 확인하고 데모를 살펴보세요! 모델은 모델 주에서 사용할 수 있습니다.

[9/26] LLaVA는 사실 기반을 개선하고 환각을 줄이기 위해 인간 피드백의 강화 학습(RLHF)으로 개선되었습니다. 프로젝트 [LLavA-RLHF]에서 새로운 SFT 및 RLHF 체크포인트를 확인하세요.

[9/22] LLaVA는 NeurIPS 2023에서 구두 발표로, LLaVA-Med는 NeurIPS 2023 데이터 세트 및 벤치마크 트랙에서 스포트라이트 발표로 채택되었습니다.

[9/20] 33B 및 65B LLaVA 모델 훈련에 대한 실증적 연구를 노트에 요약합니다. 또한 다중 모드 기반 모델의 포괄적인 검토, 진화 및 추세에 관심이 있는 경우 최근 설문 조사 논문 "다중 모드 기반 모델: 전문가에서 범용 보조자로"를 확인하십시오.

<p align="center">
<img src="https://github.com/Computer-Vision-in-the-Wild/CVinW_Readings/blob/main/images/mfm_evolution.jpeg?raw=true" width=50%/>
</p>

[7/19] 🔥 LLaMA-2, LoRA 훈련, 4/8비트 추론, 고해상도(336x336) 등에 대한 지원을 포함한 주요 업그레이드를 출시합니다. Bard 및 Bing-Chat의 결과와 함께 개방형 시각 채팅을 벤치마킹하기 위한 LLaVA 벤치를 출시합니다. 또한 RTX 3090 및 RTX A6000을 사용한 훈련을 지원하고 검증합니다. LLaVA-from-LLaMA-2 및 모델 주를 확인하십시오!

[6/26] 대규모 다중 모드 모델에 대한 CVPR 2023 튜토리얼: 다중 모드 GPT-4 구축 및 능가하기! [슬라이드] [노트] [YouTube] [Bilibli]를 확인하십시오.

[6/11] 가장 많이 요청된 기능에 대한 미리 보기인 DeepSpeed 및 LoRA 지원을 출시했습니다! 자세한 내용은 여기 문서를 참조하십시오.

[6/1] GPT-4 수준의 기능을 갖춘 생물 의학 영역 대규모 언어 및 비전 모델을 구축하기 위한 단계인 LLaVA-Med: 생물 의학을 위한 대규모 언어 및 비전 보조자를 출시했습니다. 논문과 페이지를 확인하십시오.

[5/6] MPT-7B-Chat을 기반으로 한 LLaVA-Lighting-MPT-7B-preview를 출시합니다! 자세한 내용은 여기를 참조하십시오.

[5/2] 🔥 단 3시간 만에 40달러로 라이트한 다중 모드 GPT-4를 훈련하는 LLaVA-Lighting을 출시합니다! 자세한 내용은 여기를 참조하십시오.

[4/27] 커뮤니티 노력 덕분에 4비트 양자화를 사용한 LLaVA-13B를 사용하면 12GB VRAM만 있는 GPU에서 실행할 수 있습니다! 여기서 시도해 보십시오.

[4/17] 🔥 GPT-4 수준의 기능을 갖춘 대규모 언어 및 비전 모델을 구축하기 위한 시각적 지침 튜닝인 LLaVA: 대규모 언어 및 비전 보조자를 출시했습니다. 논문과 데모를 확인하십시오.

<!-- <a href="https://llava.hliu.cc/"><img src="assets/demo.gif" width="70%"></a> -->

![alt text](https://img.shields.io/badge/Code%20License-Apache_2.0-green.svg)

![alt text](https://img.shields.io/badge/Data%20License-CC%20By%20NC%204.0-red.svg)

사용 및 라이선스 고지: 데이터와 체크포인트는 연구용으로만 사용하도록 허가되었습니다. 또한 LLaMA, Vicuna 및 GPT-4의 라이선스 계약을 따르는 용도로 제한됩니다. 데이터 세트는 CC BY NC 4.0(비상업적 용도로만 허용됨)이며 데이터 세트를 사용하여 훈련된 모델은 연구 목적 이외의 용도로 사용해서는 안 됩니다.

## 목차

- 설치
- LLaVA 가중치
- 데모
- 모델 주
- 데이터 세트
- 훈련
- 평가

## 설치

이 저장소를 복제하고 LLaVA 폴더로 이동합니다.

```bash
git clone https://github.com/haotian-liu/LLaVA.git
cd LLaVA
```

패키지 설치

```bash
conda create -n llava python=3.10 -y
conda activate llava
pip install --upgrade pip  # PEP 660 지원 활성화
pip install -e .
```

훈련 사례에 대한 추가 패키지 설치

```bash
pip install ninja
pip install flash-attn --no-build-isolation
```

최신 코드 베이스로 업그레이드
```bash
git pull
pip uninstall transformers
pip install -e .
```

## LLaVA 가중치

모든 공개 LLaVA 체크포인트와 가중치 사용 방법에 대한 지침은 모델 주를 확인하십시오.

## 데모

데모를 실행하려면 LLaVA 체크포인트를 로컬로 준비해야 합니다. 여기에 있는 지침에 따라 체크포인트를 다운로드하십시오.

### Gradio 웹 UI

Gradio 데모를 로컬로 시작하려면 다음 명령을 하나씩 실행하십시오. 여러 모델 작업자를 시작하여 다른 체크포인트 간을 비교하려는 경우 컨트롤러와 웹 서버를 한 번만 시작하면 됩니다.

```bash
python -m llava.serve.controller --host 0.0.0.0 --port 10000
```

```bash
python -m llava.serve.gradio_web_server --controller http://localhost:10000 --model-list-mode reload
```

이제 Gradio 웹 인터페이스를 시작했습니다. 이제 화면에 인쇄된 URL로 웹 인터페이스를 열 수 있습니다. 모델 목록에 모델이 없는 것을 알 수 있습니다. 걱정하지 마십시오. 아직 모델 작업자를 시작하지 않았기 때문입니다. 모델 작업자를 시작하면 자동으로 업데이트됩니다.

이것은 GPU에서 추론을 수행하는 실제 작업자입니다. 각 작업자는 --model-path에 지정된 단일 모델을 담당합니다.

```bash
python -m llava.serve.model_worker --host 0.0.0.0 --controller http://localhost:10000 --port 40000 --worker http://localhost:40000 --model-path liuhaotian/llava-v1.5-13b
```

프로세스가 모델 로드를 완료하고 "Uvicorn running on ..."이 표시될 때까지 기다립니다. 이제 Gradio 웹 UI를 새로 고치면 방금 시작한 모델이 모델 목록에 표시됩니다.

원하는 만큼 작업자를 시작하고 동일한 Gradio 인터페이스에서 다른 모델 체크포인트 간을 비교할 수 있습니다. --controller는 동일하게 유지하고 각 작업자에 대해 --port 및 --worker를 다른 포트 번호로 수정하십시오.

```bash
python -m llava.serve.model_worker --host 0.0.0.0 --controller http://localhost:10000 --port <40000과 다름, 예를 들어 40001> --worker http://localhost:<이에 따라 변경, 예: 40001> --model-path <ckpt2>
```

M1 또는 M2 칩이 장착된 Apple 장치를 사용하는 경우 --device 플래그: --device mps를 사용하여 mps 장치를 지정할 수 있습니다.

GPU의 VRAM이 24GB 미만(예: RTX 3090, RTX 4090 등)인 경우 여러 GPU로 실행해 볼 수 있습니다. 최신 코드 베이스는 GPU가 두 개 이상 있는 경우 자동으로 여러 GPU를 사용하려고 시도합니다. CUDA_VISIBLE_DEVICES로 사용할 GPU를 지정할 수 있습니다. 다음은 처음 두 GPU로 실행하는 예입니다.

```bash
CUDA_VISIBLE_DEVICES=0,1 python -m llava.serve.model_worker --host 0.0.0.0 --controller http://localhost:10000 --port 40000 --worker http://localhost:40000 --model-path liuhaotian/llava-v1.5-13b
```

양자화된 비트(4비트, 8비트)로 모델 작업자를 시작할 수 있으며, 이를 통해 GPU 메모리 사용 공간을 줄여 추론을 실행할 수 있으므로 12GB VRAM만 있는 GPU에서도 실행할 수 있습니다. 양자화된 비트를 사용한 추론은 전체 정밀도 모델만큼 정확하지 않을 수 있습니다. 실행 중인 모델 작업자 명령에 --load-4bit 또는 --load-8bit를 추가하기만 하면 됩니다. 다음은 4비트 양자화로 실행하는 예입니다.

```bash
python -m llava.serve.model_worker --host 0.0.0.0 --controller http://localhost:10000 --port 40000 --worker http://localhost:40000 --model-path liuhaotian/llava-v1.5-13b --load-4bit
```

디스크 공간을 절약하기 위해 기본 체크포인트와 병합하지 않고 LoRA 가중치로 모델 작업자를 시작할 수 있습니다. 추가 로드 시간이 있지만 추론 속도는 병합된 체크포인트와 동일합니다. 병합되지 않은 LoRA 체크포인트는 모델 이름에 lora-merge가 없으며 일반적으로 병합된 체크포인트(7B의 경우 13G, 13B의 경우 25G)보다 훨씬 작습니다(1GB 미만).

병합되지 않은 LoRA 가중치를 로드하려면 LoRA 가중치를 훈련하는 데 사용되는 기본 LLM인 --model-base라는 추가 인수를 전달하기만 하면 됩니다. 각 LoRA 가중치의 기본 LLM은 모델 주에서 확인할 수 있습니다.

```bash
python -m llava.serve.model_worker --host 0.0.0.0 --controller http://localhost:10000 --port 40000 --worker http://localhost:40000 --model-path liuhaotian/llava-v1-0719-336px-lora-vicuna-13b-v1.3 --model-base lmsys/vicuna-13b-v1.3
```

### CLI 추론

Gradio 인터페이스 없이 LLaVA를 사용하여 이미지에 대해 채팅합니다. 또한 여러 GPU, 4비트 및 8비트 양자화 추론을 지원합니다. 4비트 양자화를 사용하면 LLaVA-1.5-7B의 경우 단일 GPU에서 8GB 미만의 VRAM을 사용합니다.

```bash
python -m llava.serve.cli \
    --model-path liuhaotian/llava-v1.5-7b \
    --image-file "https://llava-vl.github.io/static/images/view.jpg" \
    --load-4bit
```

## 훈련

다음은 LLaVA v1.5에 대한 최신 훈련 구성입니다. 레거시 모델의 경우 현재 버전의 README를 참조하십시오. 나중에 별도의 문서에 추가할 예정입니다.

LLaVA 훈련은 두 단계로 구성됩니다. (1) 기능 정렬 단계: LAION-CC-SBU 데이터 세트의 558K 하위 집합을 사용하여 고정된 사전 훈련된 비전 인코더를 고정된 LLM에 연결합니다. (2) 시각적 지침 튜닝 단계: 150K GPT 생성 다중 모드 지침 준수 데이터와 학술 지향 작업의 약 515K VQA 데이터를 사용하여 모델이 다중 모드 지침을 따르도록 훈련합니다.

LLaVA는 80GB 메모리가 있는 8개의 A100 GPU에서 훈련됩니다. 더 적은 GPU에서 훈련하려면 per_device_train_batch_size를 줄이고 gradient_accumulation_steps를 그에 따라 늘릴 수 있습니다. 항상 글로벌 배치 크기를 동일하게 유지하십시오. per_device_train_batch_size x gradient_accumulation_steps x num_gpus.

### 하이퍼파라미터

미세 조정에서 Vicuna와 유사한 하이퍼파라미터 집합을 사용합니다. 사전 훈련과 미세 조정에 사용되는 하이퍼파라미터는 아래에 제공됩니다.

#### 사전 훈련

| 하이퍼파라미터 | 글로벌 배치 크기 | 학습률 | 에포크 | 최대 길이 | 가중치 감쇠 |
|---|---|---|---|---|---|
| LLaVA-v1.5-13B | 256 | 1e-3 | 1 | 2048 | 0 |

#### 미세 조정

| 하이퍼파라미터 | 글로벌 배치 크기 | 학습률 | 에포크 | 최대 길이 | 가중치 감쇠 |
|---|---|---|---|---|---|
| LLaVA-v1.5-13B | 128 | 2e-5 | 1 | 2048 | 0 |

### Vicuna 체크포인트 다운로드(자동)

지침 튜닝된 챗봇인 기본 모델 Vicuna v1.5는 제공된 훈련 스크립트를 실행할 때 자동으로 다운로드됩니다. 조치가 필요하지 않습니다.

### 사전 훈련(기능 정렬)

여기에 있는 논문에서 사용하는 BLIP 캡션이 있는 LAION-CC-SBU 데이터 세트의 558K 하위 집합을 다운로드하십시오.

사전 훈련은 해상도가 336px로 증가했기 때문에 8x A100(80G)에서 LLaVA-v1.5-13B의 경우 약 5.5시간이 소요됩니다. LLaVA-v1.5-7B의 경우 약 3.5시간이 소요됩니다.

DeepSpeed ZeRO-2를 사용한 훈련 스크립트: pretrain.sh.

- --mm_projector_type mlp2x_gelu: 2계층 MLP 비전-언어 커넥터.
- --vision_tower openai/clip-vit-large-patch14-336: CLIP ViT-L/14 336px.

### 시각적 지침 튜닝

#### 데이터 준비

최종 혼합 지침 튜닝 데이터 llava_v1_5_mix665k.json의 주석을 다운로드하고 구성 데이터 세트에서 이미지를 다운로드하십시오.

- COCO: train2017
- GQA: images
- OCR-VQA: download script
- TextVQA: train_val_images
- VisualGenome: part1, part2

모두 다운로드한 후 ./playground/data에 다음과 같이 데이터를 구성하십시오.

```
├── coco
│   └── train2017
├── gqa
│   └── images
├── ocr_vqa
│   └── images
├── textvqa
│   └── train_images
└── vg
    ├── VG_100K
    └── VG_100K_2
```

#### 훈련 시작!

모델 주에서 사전 훈련된 프로젝터를 다운로드할 수 있습니다. 레거시 프로젝터를 사용하는 것은 권장되지 않습니다. 코드 베이스의 다른 버전으로 훈련되었을 수 있으며 옵션이 꺼져 있으면 모델이 예상대로 작동/훈련되지 않기 때문입니다.

시각적 지침 튜닝은 해상도가 336px로 증가했기 때문에 8x A100(80G)에서 LLaVA-v1.5-13B의 경우 약 20시간이 소요됩니다. 8x A100(40G)에서 LLaVA-v1.5-7B의 경우 약 10시간이 소요됩니다.

DeepSpeed ZeRO-3를 사용한 훈련 스크립트: finetune.sh.

참고할 새로운 옵션:

- --mm_projector_type mlp2x_gelu: 2계층 MLP 비전-언어 커넥터.
- --vision_tower openai/clip-vit-large-patch14-336: CLIP ViT-L/14 336px.
- --image_aspect_ratio pad: 정사각형이 아닌 이미지를 자르는 대신 정사각형으로 채웁니다. 환각을 약간 줄입니다.
- --group_by_modality_length True: 지침 튜닝 데이터 세트에 언어(예: ShareGPT)와 다중 모드(예: LLaVA-Instruct)가 모두 포함된 경우에만 사용해야 합니다. 훈련 샘플러가 훈련 중에 단일 양식(이미지 또는 언어)만 샘플링하도록 하여 훈련 속도를 ~25% 높이고 최종 결과에 영향을 미치지 않습니다.

## 평가

LLaVA-1.5에서는 12개의 다양한 벤치마크에서 모델을 평가합니다. 재현성을 보장하기 위해 탐욕적 디코딩으로 모델을 평가합니다. 실시간 출력의 채팅 데모와 추론 프로세스를 일관되게 유지하기 위해 빔 검색을 사용하여 평가하지 않습니다.

Evaluation.md를 참조하십시오.

### GPT 지원 평가

다중 모드 모델링을 위한 GPT 지원 평가 파이프라인은 비전-언어 모델의 기능에 대한 포괄적인 이해를 위해 제공됩니다. 자세한 내용은 논문을 참조하십시오.

#### LLaVA 응답 생성

```bash
python model_vqa.py \
    --model-path ./checkpoints/LLaVA-13B-v0 \
    --question-file \
    playground/data/coco2014_val_qa_eval/qa90_questions.jsonl \
    --image-folder \
    /path/to/coco2014_val \
    --answers-file \
    /path/to/answer-file-our.jsonl
```

생성된 응답을 평가합니다. 이 경우 answer-file-ref.jsonl은 컨텍스트 캡션/상자가 제공된 텍스트 전용 GPT-4(0314)에서 생성된 응답입니다.

```bash
OPENAI_API_KEY="sk-***********************************" python llava/eval/eval_gpt_review_visual.py \
    --question playground/data/coco2014_val_qa_eval/qa90_questions.jsonl \
    --context llava/eval/table/caps_boxes_coco2014_val_80.jsonl \
    --answer-list \
    /path/to/answer-file-ref.jsonl \
    /path/to/answer-file-our.jsonl \
    --rule llava/eval/table/rule.json \
    --output /path/to/review.json
```

평가 결과 요약

```bash
python summarize_gpt_review.py
```

## 인용

LLaVA가 연구 및 응용 프로그램에 유용하다고 생각되면 다음 BibTeX를 사용하여 인용하십시오.

```
@misc{liu2023improvedllava,
      title={Improved Baselines with Visual Instruction Tuning},
      author={Liu, Haotian and Li, Chunyuan and Li, Yuheng and Lee, Yong Jae},
      publisher={arXiv:2310.03744},
      year={2023},
}

@misc{liu2023llava,
      title={Visual Instruction Tuning},
      author={Liu, Haotian and Li, Chunyuan and Wu, Qingyang and Lee, Yong Jae},
      publisher={arXiv:2304.08485},
      year={2023},
}
```

## 감사의 말

- Vicuna: 우리가 기반으로 한 코드베이스이자 놀라운 언어 기능을 갖춘 기본 모델 Vicuna-13B!

### Related Projects

- [Instruction Tuning with GPT-4](https://github.com/Instruction-Tuning-with-GPT-4/GPT-4-LLM)
- [LLaVA-Med: Training a Large Language-and-Vision Assistant for Biomedicine in One Day](https://github.com/microsoft/LLaVA-Med)
- [Otter: In-Context Multi-Modal Instruction Tuning](https://github.com/Luodian/Otter)

For future project ideas, please check out:

- [SEEM: Segment Everything Everywhere All at Once](https://github.com/UX-Decoder/Segment-Everything-Everywhere-All-At-Once)
- [Grounded-Segment-Anything](https://github.com/IDEA-Research/Grounded-Segment-Anything) to detect, segment, and generate anything by marrying [Grounding DINO](https://github.com/IDEA-Research/GroundingDINO) and [Segment-Anything](https://github.com/facebookresearch/segment-anything).

<br>
<br>
