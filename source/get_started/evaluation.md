# 평가 데이터 준비

## 이미지 캡션 벤치마크

### COCO Karpathy 테스트

> COCO 이미지는 VQAv2/OK-VQA/RefCOCO/RefCOCO+/RefCOCOg에서 사용됩니다. 이러한 벤치마크에서 평가하기 전에 COCO 이미지를 다운로드했는지 확인하세요.

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/coco && cd data/coco

# coco 이미지 다운로드
wget http://images.cocodataset.org/zips/train2014.zip && unzip train2014.zip
wget http://images.cocodataset.org/zips/val2014.zip && unzip val2014.zip
wget http://images.cocodataset.org/zips/test2015.zip && unzip test2015.zip

mkdir -p annotations && cd annotations/
# 변환된 주석 파일 다운로드
wget https://github.com/OpenGVLab/InternVL/releases/download/data/coco_karpathy_test.json
wget https://github.com/OpenGVLab/InternVL/releases/download/data/coco_karpathy_test_gt.json

cd ../../../
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/coco
├── annotations
│ ├── coco_karpathy_test.json
│ └── coco_karpathy_test_gt.json
├── train2014
├── val2014
└── test2015
```

### Flickr30K Karpathy 테스트

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/flickr30k && cd data/flickr30k

# https://bryanplummer.com/Flickr30kEntities/ 에서 이미지 다운로드
# karpathy 분할 주석은 다음 링크에서 다운로드할 수 있습니다.
# https://github.com/mehdidc/retrieval_annotations/releases/download/1.0.0/flickr30k_test_karpathy.txt
# 이 파일은 clip-benchmark 저장소에서 제공됩니다.
# 이 txt 파일을 json 형식으로 변환하고 변환된 파일을 다운로드합니다.
wget https://github.com/OpenGVLab/InternVL/releases/download/data/flickr30k_test_karpathy.json

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/flickr30k
├── Images
├── flickr30k_test_karpathy.txt
└── flickr30k_test_karpathy.json
```

### NoCaps val

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/nocaps && cd data/nocaps

# https://nocaps.org/download 에서 이미지 다운로드
# 원본 주석은 https://nocaps.s3.amazonaws.com/nocaps_val_4500_captions.json 에서 다운로드할 수 있습니다.
wget https://nocaps.s3.amazonaws.com/nocaps_val_4500_captions.json

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/nocaps
├── images
└── nocaps_val_4500_captions.json
```

## 일반 VQA 벤치마크

### VQAv2 val & test-dev

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/vqav2 && cd data/vqav2

# COCO 이미지를 다운로드했는지 확인하세요.
ln -s ../coco/train2014 ./
ln -s ../coco/val2014 ./
ln -s ../coco/test2015 ./

# 질문 및 주석 다운로드
wget https://s3.amazonaws.com/cvmlp/vqa/mscoco/vqa/v2_Annotations_Train_mscoco.zip && unzip v2_Annotations_Train_mscoco.zip
wget https://s3.amazonaws.com/cvmlp/vqa/mscoco/vqa/v2_Questions_Train_mscoco.zip && unzip v2_Questions_Train_mscoco.zip
wget https://s3.amazonaws.com/cvmlp/vqa/mscoco/vqa/v2_Annotations_Val_mscoco.zip && unzip v2_Annotations_Val_mscoco.zip
wget https://s3.amazonaws.com/cvmlp/vqa/mscoco/vqa/v2_Questions_Val_mscoco.zip && unzip v2_Questions_Val_mscoco.zip
wget https://s3.amazonaws.com/cvmlp/vqa/mscoco/vqa/v2_Questions_Test_mscoco.zip && unzip v2_Questions_Test_mscoco.zip

# 변환된 파일 다운로드
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/vqav2/vqav2_train.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/vqav2/vqav2_val.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/vqav2/vqav2_testdev.jsonl

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/vqav2
├── train2014 -> ../coco/train2014
├── val2014 -> ../coco/val2014
├── test2015 -> ../coco/test2015
├── v2_mscoco_train2014_annotations.json
├── v2_mscoco_train2014_complementary_pairs.json
├── v2_mscoco_val2014_annotations.json
├── v2_OpenEnded_mscoco_test2015_questions.json
├── v2_OpenEnded_mscoco_test-dev2015_questions.json
├── v2_OpenEnded_mscoco_train2014_questions.json
├── v2_OpenEnded_mscoco_val2014_questions.json
├── vqav2_testdev.jsonl
├── vqav2_train.jsonl
└── vqav2_val.jsonl
```

### OKVQA val

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/okvqa && cd data/okvqa

# COCO 이미지를 다운로드했는지 확인하세요.
ln -s ../coco/train2014 ./
ln -s ../coco/val2014 ./

# 주석 및 질문 다운로드
wget https://okvqa.allenai.org/static/data/mscoco_train2014_annotations.json.zip && unzip mscoco_train2014_annotations.json.zip
wget https://okvqa.allenai.org/static/data/OpenEnded_mscoco_train2014_questions.json.zip && unzip OpenEnded_mscoco_train2014_questions.json.zip
wget https://okvqa.allenai.org/static/data/mscoco_val2014_annotations.json.zip && unzip mscoco_val2014_annotations.json.zip
wget https://okvqa.allenai.org/static/data/OpenEnded_mscoco_val2014_questions.json.zip && unzip OpenEnded_mscoco_val2014_questions.json.zip

# 변환된 파일 다운로드
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/okvqa/okvqa_train.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/okvqa/okvqa_val.jsonl

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.


```
data/okvqa
├── mscoco_train2014_annotations.json
├── mscoco_val2014_annotations.json
├── okvqa_train.jsonl
├── okvqa_val.jsonl
├── OpenEnded_mscoco_train2014_questions.json
├── OpenEnded_mscoco_val2014_questions.json
├── test2014 -> ../coco/test2014
└── val2014 -> ../coco/val2014
```


### TextVQA val

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/textvqa && cd data/textvqa

# 이미지 다운로드
wget https://dl.fbaipublicfiles.com/textvqa/images/train_val_images.zip && unzip train_val_images.zip

# 변환된 파일 다운로드
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/textvqa/textvqa_train_annotations.json
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/textvqa/textvqa_train_questions.json
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/textvqa/textvqa_train.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/textvqa/textvqa_val_annotations.json
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/textvqa/textvqa_val_questions.json
wget https://huggingface.co/OpenGVLab/InternVL/raw/main/textvqa_val.jsonl
wget https://huggingface.co/OpenGVLab/InternVL/raw/main/textvqa_val_llava.jsonl


cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/textvqa
├── TextVQA_Rosetta_OCR_v0.2_test.json
├── TextVQA_Rosetta_OCR_v0.2_train.json
├── TextVQA_Rosetta_OCR_v0.2_val.json
├── textvqa_train_annotations.json
├── textvqa_train.jsonl
├── textvqa_train_questions.json
├── textvqa_val_annotations.json
├── textvqa_val.jsonl
├── textvqa_val_llava.jsonl
├── textvqa_val_questions.json
└── train_images
```

### VizWiz val & test

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/vizwiz && cd data/vizwiz

# 이미지 다운로드
wget https://vizwiz.cs.colorado.edu/VizWiz_final/images/train.zip && unzip train.zip
wget https://vizwiz.cs.colorado.edu/VizWiz_final/images/val.zip && unzip val.zip
wget https://vizwiz.cs.colorado.edu/VizWiz_final/images/test.zip && unzip test.zip

# 주석 다운로드
wget https://vizwiz.cs.colorado.edu/VizWiz_final/vqa_data/Annotations.zip && unzip Annotations.zip

# 변환된 파일 다운로드
# train
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/vizwiz/vizwiz_train_annotations.json
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/vizwiz/vizwiz_train_questions.json
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/vizwiz/vizwiz_train.jsonl
# val
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/vizwiz/vizwiz_val_annotations.json
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/vizwiz/vizwiz_val_questions.json
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/vizwiz/vizwiz_val.jsonl
# test
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/vizwiz/vizwiz_test.jsonl
cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/vizwiz
├── annotations
├── test
├── train
├── val
├── vizwiz_test.jsonl
├── vizwiz_train_annotations.json
├── vizwiz_train.jsonl
├── vizwiz_train_questions.json
├── vizwiz_val_annotations.json
├── vizwiz_val.jsonl
└── vizwiz_val_questions.json
```

### DocVQA val & test

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/docvqa && cd data/docvqa

# 이미지 및 주석 다운로드
wget https://datasets.cvc.uab.es/rrc/DocVQA/train.tar.gz --no-check-certificate # (선택사항)
wget https://datasets.cvc.uab.es/rrc/DocVQA/val.tar.gz --no-check-certificate
wget https://datasets.cvc.uab.es/rrc/DocVQA/test.tar.gz --no-check-certificate

# 파일 압축 해제
tar -zxvf train.tar.gz
tar -zxvf val.tar.gz
tar -zxvf test.tar.gz

# 변환된 jsonl 파일 다운로드
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/docvqa/train.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/docvqa/val.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/docvqa/test.jsonl
cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/docvqa
├── test
├── test.jsonl
├── train
├── train.jsonl
├── val
└── val.jsonl
```

### InfoVQA val & test

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/infographicsvqa && cd data/infographicsvqa

# https://rrc.cvc.uab.es/?ch=17&com=downloads 에서 이미지 및 주석 다운로드
# infographicsVQA_test_v1.0.json, infographicsVQA_val_v1.0_withQT.json, infographicVQA_train_v1.0.json

# 변환된 파일 다운로드
wget https://huggingface.co/OpenGVLab/InternVL/raw/main/infographicsvqa_val.jsonl -O val.jsonl
wget https://huggingface.co/OpenGVLab/InternVL/raw/main/infographicsvqa_test.jsonl -O test.jsonl

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/infographicsvqa
├── infographicsvqa_images
├── infographicsVQA_test_v1.0.json
├── infographicsVQA_val_v1.0_withQT.json
├── infographicVQA_train_v1.0.json
├── test.jsonl
└── val.jsonl
```

### ChartQA test-human & test-augmented

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/chartqa && cd data/chartqa

# https://drive.google.com/file/d/1Lm_w6zeET1Hyl_9ks6w5nEsgpoyPHalV/view 에서 이미지 다운로드

# 변환된 파일 다운로드
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/chartqa/train_human.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/chartqa/train_augmented.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/chartqa/test_human.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/chartqa/test_augmented.jsonl

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/chartqa
├── ChartQA Dataset
│ ├── test
│ ├── train
│ └── val
├── test_augmented.jsonl
├── test_human.jsonl
├── train_augmented.jsonl
└── train_human.jsonl
```

### GQA testdev

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/gqa && cd data/gqa

# 이미지 다운로드
wget https://downloads.cs.stanford.edu/nlp/data/gqa/images.zip
unzip images.zip

# 변환된 파일 다운로드
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/gqa/testdev_balanced.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/gqa/train_balanced.jsonl
wget https://github.com/OpenGVLab/InternVL/releases/download/data/llava_gqa_testdev_balanced_qwen_format.jsonl


cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/gqa
├── challenge_all_questions.json
├── challenge_balanced_questions.json
├── eval.py
├── images
├── llava_gqa_testdev_balanced_qwen_format.jsonl
├── readme.txt
├── submission_all_questions.json
├── test_all_questions.json
├── test_balanced.jsonl
├── test_balanced_questions.json
├── testdev_all_questions.json
├── testdev_balanced_all_questions.json
├── testdev_balanced_predictions.json
├── testdev_balanced_questions.json
├── train_all_questions
├── train_balanced.jsonl
├── train_balanced_questions.json
├── val_all_questions.json
└── val_balanced_questions.json
```

### OCRVQA val & test

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/ocrvqa && cd data/ocrvqa

# https://ocr-vqa.github.io/kvqa_ProjectFiles/README.txt 의 지침에 따라 이미지 다운로드

# 변환된 파일 다운로드
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/ocrvqa/ocrvqa_train.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/ocrvqa/ocrvqa_val.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/ocrvqa/ocrvqa_test.jsonl

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/ocrvqa
├── images
├── ocrvqa_test.jsonl
├── ocrvqa_train.jsonl
└── ocrvqa_val.jsonl
```

### AI2D test

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/ai2diagram && cd data/ai2diagram
# 변환된 파일 다운로드
wget https://huggingface.co/OpenGVLab/InternVL/raw/main/ai2d_test_vlmevalkit.jsonl -O test_vlmevalkit.jsonl
wget https://huggingface.co/OpenGVLab/InternVL/resolve/main/AI2D_TEST.zip && unzip AI2D_TEST.zip

# Google 드라이브에서 이미지 다운로드 (선택사항, InternLM-XComposer에서 제공)
# https://drive.google.com/file/d/1dqqa3MnrxMXaU_K9JA6C83je32ibwdOY/view?usp=sharing
# 이미지는 `data/ai2diagram/ai2d/abc_images` 및 `data/ai2diagram/ai2d/images`에 배치해야 합니다.
cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/ai2diagram
├── test_vlmevalkit.jsonl
├── ai2d # (선택사항)
│ ├── abc_images
│ └── images
└── AI2D_TEST
```

### ScienceQA test

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/scienceqa/images && cd data/scienceqa/images

# 이미지 다운로드
wget https://scienceqa.s3.us-west-1.amazonaws.com/images/test.zip && unzip test.zip

cd ..

# 원본 질문 다운로드
wget https://github.com/lupantech/ScienceQA/blob/main/data/scienceqa/problems.json

# 변환된 파일 다운로드
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/scienceqa/scienceqa_test_img.jsonl

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/scienceqa
├── images
├── problems.json
└── scienceqa_test_img.jsonl
```

## 참조 표현 이해

### RefCOCO/RefCOCO+/RefCOCO-g

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/refcoco && cd data/refcoco

# 변환된 파일 다운로드
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/refcoco/refcoco_val.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/refcoco/refcoco_testA.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/refcoco/refcoco_testB.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/refcoco%2B/refcoco%2B_val.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/refcoco%2B/refcoco%2B_testA.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/refcoco%2B/refcoco%2B_testB.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/refcocog/refcocog_val.jsonl
wget https://ofasys-wlcb.oss-cn-wulanchabu.aliyuncs.com/Qwen-VL/evaluation/refcocog/refcocog_test.jsonl

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/refcoco
├── refcocog_test.jsonl
├── refcocog_val.jsonl
├── refcoco_testA.jsonl
├── refcoco+_testA.jsonl
├── refcoco_testB.jsonl
├── refcoco+_testB.jsonl
├── refcoco_val.jsonl
└── refcoco+_val.jsonl
```

## 멀티모달 벤치마크

### MME

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/mme && cd data/mme

# 1. 공식 지침 [여기](https://github.com/BradyFU/Awesome-Multimodal-Large-Language-Models/tree/Evaluation)에 따라 데이터를 다운로드합니다.
# 2. 다운로드한 이미지를 `MME_Benchmark_release_version`에 넣습니다.

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/mme
└── MME_Benchmark_release_version
```

### MMBench & CCBench

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/mmbench && cd data/mmbench

# mmbench의 csv 파일 다운로드
wget http://opencompass.openxlab.space/utils/MMBench/CCBench_legacy.tsv
wget https://download.openmmlab.com/mmclassification/datasets/mmbench/mmbench_dev_20230712.tsv
wget https://download.openmmlab.com/mmclassification/datasets/mmbench/mmbench_dev_cn_20231003.tsv
wget https://download.openmmlab.com/mmclassification/datasets/mmbench/mmbench_dev_en_20231003.tsv
wget https://download.openmmlab.com/mmclassification/datasets/mmbench/mmbench_test_cn_20231003.tsv
wget https://download.openmmlab.com/mmclassification/datasets/mmbench/mmbench_test_en_20231003.tsv

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.


```
data/mmbench
├── CCBench_legacy.tsv
├── mmbench_dev_20230712.tsv
├── mmbench_dev_cn_20231003.tsv
├── mmbench_dev_en_20231003.tsv
├── mmbench_test_cn_20231003.tsv
└── mmbench_test_en_20231003.tsv
```


### POPE

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/pope && cd data/pope

# COCO 이미지를 다운로드했는지 확인하세요.
ln -s ../coco/val2014 ./
wget https://github.com/OpenGVLab/InternVL/releases/download/data/llava_pope_test.jsonl

# POPE에서 `coco` 다운로드
mkdir -p coco && cd coco
wget https://github.com/AoiDragon/POPE/raw/e3e39262c85a6a83f26cf5094022a782cb0df58d/output/coco/coco_pope_adversarial.json
wget https://github.com/AoiDragon/POPE/raw/e3e39262c85a6a83f26cf5094022a782cb0df58d/output/coco/coco_pope_popular.json
wget https://github.com/AoiDragon/POPE/raw/e3e39262c85a6a83f26cf5094022a782cb0df58d/output/coco/coco_pope_random.json
cd ../../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/pope
├── coco
├── llava_pope_test.jsonl
└── val2014
```

### MMMU

평가 코드가 Hugging Face에서 데이터셋을 자동으로 다운로드합니다.

### Tiny LVLM

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/tiny_lvlm && cd data/tiny_lvlm

# https://github.com/OpenGVLab/Multi-Modality-Arena/tree/main/tiny_lvlm_evaluation 에서 데이터셋 다운로드
# 즉, https://drive.google.com/file/d/1PuFC612XzOmKwzRldtBb1CFZnIjiR7we/view 에서 `updated_datasets.tar.gz` 다운로드
tar -xzvf updated_datasets.tar.gz

cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/tiny_lvlm
└── updated_datasets
```

### MMVet

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/mm-vet && cd data/mm-vet
wget https://github.com/yuweihao/MM-Vet/releases/download/v1/mm-vet.zip
unzip mm-vet.zip
wget https://huggingface.co/OpenGVLab/InternVL/raw/main/llava-mm-vet.jsonl
cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/mm-vet
├── images
└── llava-mm-vet.jsonl
```


### MMVP

다음 지침에 따라 데이터를 준비하세요.

```bash
cd data
git lfs install
git clone https://huggingface.co/datasets/MMVP/MMVP
cd ..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/MMVP
├── MMVP\ Images
├── Questions.csv
├── Questions.xlsx
└── README.md
```

### MathVista

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/MathVista && cd data/MathVista
wget https://huggingface.co/datasets/AI4Math/MathVista/raw/main/annot_testmini.json
cd ../..
```

준비가 완료되면 디렉토리 구조는 다음과 같습니다.

```
MathVista
└── annot_testmini.json
```

### SEED

다음 지침에 따라 데이터를 준비하세요.

```bash
mkdir -p data/SEED && cd data/SEED
# 1. SEED-Bench-1에 대한 공식 지침 [데이터 준비 방법](https://github.com/AILab-CVC/SEED-Bench/blob/main/DATASET.md#data-preparation-for-seed-bench-1)에 따라
# 이미지와 비디오를 다운로드합니다. 이미지는 `./data/SEED/SEED-Bench-image` 아래에 넣습니다.
# 2. 다운로드한 비디오에서 중간의 비디오 프레임을 추출하고 `./data/SEED/SEED-Bench-image` 아래에 넣습니다.
# LLaVA는 공식 스크립트를 수정한 [`extract_video_frames.py`](../internvl_chat/tools/extract_video_frames.py) 스크립트를 제공했습니다.

wget https://huggingface.co/OpenGVLab/InternVL/raw/main/seed.jsonl
cd ../..
```

준비가 완료되면 디렉터리 구조는 다음과 같습니다.

```
data/SEED
├── SEED-Bench-image
└── seed.jsonl
```
