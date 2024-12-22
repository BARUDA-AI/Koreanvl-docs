# LoRA 미세 조정을 사용하여 COCO 캡션에서 InternVL2 성능 향상하기

이 튜토리얼에서는 LoRA 미세 조정을 사용하여 COCO 캡션에서 학습된 InternVL2 모델의 성능을 향상시키는 방법에 대한 자세한 가이드를 제공합니다.

## 준비

시작하기 전에 [설치 가이드](../get_started/installation.md)에 따라 InternVL 학습 환경을 준비하십시오. [Flash Attention](http://localhost:8000/get_started/installation.html#additional-instructions)은 제공된 단계에 따라 수동으로 설치해야 합니다. 문제가 발생하면 [공식 저장소](https://github.com/Dao-AILab/flash-attention/issues)의 이슈 섹션을 확인하십시오.

선택적으로 효율성 향상을 위해 `apex`를 설치할 수 있습니다. Apex는 PyTorch의 `RMSNorm`을 대체하기 위해 `FusedRMSNorm`을 사용하며, 이는 학습 속도를 높이고 메모리를 절약할 수 있습니다. 그러나 apex 설치는 어려울 수 있으므로 선택 사항입니다.

## 모델 준비

환경을 설정한 후 `internvl_chat` 디렉토리로 이동합니다. 사전 학습된 InternVL2 모델을 다운로드해야 합니다. 아래 표는 InternVL2 시리즈에서 사용 가능한 모든 모델을 나열합니다.

| 모델 이름           | 유형 | 매개변수 | 다운로드 링크                                                       |  크기  |
| -------------------- | ---- | ------ | ------------------------------------------------------------------- | :----: |
| InternVL2-1B         | MLLM | 0.9B   | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-1B)         | 1.8 GB |
| InternVL2-2B         | MLLM | 2.2B   | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-2B)         | 4.2 GB |
| InternVL2-4B         | MLLM | 4.2B   | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-4B)         | 7.8 GB |
| InternVL2-8B         | MLLM | 8.1B   | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-8B)         | 16 GB  |
| InternVL2-26B        | MLLM | 25.5B  | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-26B)        | 48 GB  |
| InternVL2-40B        | MLLM | 40.1B  | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-40B)        | 75 GB  |
| InternVL2-Llama3-76B | MLLM | 76.3B  | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-Llama3-76B) | 143 GB |

다음은 `huggingface_hub`를 사용하여 이러한 모델을 다운로드하는 명령입니다. 필요에 맞는 모델을 선택하십시오.

```sh
cd pretrained/
# pip install -U huggingface_hub
# OpenGVLab/InternVL2-1B 다운로드
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternVL2-1B --local-dir InternVL2-1B
# OpenGVLab/InternVL2-2B 다운로드
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternVL2-2B --local-dir InternVL2-2B
# OpenGVLab/InternVL2-4B 다운로드
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternVL2-4B --local-dir InternVL2-4B
# OpenGVLab/InternVL2-8B 다운로드
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternVL2-8B --local-dir InternVL2-8B
# OpenGVLab/InternVL2-26B 다운로드
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternVL2-26B --local-dir InternVL2-26B
# OpenGVLab/InternVL2-40B 다운로드
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternVL2-40B --local-dir InternVL2-40B
# OpenGVLab/InternVL2-Llama3-76B 다운로드
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternVL2-Llama3-76B --local-dir InternVL2-Llama3-76B
```

이 튜토리얼에서는 InternVL2-2B 모델을 다운로드합니다. `pretrained` 디렉토리로 이동하여 위의 두 번째 명령을 실행합니다. 다운로드 후 디렉토리 구조는 다음과 같아야 합니다.

```sh
pretrained
└── InternVL2-2B
```

## 데이터 준비

COCO 캡션에서 InternVL2 모델을 향상시키려면 학습 및 테스트를 위해 COCO 캡션 데이터 세트를 준비해야 합니다.

COCO 캡션 데이터를 준비하려면 다음 지침을 따르십시오.

```shell
mkdir -p data/coco && cd data/coco

# COCO 이미지 다운로드
wget http://images.cocodataset.org/zips/train2014.zip && unzip train2014.zip
wget http://images.cocodataset.org/zips/val2014.zip && unzip val2014.zip
wget http://images.cocodataset.org/zips/test2015.zip && unzip test2015.zip

mkdir -p annotations && cd annotations/
# 변환된 주석 파일 다운로드
wget https://github.com/OpenGVLab/InternVL/releases/download/data/coco_karpathy_test.json
wget https://github.com/OpenGVLab/InternVL/releases/download/data/coco_karpathy_test_gt.json
wget https://github.com/OpenGVLab/InternVL/releases/download/data/coco_karpathy_train_567k.zip
unzip coco_karpathy_train_567k.zip
cd ../../../
```

다운로드 후 디렉토리 구조는 다음과 같습니다.

```
data/coco
├── annotations
│   ├── coco_karpathy_test.json
│   ├── coco_karpathy_test_gt.json
│   └── coco_karpathy_train_567k.jsonl
├── train2014
├── val2014
└── test2015
```

## 원본 모델 평가

데이터와 모델이 준비되었으므로 COCO 캡션에서 InternVL2-2B 모델을 평가할 수 있습니다.

다음 명령을 사용하여 4개의 GPU로 모델을 테스트합니다. 설정에 따라 GPU 수를 조정하십시오.

```shell
GPUS=4 sh evaluate.sh pretrained/InternVL2-2B caption-coco --dynamic
```

InternVL2의 SFT 데이터에는 COCO 캡션이 포함되어 있지 않기 때문에 초기 평가 결과가 낮을 수 있습니다. 예상되는 결과는 다음과 같습니다.

```
Bleu_1: 0.640
Bleu_2: 0.463
Bleu_3: 0.320
Bleu_4: 0.214
computing METEOR score...
METEOR: 0.267
computing Rouge score...
ROUGE_L: 0.504
computing CIDEr score...
CIDEr: 0.793
```

## LoRA 미세 조정

다음으로 LoRA를 사용하여 InternVL2-2B 모델을 미세 조정합니다. 미세 조정을 위해 다음 명령을 실행하십시오.

```shell
GPUS=8 PER_DEVICE_BATCH_SIZE=4 sh shell/internvl2.0/2nd_finetune/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_lora_coco.sh
```

[이 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_lora_coco.sh)에서 LoRA 순위를 128로 설정합니다. 즉, InternLM2-Chat-1.8B 매개변수의 6.24%가 학습 가능하며 총 1억 2,580만 개의 매개변수가 있습니다.

```shell
trainable params: 125,829,120 || all params: 2,014,976,000 || trainable%: 6.244695718460171
```

총 배치 크기는 512로 설정되고 장치당 배치 크기는 4로 설정되어 약 32G의 메모리를 소비합니다. COCO 캡션 데이터 세트(566k 항목)에서 한 에포크를 학습하는 데 약 1100번의 반복이 필요합니다.

```shell
[INFO|trainer.py:1721] 2024-07-31 22:44:12,436 >> ***** Running training *****
[INFO|trainer.py:1722] 2024-07-31 22:44:12,436 >>   Num examples = 566,747
[INFO|trainer.py:1723] 2024-07-31 22:44:12,436 >>   Num Epochs = 1
[INFO|trainer.py:1724] 2024-07-31 22:44:12,436 >>   Instantaneous batch size per device = 4
[INFO|trainer.py:1727] 2024-07-31 22:44:12,436 >>   Total train batch size (w. parallel, distributed & accumulation) = 512
[INFO|trainer.py:1728] 2024-07-31 22:44:12,436 >>   Gradient Accumulation steps = 16
[INFO|trainer.py:1729] 2024-07-31 22:44:12,436 >>   Total optimization steps = 1,106
[INFO|trainer.py:1730] 2024-07-31 22:44:12,440 >>   Number of trainable parameters = 125,829,120
```

8개의 A100 GPU로 학습하는 데 약 4시간이 걸립니다. 메모리 문제가 발생하면 `PER_DEVICE_BATCH_SIZE`를 2 또는 1로 줄여 보십시오.

여기에 제공된 하이퍼파라미터는 임의적이며 최적이 아닐 수 있습니다. 매개변수를 조정하여 더 나은 성능을 얻을 수 있습니다.

## TensorBoard로 모니터링

학습을 시작한 후 다음 디렉토리로 이동합니다.

```shell
cd work_dirs/internvl_chat_v2_0/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_lora_coco
```

다음 명령으로 TensorBoard를 시작합니다.

```shell
tensorboard --logdir ./ --port 10097 --host 0.0.0.0
```

그런 다음 웹 브라우저를 열고 `http://localhost:10097/`로 이동하여 학습 손실 곡선 및 기타 메트릭을 봅니다.

![tensorboard](./tensorboard.png)

## 미세 조정된 모델 평가

미세 조정 후 다음 명령을 사용하여 4개의 GPU로 COCO 캡션에서 모델을 다시 평가합니다. 설정에 따라 GPU 수를 조정하십시오.

```shell
GPUS=4 sh evaluate.sh work_dirs/internvl_chat_v2_0/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_lora_coco caption-coco --dynamic
```

미세 조정된 모델은 COCO 캡션 평가 메트릭에서 상당한 개선을 보여야 합니다.

```
Bleu_1: 0.805
Bleu_2: 0.649
Bleu_3: 0.504
Bleu_4: 0.385
computing METEOR score...
METEOR: 0.300
computing Rouge score...
ROUGE_L: 0.595
computing CIDEr score...
CIDEr: 1.312
```

## LoRA 가중치 병합

미세 조정된 모델을 평가한 후 LoRA 가중치를 원래 InternVL2 모델에 다시 병합할 수 있습니다. 이를 수행하려면 다음 단계를 따르십시오.

제공된 스크립트를 사용하여 LoRA 가중치를 기본 모델에 병합합니다. 스크립트는 미세 조정된 모델의 입력 경로와 병합된 모델의 출력 경로라는 두 가지 인수를 사용합니다.

```shell
python tools/merge_lora.py <input_path> <output_path>
```

예:

```shell
python tools/merge_lora.py work_dirs/internvl_chat_v2_0/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_lora_coco/ work_dirs/internvl_chat_v2_0/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_lora_coco_merge
```

스크립트는 다음을 출력합니다.

```shell
Loading model...
trainable params: 125,829,120 || all params: 2,014,976,000 || trainable%: 6.244695718460171
Loading tokenizer...
Saving model...
Saving tokenizer...
Done!
```

## AutoModel로 래핑

LoRA 가중치를 병합한 후 미세 조정된 InternVL2 모델을 AutoModel로 래핑하여 추론 또는 배포를 더 쉽게 할 수 있습니다.

먼저 원래 InternVL2-2B 디렉토리의 모든 Python 스크립트를 새 병합된 모델 디렉토리에 복사합니다.

```shell
cp pretrained/InternVL2-2B/*.py work_dirs/internvl_chat_v2_0/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_lora_coco_merge
```

다음으로 `config.json` 파일을 원래 InternVL2-2B 디렉토리에서 새 병합된 모델 디렉토리로 복사합니다.

```shell
cp pretrained/InternVL2-2B/config.json work_dirs/internvl_chat_v2_0/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_lora_coco_merge
```

필요한 파일을 복사한 후 이제 AutoModel을 사용하여 미세 조정된 InternVL2 모델을 로드하고 추론 또는 배포에 사용할 수 있습니다.

## 결론

이 가이드는 LoRA 미세 조정을 사용하여 COCO 캡션에서 InternVL2 모델을 향상시키는 단계별 접근 방식을 제공했습니다. 이 지침을 따르면 캡션 작업에서 향상된 성능을 얻을 수 있습니다. COCO 캡션은 하나의 예일 뿐입니다. 미세 조정을 위해 다른 다운스트림 데이터 세트로 바꿀 수 있습니다. 즐거운 미세 조정!
