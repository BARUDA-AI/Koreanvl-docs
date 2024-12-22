# 사용자 정의 데이터 세트로 미세 조정하기

## 모델 준비

| 모델 이름           | 유형 | 파라미터 | 다운로드                                                            |  크기  |
| -------------------- | ---- | ----- | ------------------------------------------------------------------- | :----: |
| InternVL2-1B         | MLLM | 0.9B  | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-1B)         | 1.8 GB |
| InternVL2-2B         | MLLM | 2.2B  | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-2B)         | 4.2 GB |
| InternVL2-4B         | MLLM | 4.2B  | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-4B)         | 7.8 GB |
| InternVL2-8B         | MLLM | 8.1B  | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-8B)         | 16 GB  |
| InternVL2-26B        | MLLM | 25.5B | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-26B)        | 48 GB  |
| InternVL2-40B        | MLLM | 40.1B | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-40B)        | 75 GB  |
| InternVL2-Llama3-76B | MLLM | 76.3B | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-Llama3-76B) | 143 GB |

두 번째 미세 조정을 시작하기 전에 제공된 사전 학습 모델을 다운로드하십시오.

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

디렉토리 구조는 다음과 같습니다:

```sh
pretrained
├── InternVL2-1B
├── InternVL2-2B
├── InternVL2-4B
├── InternVL2-8B
├── InternVL2-26B
├── InternVL2-40B
└── InternVL2-Llama3-76B
```

## 사용자 정의 학습 데이터 준비

사전 학습 모델을 다운로드한 후 사용자 정의 SFT(Supervised Fine-Tuning) 데이터를 준비합니다. `internvl_chat/shell/data/`에 [이 예](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/data/internvl_1_2_finetune.json)와 유사한 JSON 파일을 생성합니다.

JSON 파일 형식은 다음과 같아야 합니다.

```json
{
  "your-custom-dataset-1": {
    "root": "이미지/경로/",
    "annotation": "jsonl/주석/경로",
    "data_augment": false,
    "repeat_time": 1,
    "length": "데이터 세트의 샘플 수"
  },
  ...
}
```

예:

```json
{
  "sharegpt4v_instruct_gpt4-vision_cap100k": {
    "root": "playground/data/",
    "annotation": "playground/opensource/sharegpt4v_instruct_gpt4-vision_cap100k.jsonl",
    "data_augment": false,
    "repeat_time": 1,
    "length": 102025
  }
}
```

각 특정 JSONL(예: 일반 텍스트 데이터, 단일 이미지 데이터, 다중 이미지 데이터, 비디오 데이터)의 형식은 [이 문서](../get_started/chat_data_format.md)에 제공된 설명에 따라 구성될 수 있습니다.

제 제안은 [오픈 소스 InternVL 1.2의 일반 데이터](../internvl1.2/reproduce.md#training-datasets-preparation)에 새로운 도메인 특정 데이터를 추가하는 것입니다. 이를 통해 기본 기술을 유지하면서 다운스트림 기능을 향상시킬 수 있습니다. 물론 필요에 따라 새 데이터에서만 미세 조정을 선택할 수도 있습니다.

## 2차 미세 조정 시작

`````{tabs}

````{tab} 1B

사용 가능한 GPU 리소스에 따라 [전체 LLM을 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_1b_qwen2_0_5b_dynamic_res_2nd_finetune_full.sh) 또는 [LoRA 어댑터를 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_1b_qwen2_0_5b_dynamic_res_2nd_finetune_lora.sh)를 사용하여 사전 학습 모델을 미세 조정합니다.

미세 조정하기 전에 `--meta_path`를 이전 단계에서 생성한 JSON 파일의 경로로 설정합니다. 이러한 셸 스크립트의 기본 사전 학습 모델 경로는 `./pretrained/InternVL2-1B`입니다.

기본 설정에서는 시각적 인코더를 고정했습니다. 필요한 경우 고정을 해제할 수 있습니다. 일반적으로 시각적 인코더를 고정 해제하면 성능이 향상됩니다.

> 💡 전체 LLM을 미세 조정하려면 8x 32G/40G GPU가 필요하고, LoRA를 미세 조정하려면 2x 32G/40G GPU가 필요합니다.

> 💡 여기에 사용된 GPU 수와 하이퍼파라미터는 단지 예일 뿐입니다. 최적의 결과를 얻으려면 사용 가능한 하드웨어 및 데이터 세트 크기에 따라 이러한 설정을 조정해야 할 수 있습니다.

미세 조정 명령:

```sh
# 8개의 GPU를 사용하여 전체 LLM을 미세 조정, GPU당 약 30G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/internvl2.0/2nd_finetune/internvl2_1b_qwen2_0_5b_dynamic_res_2nd_finetune_full.sh
# 2개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 27G 소요
GPUS=2 PER_DEVICE_BATCH_SIZE=1 sh shell/internvl2.0/2nd_finetune/internvl2_1b_qwen2_0_5b_dynamic_res_2nd_finetune_lora.sh
# 8개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 27G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/internvl2.0/2nd_finetune/internvl2_1b_qwen2_0_5b_dynamic_res_2nd_finetune_lora.sh
```

````

````{tab} 2B

사용 가능한 GPU 리소스에 따라 [전체 LLM을 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_full.sh) 또는 [LoRA 어댑터를 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_lora.sh)를 사용하여 사전 학습 모델을 미세 조정합니다.

미세 조정하기 전에 `--meta_path`를 이전 단계에서 생성한 JSON 파일의 경로로 설정합니다. 이러한 셸 스크립트의 기본 사전 학습 모델 경로는 `./pretrained/InternVL2-2B`입니다.

기본 설정에서는 시각적 인코더를 고정했습니다. 필요한 경우 고정을 해제할 수 있습니다. 일반적으로 시각적 인코더를 고정 해제하면 성능이 향상됩니다.

> 💡 전체 LLM을 미세 조정하려면 8x 32G/40G GPU가 필요하고, LoRA를 미세 조정하려면 2x 32G/40G GPU가 필요합니다.

> 💡 여기에 사용된 GPU 수와 하이퍼파라미터는 단지 예일 뿐입니다. 최적의 결과를 얻으려면 사용 가능한 하드웨어 및 데이터 세트 크기에 따라 이러한 설정을 조정해야 할 수 있습니다.

미세 조정 명령:

```sh
# 8개의 GPU를 사용하여 전체 LLM을 미세 조정, GPU당 약 30G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/internvl2.0/2nd_finetune/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_full.sh
# 2개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 27G 소요
GPUS=2 PER_DEVICE_BATCH_SIZE=1 sh shell/internvl2.0/2nd_finetune/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_lora.sh
# 8개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 27G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/internvl2.0/2nd_finetune/internvl2_2b_internlm2_1_8b_dynamic_res_2nd_finetune_lora.sh
```

````

````{tab} 4B

사용 가능한 GPU 리소스에 따라 [전체 LLM을 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_4b_phi3_3_8b_dynamic_res_2nd_finetune_full.sh) 또는 [LoRA 어댑터를 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_4b_phi3_3_8b_dynamic_res_2nd_finetune_lora.sh)를 사용하여 사전 학습 모델을 미세 조정합니다.

미세 조정하기 전에 `--meta_path`를 이전 단계에서 생성한 JSON 파일의 경로로 설정합니다. 이러한 셸 스크립트의 기본 사전 학습 모델 경로는 `./pretrained/InternVL2-4B`입니다.

기본 설정에서는 시각적 인코더를 고정했습니다. 필요한 경우 고정을 해제할 수 있습니다. 일반적으로 시각적 인코더를 고정 해제하면 성능이 향상됩니다.

> 💡 전체 LLM을 미세 조정하려면 8x 40G GPU가 필요하고, LoRA를 미세 조정하려면 2x 24G GPU가 필요합니다.

> 💡 여기에 사용된 GPU 수와 하이퍼파라미터는 단지 예일 뿐입니다. 최적의 결과를 얻으려면 사용 가능한 하드웨어 및 데이터 세트 크기에 따라 이러한 설정을 조정해야 할 수 있습니다.

미세 조정 명령:

```sh
# 8개의 GPU를 사용하여 전체 LLM을 미세 조정, GPU당 약 40G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/internvl2.0/2nd_finetune/internvl2_4b_phi3_3_8b_dynamic_res_2nd_finetune_full.sh
# 2개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 19G 소요
GPUS=2 PER_DEVICE_BATCH_SIZE=1 sh shell/internvl2.0/2nd_finetune/internvl2_4b_phi3_3_8b_dynamic_res_2nd_finetune_lora.sh
# 8개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 19G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/internvl2.0/2nd_finetune/internvl2_4b_phi3_3_8b_dynamic_res_2nd_finetune_lora.sh
```

````

````{tab} 8B

사용 가능한 GPU 리소스에 따라 [전체 LLM을 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_8b_internlm2_7b_dynamic_res_2nd_finetune_full.sh) 또는 [LoRA 어댑터를 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_8b_internlm2_7b_dynamic_res_2nd_finetune_lora.sh)를 사용하여 사전 학습 모델을 미세 조정합니다.

미세 조정하기 전에 `--meta_path`를 이전 단계에서 생성한 JSON 파일의 경로로 설정합니다. 이러한 셸 스크립트의 기본 사전 학습 모델 경로는 `./pretrained/InternVL2-8B`입니다.

기본 설정에서는 시각적 인코더를 고정했습니다. 필요한 경우 고정을 해제할 수 있습니다. 일반적으로 시각적 인코더를 고정 해제하면 성능이 향상됩니다.

> 💡 전체 LLM을 미세 조정하려면 8개의 A100 80G GPU가 필요하고, LoRA를 미세 조정하려면 2개의 A100 80G GPU가 필요합니다.

> 💡 여기에 사용된 GPU 수와 하이퍼파라미터는 단지 예일 뿐입니다. 최적의 결과를 얻으려면 사용 가능한 하드웨어 및 데이터 세트 크기에 따라 이러한 설정을 조정해야 할 수 있습니다.

미세 조정 명령:

```sh
# 8개의 GPU를 사용하여 전체 LLM을 미세 조정, GPU당 약 77G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=2 sh shell/internvl2.0/2nd_finetune/internvl2_8b_internlm2_7b_dynamic_res_2nd_finetune_full.sh
# 2개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 79G 소요
GPUS=2 PER_DEVICE_BATCH_SIZE=2 sh shell/internvl2.0/2nd_finetune/internvl2_8b_internlm2_7b_dynamic_res_2nd_finetune_lora.sh
# 8개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 60G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=2 sh shell/internvl2.0/2nd_finetune/internvl2_8b_internlm2_7b_dynamic_res_2nd_finetune_lora.sh
```

````

````{tab} 26B

사용 가능한 GPU 리소스에 따라 [전체 LLM을 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_26b_internlm2_20b_dynamic_res_2nd_finetune_full.sh) 또는 [LoRA 어댑터를 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_26b_internlm2_20b_dynamic_res_2nd_finetune_lora.sh)를 사용하여 사전 학습 모델을 미세 조정합니다.

미세 조정하기 전에 `--meta_path`를 이전 단계에서 생성한 JSON 파일의 경로로 설정합니다. 이러한 셸 스크립트의 기본 사전 학습 모델 경로는 `./pretrained/InternVL2-26B`입니다.

기본 설정에서는 시각적 인코더를 고정했습니다. 필요한 경우 고정을 해제할 수 있습니다. 일반적으로 시각적 인코더를 고정 해제하면 성능이 향상됩니다.

> 💡 전체 LLM을 미세 조정하려면 8개의 A100 80G GPU가 필요하고, LoRA를 미세 조정하려면 2개의 A100 80G GPU가 필요합니다.

> 💡 여기에 사용된 GPU 수와 하이퍼파라미터는 단지 예일 뿐입니다. 최적의 결과를 얻으려면 사용 가능한 하드웨어 및 데이터 세트 크기에 따라 이러한 설정을 조정해야 할 수 있습니다.

미세 조정 명령:

```sh
# 8개의 GPU를 사용하여 전체 LLM을 미세 조정, GPU당 약 77G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=2 sh shell/internvl2.0/2nd_finetune/internvl2_26b_internlm2_20b_dynamic_res_2nd_finetune_full.sh
# 2개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 79G 소요
GPUS=2 PER_DEVICE_BATCH_SIZE=2 sh shell/internvl2.0/2nd_finetune/internvl2_26b_internlm2_20b_dynamic_res_2nd_finetune_lora.sh
# 8개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 60G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=2 sh shell/internvl2.0/2nd_finetune/internvl2_26b_internlm2_20b_dynamic_res_2nd_finetune_lora.sh
```

````

````{tab} 40B

사용 가능한 GPU 리소스에 따라 [전체 LLM을 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_40b_hermes2_yi_34b_dynamic_res_2nd_finetune_full.sh) 또는 [LoRA 어댑터를 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_40b_hermes2_yi_34b_dynamic_res_2nd_finetune_lora.sh)를 사용하여 사전 학습 모델을 미세 조정합니다.

미세 조정하기 전에 `--meta_path`를 이전 단계에서 생성한 JSON 파일의 경로로 설정합니다. 이러한 셸 스크립트의 기본 사전 학습 모델 경로는 `./pretrained/InternVL2-40B`입니다.

기본 설정에서는 시각적 인코더를 고정했습니다. 필요한 경우 고정을 해제할 수 있습니다. 일반적으로 시각적 인코더를 고정 해제하면 성능이 향상됩니다.

> 💡 전체 LLM을 미세 조정하려면 16개의 A100 80G GPU가 필요하고, LoRA를 미세 조정하려면 2개의 A100 80G GPU가 필요합니다.

> 💡 여기에 사용된 GPU 수와 하이퍼파라미터는 단지 예일 뿐입니다. 최적의 결과를 얻으려면 사용 가능한 하드웨어 및 데이터 세트 크기에 따라 이러한 설정을 조정해야 할 수 있습니다.

미세 조정 명령:

```sh
# SLURM 시스템에서 16개의 GPU를 사용하여 전체 LLM을 미세 조정, GPU당 약 77G 소요
PARTITION='your partition' GPUS=16 PER_DEVICE_BATCH_SIZE=2 sh shell/internvl2.0/2nd_finetune/internvl2_40b_hermes2_yi_34b_dynamic_res_2nd_finetune_full.sh
# 2개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 74G 소요
GPUS=2 PER_DEVICE_BATCH_SIZE=2 sh shell/internvl2.0/2nd_finetune/internvl2_40b_hermes2_yi_34b_dynamic_res_2nd_finetune_lora.sh
# 8개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 74G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=2 sh shell/internvl2.0/2nd_finetune/internvl2_40b_hermes2_yi_34b_dynamic_res_2nd_finetune_lora.sh
```

````

````{tab} 76B

사용 가능한 GPU 리소스에 따라 [전체 LLM을 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_76b_hermes2_llama3_70b_dynamic_res_2nd_finetune_full.sh) 또는 [LoRA 어댑터를 학습하기 위한 스크립트](https://github.com/OpenGVLab/InternVL/blob/main/internvl_chat/shell/internvl2.0/2nd_finetune/internvl2_76b_hermes2_llama3_70b_dynamic_res_2nd_finetune_lora.sh)를 사용하여 사전 학습 모델을 미세 조정합니다.

미세 조정하기 전에 `--meta_path`를 이전 단계에서 생성한 JSON 파일의 경로로 설정합니다. 이러한 셸 스크립트의 기본 사전 학습 모델 경로는 `./pretrained/InternVL2-Llama3-76B`입니다.

기본 설정에서는 시각적 인코더를 고정했습니다. 필요한 경우 고정을 해제할 수 있습니다. 일반적으로 시각적 인코더를 고정 해제하면 성능이 향상됩니다.

> 💡 전체 LLM을 미세 조정하려면 32개의 A100 80G GPU가 필요하고, LoRA를 미세 조정하려면 8개의 A100 80G GPU가 필요합니다.

> 💡 여기에 사용된 GPU 수와 하이퍼파라미터는 단지 예일 뿐입니다. 최적의 결과를 얻으려면 사용 가능한 하드웨어 및 데이터 세트 크기에 따라 이러한 설정을 조정해야 할 수 있습니다.

미세 조정 명령:

```sh
# SLURM 시스템에서 32개의 GPU를 사용하여 전체 LLM을 미세 조정, GPU당 약 77G 소요
PARTITION='your partition' GPUS=32 PER_DEVICE_BATCH_SIZE=1 sh shell/internvl2.0/2nd_finetune/internvl2_76b_hermes2_llama3_70b_dynamic_res_2nd_finetune_full.sh
# 8개의 GPU를 사용하여 LoRA를 미세 조정, GPU당 약 74G 소요
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/internvl2.0/2nd_finetune/internvl2_76b_hermes2_llama3_70b_dynamic_res_2nd_finetune_lora.sh
```

````

`````

문제가 발생하면 알려주세요. 사용성을 향상시키기 위해 학습 가이드를 업데이트하겠습니다.

## 인용

이 프로젝트가 연구에 유용하다고 생각되면 다음을 인용해 주십시오.

```BibTeX
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
