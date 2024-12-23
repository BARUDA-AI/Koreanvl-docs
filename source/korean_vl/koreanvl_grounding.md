# InternVL 2단계 사전 학습 및 검색 미세 조정

이 폴더에는 2단계 사전 학습 및 검색 미세 조정을 위한 InternVL 1.0의 구현이 포함되어 있으며, 이는 [InternVL 1.0 논문](https://arxiv.org/pdf/2312.14238)의 섹션 4.3에 해당합니다.

![image](./internvl_g.png)

## 데이터 준비

세 가지 데이터 세트를 준비해야 합니다: COCO Caption, Flickr30K, NoCaps.

<details open>
<summary>COCO Caption</summary>

```bash
mkdir -p data/coco && cd data/coco

# coco 이미지 다운로드
wget http://images.cocodataset.org/zips/train2014.zip && unzip train2014.zip
wget http://images.cocodataset.org/zips/val2014.zip && unzip val2014.zip
wget http://images.cocodataset.org/zips/test2015.zip && unzip test2015.zip

mkdir -p annotations && cd annotations/
# 변환된 주석 파일 다운로드
wget https://storage.googleapis.com/sfr-vision-language-research/datasets/coco_karpathy_train.json
wget https://github.com/OpenGVLab/InternVL/releases/download/data/coco_karpathy_test.json
wget https://github.com/OpenGVLab/InternVL/releases/download/data/coco_karpathy_test_gt.json
cd ../../../
```

</details>

<details open>
<summary>Flickr30K</summary>

```bash
mkdir -p data/flickr30k && cd data/flickr30k

# https://bryanplummer.com/Flickr30kEntities/ 에서 이미지 다운로드
# karpathy 분할 주석은 다음 링크에서 다운로드할 수 있습니다:
# https://github.com/mehdidc/retrieval_annotations/releases/download/1.0.0/flickr30k_test_karpathy.txt
# 이 파일은 clip-benchmark 저장소에서 제공됩니다.
# 이 txt 파일을 json 형식으로 변환하고 변환된 파일을 다운로드합니다:
wget https://github.com/OpenGVLab/InternVL/releases/download/data/flickr30k_cn_test.txt
wget https://github.com/OpenGVLab/InternVL/releases/download/data/flickr30k_cn_train.txt
wget https://github.com/OpenGVLab/InternVL/releases/download/data/flickr30k_test_karpathy.json
wget https://github.com/mehdidc/retrieval_annotations/releases/download/1.0.0/flickr30k_test_karpathy.txt
wget https://github.com/mehdidc/retrieval_annotations/releases/download/1.0.0/flickr30k_train_karpathy.txt
wget https://github.com/mehdidc/retrieval_annotations/releases/download/1.0.0/flickr30k_val_karpathy.txt

cd ../..
```

</details>

<details open>
<summary>NoCaps</summary>

```bash
mkdir -p data/nocaps && cd data/nocaps

# https://nocaps.org/download 에서 이미지 다운로드
# 원본 주석은 https://nocaps.s3.amazonaws.com/nocaps_val_4500_captions.json 에서 다운로드할 수 있습니다.
wget https://nocaps.s3.amazonaws.com/nocaps_val_4500_captions.json

cd ../..
```

</details>

다운로드가 완료되면 디렉토리 구조는 다음과 같습니다:

```shell
data
├── coco
│   ├── annotations
│   │   ├── coco_karpathy_train.json
│   ├── test2017
│   ├── train2014
│   ├── train2017
│   ├── val2014
│   └── val2017
├── flickr30k
│   ├── flickr30k_cn_test.txt
│   ├── flickr30k_cn_train.txt
│   ├── flickr30k_test_karpathy.json
│   ├── flickr30k_test_karpathy.txt
│   ├── flickr30k_train_karpathy.txt
│   ├── flickr30k_val_karpathy.txt
│   └── Images
└── nocaps
    ├── images
    └── nocaps_val_4500_captions.json
```

## 모델 준비

| 모델 이름          | 유형        | 파라미터 | 다운로드                                                          |  크기   |
| ------------------ | ----------- | ----- | ----------------------------------------------------------------- | :-----: |
| InternVL-14B-224px | huggingface | 13.8B | 🤗 [HF link](https://huggingface.co/OpenGVLab/InternVL-14B-224px) | 27.7 GB |

위의 모델 가중치를 다운로드하여 `pretrained/` 폴더에 넣으십시오.

```sh
cd pretrained/
# pip install -U huggingface_hub
huggingface-cli download --resume-download --local-dir-use-symlinks False OpenGVLab/InternVL-14B-224px --local-dir InternVL-14B-224px
```

디렉토리 구조는 다음과 같습니다:

```sh
pretrained
└── InternVL-14B-224px/
```

## 생성적 사전 학습

현재 이 부분의 코드를 공개할 계획은 없습니다.

## 평가

### 제로샷 이미지 캡셔닝

| 모델      | 데이터 세트                 | BLEU4 | METEOR | CIDEr |
| ---------- | ----------------------- | ----- | ------ | ----- |
| InternVL-G | COCO Karpathy test      | 37.1  | 30.1   | 128.2 |
| InternVL-G | Flickr30K Karpathy test | 27.0  | 25.3   | 79.2  |
| InternVL-G | NoCaps val              | 44.3  | 30.1   | 113.7 |

<details>
  <summary>[InternVL-G] COCO Karpathy test</summary>

```bash
sh evaluate.sh pretrained/InternVL-14B-224px caption-coco
```

예상 결과:

```
['coco', 'English caption:', 10.5974, dict_items([('Bleu_1', 0.7876323287981284), ('Bleu_2', 0.6353512494727918), ('Bleu_3', 0.49108984183589743), ('Bleu_4', 0.37062736733849205), ('METEOR', 0.30106315496945923), ('ROUGE_L', 0.5898249189475652), ('CIDEr', 1.281844384075423)])]
```

</details>

<details>
  <summary>[InternVL-G] Flickr30K Karpathy test</summary>

```
sh evaluate.sh pretrained/InternVL-14B-224px caption-flickr30k
```

예상 결과:

```bash
['flickr30k', 'English caption:', 10.666, dict_items([('Bleu_1', 0.7182900534357628), ('Bleu_2', 0.5353390037921949), ('Bleu_3', 0.3834462132295285), ('Bleu_4', 0.2702131471765472), ('METEOR', 0.25263515267930103), ('ROUGE_L', 0.5305876871149064), ('CIDEr', 0.7919734768328237)])]
```

</details>

<details>
  <summary>[InternVL-G] NoCaps val</summary>

```bash
sh evaluate.sh pretrained/InternVL-14B-224px caption-nocaps
```

예상 결과:

```
['nocaps', 'English caption:', 10.463111111111111, dict_items([('Bleu_1', 0.8518290482155187), ('Bleu_2', 0.7165227921485106), ('Bleu_3', 0.5733723839888316), ('Bleu_4', 0.44268902150723105), ('METEOR', 0.30078174807736896), ('ROUGE_L', 0.6070208063052156), ('CIDEr', 1.1371742045267772)])]
```

</details>

### 미세 조정된 이미지-텍스트 검색

#### Flickr30K 미세 조정 모델: [InternVL-14B-Flickr30K-FT-364px](https://huggingface.co/OpenGVLab/InternVL-14B-Flickr30K-FT-364px)

<table class="table">
  <tr align=center>
      <td rowspan="3" align=center><b>모델</b></td>
      <td colspan="6" align=center><b>Flickr30K</b></td>
      <td rowspan="3" align=center><b>평균</b></td>

</tr>
   <tr align=center>
      <td colspan="3" align=center><b>이미지-텍스트</b></td>
      <td colspan="3" align=center><b>텍스트-이미지</b></td>
   </tr>
   <tr>
      <td>R@1</td>
      <td>R@5</td>
      <td>R@10</td>
      <td>R@1</td>
      <td>R@5</td>
      <td>R@10</td>
   </tr>

<tr align=center>
      <td>InternVL-C-FT</td>
      <td>97.2</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>88.5</td>
      <td>98.4</td>
      <td>99.2</td>
      <td>97.2</td>
   </tr>
<tr align=center>
      <td>InternVL-G-FT</td>
      <td>97.9</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>89.6</td>
      <td>98.6</td>
      <td>99.2</td>
      <td>97.6</td>
   </tr>

</table>

<details>
  <summary>[InternVL-C-FT] Flickr30K</summary>

```bash
cd ../clip_benchmark/
CUDA_VISIBLE_DEVICES=0 python3 clip_benchmark/cli.py eval --model_type internvl --language "en" --task "zeroshot_retrieval" \
     --dataset "flickr30k" --dataset_root ./data/flickr30k --model internvl_c_retrieval_hf \
     --pretrained ./work_dirs/internvl_stage2_finetune_flickr_364_bs1024_ep10/ --output result_ft.json
```

예상 결과:

```
{"dataset": "flickr30k", "model": "internvl_c_retrieval_hf", "pretrained": "./work_dirs/internvl_stage2_finetune_flickr_364_bs1024_ep10", "task": "zeroshot_retrieval",
"metrics": {"image_retrieval_recall@1": 0.8853999972343445, "text_retrieval_recall@1": 0.972000002861023,
"image_retrieval_recall@5": 0.9836000204086304, "text_retrieval_recall@5": 1.0,
"image_retrieval_recall@10": 0.9923999905586243, "text_retrieval_recall@10": 1.0}, "language": "en"}
```

</details>

<details>
  <summary>[InternVL-G-FT] Flickr30K</summary>

```bash
cd ../clip_benchmark/
CUDA_VISIBLE_DEVICES=0 python3 clip_benchmark/cli.py eval --model_type internvl --language "en" --task "zeroshot_retrieval" \
     --dataset "flickr30k" --dataset_root ./data/flickr30k --model internvl_g_retrieval_hf \
     --pretrained ./work_dirs/internvl_stage2_finetune_flickr_364_bs1024_ep10/ --output result_ft.json
```

예상 결과:

```
{"dataset": "flickr30k", "model": "internvl_g_retrieval_hf", "pretrained": "./work_dirs/internvl_stage2_finetune_flickr_364_bs1024_ep10", "task": "zeroshot_retrieval",
"metrics": {"image_retrieval_recall@1": 0.895799994468689, "text_retrieval_recall@1": 0.9789999723434448,
"image_retrieval_recall@5": 0.9861999750137329, "text_retrieval_recall@5": 1.0,
"image_retrieval_recall@10": 0.9922000169754028, "text_retrieval_recall@10": 1.0}, "language": "en"}
```

</details>

#### Flickr30K-CN 미세 조정 모델: [InternVL-14B-FlickrCN-FT-364px](https://huggingface.co/OpenGVLab/InternVL-14B-FlickrCN-FT-364px)

<table class="table">
  <tr align=center>
      <td rowspan="3" align=center><b>모델</b></td>
      <td colspan="6" align=center><b>Flickr30K-CN</b></td>
      <td rowspan="3" align=center><b>평균</b></td>

</tr>
   <tr align=center>
       <td colspan="3" align=center><b>이미지-텍스트</b></td>
      <td colspan="3" align=center><b>텍스트-이미지</b></td>
   </tr>
   <tr>
      <td>R@1</td>
      <td>R@5</td>
      <td>R@10</td>
      <td>R@1</td>
      <td>R@5</td>
      <td>R@10</td>
   </tr>

<tr align=center>
      <td>InternVL-C-FT</td>
      <td>96.5</td>
      <td>99.9</td>
      <td>100.0</td>
      <td>85.2</td>
      <td>97.0</td>
      <td>98.5</td>
      <td>96.2</td>
   </tr>
<tr align=center>
      <td>InternVL-G-FT</td>
      <td>96.9</td>
      <td>99.9</td>
      <td>100.0</td>
      <td>85.9</td>
      <td>97.1</td>
      <td>98.7</td>
      <td>96.4</td>
   </tr>

</table>

<details>
  <summary>[InternVL-C-FT] Flickr30K-CN</summary>

```bash
cd ../clip_benchmark/
CUDA_VISIBLE_DEVICES=0 python3 clip_benchmark/cli.py eval --model_type internvl --language "cn" --task "zeroshot_retrieval" \
     --dataset "flickr30k" --dataset_root ./data/flickr30k --model internvl_c_retrieval_hf \
     --pretrained ./work_dirs/internvl_stage2_finetune_flickrcn_364_bs1024_ep10/ --output result_ft.json
```

예상 결과:

```
{"dataset": "flickr30k", "model": "internvl_c_retrieval_hf", "pretrained": "./work_dirs/internvl_stage2_finetune_flickrcn_364_bs1024_ep10", "task": "zeroshot_retrieval",
"metrics": {"image_retrieval_recall@1": 0.8521999716758728, "text_retrieval_recall@1": 0.9649999737739563,
"image_retrieval_recall@5": 0.9697999954223633, "text_retrieval_recall@5": 0.9990000128746033,
"image_retrieval_recall@10": 0.9854000210762024, "text_retrieval_recall@10": 1.0}, "language": "cn"}
```

</details>

<details>
  <summary>[InternVL-G-FT] Flickr30K-CN</summary>

```bash
cd ../clip_benchmark/
CUDA_VISIBLE_DEVICES=0 python3 clip_benchmark/cli.py eval --model_type internvl --language "cn" --task "zeroshot_retrieval" \
     --dataset "flickr30k" --dataset_root ./data/flickr30k --model internvl_g_retrieval_hf \
     --pretrained ./work_dirs/internvl_stage2_finetune_flickrcn_364_bs1024_ep10/ --output result_ft.json
```

예상 결과:

```
{"dataset": "flickr30k", "model": "internvl_g_retrieval_hf", "pretrained": "./work_dirs/internvl_stage2_finetune_flickrcn_364_bs1024_ep10", "task": "zeroshot_retrieval",
"metrics": {"image_retrieval_recall@1": 0.8587999939918518, "text_retrieval_recall@1": 0.968999981880188,
"image_retrieval_recall@5": 0.9714000225067139, "text_retrieval_recall@5": 0.9990000128746033,
"image_retrieval_recall@10": 0.9865999817848206, "text_retrieval_recall@10": 1.0}, "language": "cn"}
```

</details>

## 검색 미세 조정 (전체)

> 참고: 실험에서 전체 매개변수 미세 조정은 Flickr30K 및 COCO의 이미지-텍스트 검색 작업에서 최상의 결과를 얻었습니다. 이 섹션의 실험적 하이퍼파라미터를 따르면 [평가 섹션](#평가)에 보고된 모델 성능을 재현할 수 있습니다.

32개의 GPU와 slurm 시스템을 사용하여 Flickr30K에서 InternVL을 미세 조정하려면 다음을 실행하십시오:

```bash
PARTITION='your partition' GPUS=32 sh shell/finetune/internvl_stage2_finetune_flickr_364_bs1024_ep10.sh
```

32개의 GPU와 slurm 시스템을 사용하여 Flickr30K-CN에서 InternVL을 미세 조정하려면 다음을 실행하십시오:

```shell
PARTITION='your partition' GPUS=32 sh shell/finetune/internvl_stage2_finetune_flickrcn_364_bs1024_ep10.sh
```

32개의 GPU와 slurm 시스템을 사용하여 COCO에서 InternVL을 미세 조정하려면 다음을 실행하십시오:

```shell
PARTITION='your partition' GPUS=32 sh shell/finetune/internvl_stage2_finetune_coco_364_bs1024_ep5.sh
```

여기에 사용된 하이퍼파라미터는 다음과 같습니다:

| 설정                      | Flickr30K                           | Flickr30K-CN                        | COCO                                |
| --------------------------- | ----------------------------------- | ----------------------------------- | ----------------------------------- |
| 학습률               | 1e-6                                | 1e-6                                | 1e-6                                |
| 계층별 학습률<br>감쇠율 | InternViT-6B (0.9),<br>QLLaMA (0.9) | InternViT-6B (0.9),<br>QLLaMA (0.9) | InternViT-6B (0.9),<br>QLLaMA (0.9) |
| 옵티마이저                   | AdamW                               | AdamW                               | AdamW                               |
| 가중치 감쇠                | 0.05                                | 0.05                                | 0.05                                |
| 입력 해상도            | 364x364                             | 364x364                             | 364x364                             |
| 총 배치 크기            | 1024                                | 1024                                | 1024                                |
| 웜업 반복          | 100                                 | 100                                 | 100                                 |
| 훈련 에폭             | 10                                  | 10                                  | 5                                   |
| 드롭 경로 비율              | 0.3                                 | 0.3                                 | 0.3                                 |
| 수치 정밀도         | zero1 + bf16                        | zero1 + bf16                        | zero1 + bf16                        |
| 훈련 가능한 / 총 매개변수    | 14B / 14B                           | 14B / 14B                           | 14B / 14B                           |
| 훈련을 위한 GPU           | 32×A100 (80G)                       | 32×A100 (80G)                       | 32×A100 (80G)                       |
| 필요한 GPU 메모리         | 80G                                 | 80G                                 | 80G                                 |

## 검색 미세 조정 (헤드)

> 참고: 이 섹션에서는 모델의 비용 효율적인 미세 조정을 수행하는 방법을 보여줍니다. 여기에 표시된 하이퍼파라미터는 특정 작업에 최적화되지 않았습니다. 실제 적용을 위해서는 최적의 성능을 달성하기 위해 하이퍼파라미터를 추가로 조정해야 할 수 있습니다.

4개의 GPU를 사용하여 Flickr30K에서 InternVL의 헤드를 미세 조정하려면 다음을 실행하십시오:

```bash
GPUS=4 BATCH_SIZE=32 sh shell/head_finetune/internvl_stage2_finetune_flickr_224_bs1024_ep10_head_4gpu.sh
```

4개의 GPU를 사용하여 Flickr30K-CN에서 InternVL의 헤드를 미세 조정하려면 다음을 실행하십시오:

```shell
GPUS=4 BATCH_SIZE=32 sh shell/head_finetune/internvl_stage2_finetune_flickrcn_224_bs1024_ep10_head_4gpu.sh
```

4개의 GPU를 사용하여 COCO에서 InternVL의 헤드를 미세 조정하려면 다음을 실행하십시오:

```shell
GPUS=4 BATCH_SIZE=32 shell/head_finetune/internvl_stage2_finetune_coco_224_bs1024_ep5_head_4gpu.sh
```

여기에 사용된 하이퍼파라미터는 다음과 같습니다:

| 설정                   | Flickr30K     | Flickr30K-CN  | COCO          |
| ------------------------ | ------------- | ------------- | ------------- |
| 학습률            | 1e-6          | 1e-6          | 1e-6          |
| 옵티마이저                | AdamW         | AdamW         | AdamW         |
| 가중치 감쇠             | 0.05          | 0.05          | 0.05          |
| 입력 해상도         | 224x224       | 224x224       | 224x224       |
| 총 배치 크기         | 4x32          | 4x32          | 4x32          |
| 웜업 반복       | 100           | 100           | 100           |
| 훈련 에폭          | 10            | 10            | 5             |
| 드롭 경로 비율           | 0.0           | 0.0           | 0.3           |
| 수치 정밀도      | zero3 + bf16  | zero3 + bf16  | zero1 + bf16  |
| 훈련 가능한 / 총 매개변수 | 0.2B / 14B    | 0.2B / 14B    | 0.2B / 14B    |
| 훈련을 위한 GPU        | 4×GPU (>=32G) | 4×GPU (>=32G) | 4×GPU (>=32G) |
| 필요한 GPU 메모리      | 24G           | 24G           | 24G           |

## 검색 미세 조정 (LoRA)

> 참고: 이 섹션에서는 모델의 비용 효율적인 미세 조정을 수행하는 방법을 보여줍니다. 여기에 표시된 하이퍼파라미터는 특정 작업에 최적화되지 않았습니다. 실제 적용을 위해서는 최적의 성능을 달성하기 위해 하이퍼파라미터를 추가로 조정해야 할 수 있습니다.

4개의 GPU를 사용하여 Flickr30K에서 LoRA를 사용하여 InternVL을 미세 조정하려면 다음을 실행하십시오:

```bash
GPUS=4 BATCH_SIZE=32 sh shell/lora_finetune/internvl_stage2_finetune_flickr_224_bs1024_ep10_lora16_4gpu.sh
```

4개의 GPU를 사용하여 Flickr30K-CN에서 LoRA를 사용하여 InternVL을 미세 조정하려면 다음을 실행하십시오:

```shell
GPUS=4 BATCH_SIZE=32 sh shell/lora_finetune/internvl_stage2_finetune_flickrcn_224_bs1024_ep10_lora16_4gpu.sh
```

4개의 GPU를 사용하여 COCO에서 LoRA를 사용하여 InternVL을 미세 조정하려면 다음을 실행하십시오:

```shell
GPUS=4 BATCH_SIZE=32 shell/lora_finetune/internvl_stage2_finetune_coco_224_bs1024_ep5_lora16_4gpu.sh
```

여기에 사용된 하이퍼파라미터는 다음과 같습니다:

| 설정                   | Flickr30K     | Flickr30K-CN  | COCO          |
| ------------------------ | ------------- | ------------- | ------------- |
| 학습률            | 1e-6          | 1e-6          | 1e-6          |
| 옵티마이저                | AdamW         | AdamW         | AdamW         |
| lora 순위                | 16            | 16            | 16            |
| 가중치 감쇠             | 0.05          | 0.05          | 0.05          |
| 입력 해상도         | 224x224       | 224x224       | 224x224       |
| 총 배치 크기         | 4x32          | 4x32          | 4x32          |
| 웜업 반복       | 100           | 100           | 100           |
| 훈련 에폭          | 10            | 10            | 5             |
| 드롭 경로 비율           | 0.0           | 0.0           | 0.3           |
| 수치 정밀도      | zero3 + bf16  | zero3 + bf16  | zero1 + bf16  |
| 훈련 가능한 / 총 매개변수 | 0.3B / 14B    | 0.3B / 14B    | 0.3B / 14B    |
| 훈련을 위한 GPU        | 4×GPU (>=40G) | 4×GPU (>=40G) | 4×GPU (>=40G) |
| 필요한 GPU 메모리      | 37G           | 37G           | 37G           |

## 사용자 정의 데이터 세트 미세 조정

1. **데이터 구성**: COCO 또는 Flickr30K와 유사하게 데이터 세트를 포맷하십시오.

2. **메타 정보 업데이트**: `internvl_g/internvl/train/internvl_stage2_finetune.py`의 `ds_collections` 사전에 데이터 세트의 메타 정보를 추가하십시오. 예를 들면 다음과 같습니다:

   ```python
   ds_collections = {
       'my_dataset_flickr_format': {
           'root': './data/my_dataset/images/',
           'annotation': './data/my_dataset/annotations.txt',
       },
       'my_dataset_coco_format': {
           'root': './data/my_dataset/',
           'annotation': './data/my_dataset/annotations.json',
       },
   }
   ```

3. **데이터 세트 이름 지정**:

   - 데이터 세트의 `dataset_name`에 `flickr_format` 또는 `coco_format`을 포함하십시오. 이렇게 하면 스크립트가 Flickr30K 또는 COCO 데이터 로더를 재사용할 수 있습니다.

이러한 단계를 따르면 기존 COCO 또는 Flickr30K 데이터 로딩 메커니즘을 사용하여 사용자 정의 데이터 세트에서 InternVL 모델을 쉽게 미세 조정할 수 있습니다.

## 인용

이 프로젝트가 귀하의 연구에 유용하다고 생각되면 다음을 인용해 주십시오:

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

<br>
<br>
