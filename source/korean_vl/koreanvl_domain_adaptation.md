# 도메인 적응

## 다중 시점 이미지 기반 자율 주행

### 데이터 준비

- *InternVL-Chat-V1-2-SFT-Data*를 준비합니다. [문서](../internvl1.2/reproduce.md/#training-datasets-preparation)를 참조하세요.
- [InternVL-Domain-Adaptation-Data](https://huggingface.co/datasets/OpenGVLab/InternVL-Domain-Adaptation-Data)에서 `drivelm_train.jsonl` 및 `drivelm_val.jsonl`을 다운로드합니다. `drivelm_train.jsonl` 및 `drivelm_val.jsonl`은 형식 변환 후의 데이터입니다.
- [DriveLM](https://github.com/OpenDriveLab/DriveLM/tree/main/challenge)에서 이미지를 다운로드하고 `tools/images_stitching.py`를 사용하여 이미지를 처리합니다.

```sh
python tools/images_stitching.py --data-root InternVL-Domain-Adaptation-Data/images/drivelm --ann-file path/to/v1_1_val_nus_q_only.json
```

- [mme-realworld](https://huggingface.co/datasets/yifanzhang114/MME-RealWorld/tree/main)의 자율 주행 하위 집합을 다운로드합니다.
- 다음 구조에 따라 파일을 구성합니다.

  ```
  path/to/internvl_chat/InternVL-Domain-Adaptation-Data
  ├── train_data
  │   └── drivelm_train.jsonl
  ├── images
  │   ├── MME-RealWorld
  |   |   └── data/AutonomousDriving/
  |   └── drivelm
  |       ├── nuscenes/
  |       └── stitch/
  ├── train_meta
  |   ├── internvl_1_2_finetune_drivelm.json
  └── val
      ├── MME_RealWorld.json
      └── drivelm_val.jsonl
  ```

### 미세 조정

사전 훈련된 모델을 다운로드하고 훈련 데이터를 준비한 후 다음 스크립트를 사용하여 모델을 조정할 수 있습니다.

미세 조정 전에 `--model_name_or_path`를 사전 훈련된 모델의 경로로 설정합니다.

기본 설정에서는 전체 매개변수 미세 조정을 수행하지만 계산 리소스에 따라 시각적 인코더를 선택적으로 고정할 수 있습니다.

`````{tabs}

````{tab} Mini-InternVL-1B

```sh
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/mini_internvl/domain_adaptation/internvl2_1b_qwen2_0_5b_dynamic_res_finetune_drivelm.sh
```

````

````{tab} Mini-InternVL-2B

```sh
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/mini_internvl/domain_adaptation/internvl2_2b_internlm2_1_8b_dynamic_res_finetune_drivelm.sh
```

````
````{tab} Mini-InternVL-4B

```sh
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/mini_internvl/domain_adaptation/internvl2_4b_phi3_3_8b_dynamic_res_finetune_drivelm.sh
```

````

`````

### 평가

- [DriveLM Challenge](https://github.com/OpenDriveLab/DriveLM/tree/main/challenge)

이 데이터 세트에는 인식, 예측 및 계획에 대한 데이터가 포함되어 있어 자율 주행 시나리오에 대한 포괄적인 보기를 제공합니다. DriveLM Challenge에서 미세 조정된 모델을 테스트하기 위해 이미지와 주석을 모두 포함하여 데이터를 이미 사전 처리했습니다. 이제 다음 명령을 사용하여 8개의 GPU로 테스트를 직접 실행할 수 있습니다.

```bash
GPUS=8 sh evaluate.sh ${checkpoint} drivelm
```

- MME-Realworld-AD

[MME-Realworld](https://huggingface.co/datasets/yifanzhang114/MME-RealWorld/tree/main)에는 자율 주행 장면의 하위 집합이 포함되어 있으며, 여기서 *인식* 및 *추론* 작업에 대한 모델의 성능을 평가합니다.
다음 명령을 사용하여 8개의 GPU로 테스트를 수행하십시오.

```bash
GPUS=8 sh evaluate.sh ${checkpoint} mme—realworld --dynamic --max-num  12 --subtask  Autonomous_Driving
```

## 의료 이미지

### 데이터 준비

- *InternVL-Chat-V1-2-SFT-Data*를 준비합니다. [문서](../internvl1.2/reproduce.md/#training-datasets-preparation)를 참조하십시오.
- [InternVL-Domain-Adaptation-Data](https://huggingface.co/datasets/OpenGVLab/InternVL-Domain-Adaptation-Data)에서 다음 파일을 다운로드하고 이미지를 추출한 후 다음 디렉토리 구조로 구성합니다.

```
path/to/internvl_chat/InternVL-Domain-Adaptation-Data
├── train_data
│   └── medical_sft_sample500k.jsonl
├── images
│   └── medical_images
└── train_meta
    └── internvl_1_2_finetune_medical.json
```

### 미세 조정

다음 스크립트를 사용하여 모델을 미세 조정하십시오.

`````{tabs}

````{tab} 1B

```sh
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/mini_internvl/domain_adaptation/internvl2_1b_qwen2_0_5b_dynamic_res_finetune_medical.sh

````

````{tab} 2B

```sh
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/mini_internvl/domain_adaptation/internvl2_2b_internlm2_1_8b_dynamic_res_finetune_medical.sh
```

````
````{tab} 4B

```sh
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/mini_internvl/domain_adaptation/internvl2_4b_phi3_3_8b_dynamic_res_finetune_medical.sh
```

````

`````

### 평가

포괄적인 의료 AI 벤치마크인 [GMAI-MMBench](https://github.com/uni-medical/GMAI-MMBench)에서 모델을 테스트합니다. 평가는 [VLMEvalKit](https://github.com/open-compass/VLMEvalKit) 프레임워크를 사용하여 수행되었습니다.

테스트에 대한 자세한 내용은 [문서](https://huggingface.co/datasets/OpenGVLab/GMAI-MMBench)를 참조하십시오.

중요하게, 테스트 전에 [config_file](https://github.com/open-compass/VLMEvalKit/blob/main/vlmeval/config.py)의 `internvl_series`에 모델을 추가하십시오.

```
  'Mini-InternVL-DA-1B': partial(InternVLChat, model_path='path/to/your/checkpoints', version='V2.0'),
  'Mini-InternVL-DA-2B': partial(InternVLChat, model_path='path/to/your/checkpoints', version='V2.0'),
  'Mini-InternVL-DA-4B': partial(InternVLChat, model_path='path/to/your/checkpoints', version='V2.0')
```

## 원격 감지

### 데이터 준비

- *InternVL-Chat-V1-2-SFT-Data*를 준비합니다. [문서](../internvl1.2/reproduce.md/#training-datasets-preparation)를 참조하십시오.
- 아래 디렉토리 트리 구조에 따라 [InternVL-Domain-Adaptation-Data](https://huggingface.co/datasets/OpenGVLab/InternVL-Domain-Adaptation-Data)의 train_data, train_meta 및 val 디렉토리에서 해당 파일을 다운로드하십시오.
- [GeoChat](https://huggingface.co/datasets/MBZUAI/GeoChat_Instruct/tree/main), [FIT-RS](https://huggingface.co/datasets/ll-13/FIT-RS/blob/main/FIT-RS_Instruction/FIT-RS_Img.tar.gz), [RSVQA](https://rsvqa.sylvainlobry.com/) 및 [DIOR-RSVG](https://drive.google.com/drive/folders/1hTqtYsC6B-m4ED2ewx5oKuYZV13EoJp_)에서 이미지를 다운로드합니다. 파일을 추출하여 아래 디렉토리 구조 내의 해당 위치에 배치합니다.

```
path/to/internvl_chat/InternVL-Domain-Adaptation-Data
├── train_data
│   ├── dior_rsvg_instruct_26k.jsonl
|   ├── fit_rs_vqa_100k.jsonl
|   ├── rsvqa_hr_train_instruct_100k.jsonl
│   └── geochat_instruct.jsonl
├── images
|   ├── RSVQA_L
|   |   └── Images_LR
|   ├── RSVQA-H
|   |   └── Data
|   ├── DIOR-RSVG
|   |   └── JPEGImages
|   ├── FIT-RS
|   |   └── imgv2_split_512_100_vaild
|   └── GeoChat
|       └── images
|           └── final_images_llava
├── train_meta
|   └── internvl_1_2_finetune_remote.json
└── val
    ├── dior_rsvg_test.json
    ├── rsvqa_h_test_1_instruct.json
    ├── rsvqa_h_test_2_instruct.json
    └── rsvqa_l_test_instruct.json
```
### 미세 조정

다음 스크립트를 사용하여 모델을 미세 조정하십시오.

`````{tabs}

````{tab} 1B

```sh
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/mini_internvl/domain_adaptation/internvl2_1b_qwen2_0_5b_dynamic_res_finetune_remote.sh

````

````{tab} 2B

```sh
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/mini_internvl/domain_adaptation/internvl2_2b_internlm2_1_8b_dynamic_res_finetune_remote.sh
```

````
````{tab} 4B

```sh
GPUS=8 PER_DEVICE_BATCH_SIZE=1 sh shell/mini_internvl/domain_adaptation/internvl2_4b_phi3_3_8b_dynamic_res_finetune_remote.sh
```

`````

### 평가

VQA 작업에는 RSVQA 데이터 세트를 사용하고 시각적 접지 작업에는 DIOR-RSVG 데이터 세트를 사용하여 전송된 모델의 성능을 평가합니다.

- RS-VQA

평가를 위해 RSVQA-LR 및 RSVQA-HR 데이터 세트의 Presence, Comparison 및 Rural/Urban 하위 집합을 선택했습니다.

이제 다음 명령을 사용하여 8개의 GPU로 테스트를 직접 실행할 수 있습니다.
```bash
# RSVQA-LR
GPUS=8 sh evaluate.sh ${checkpoint} rsvqa-lr --dynamic --max-num  12
# RSVQA-HR-test1
GPUS=8 sh evaluate.sh ${checkpoint} rsvqa-hr-test1 --dynamic --max-num  12
# RSVQA-LR-test2
GPUS=8 sh evaluate.sh ${checkpoint} rsvqa-hr-test2 --dynamic --max-num  12
```

- DIOR-RSVG

이제 다음 명령을 사용하여 8개의 GPU로 테스트를 직접 실행할 수 있습니다.

```bash
GPUS=8 sh evaluate.sh ${checkpoint} dior-rsvg --dynamic --max-num  12
```

## 시간 정보를 사용한 자율 주행

곧 제공됩니다...

## 인용

이 프로젝트가 연구에 유용하다고 생각되면 다음을 인용하십시오.

```BibTeX
@article{gao2024mini,
  title={Mini-InternVL: A Flexible-Transfer Pocket Multimodal Model with 5\% Parameters and 90\% Performance},
  author={Gao, Zhangwei and Chen, Zhe and Cui, Erfei and Ren, Yiming and Wang, Weiyun and Zhu, Jinguo and Tian, Hao and Ye, Shenglong and He, Junjun and Zhu, Xizhou and others},
  journal={arXiv preprint arXiv:2410.16261},
  year={2024}
}
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
