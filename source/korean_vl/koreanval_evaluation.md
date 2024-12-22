# InternVL2 시리즈 평가

다양한 작업에 걸쳐 InternVL2 시리즈의 성능을 평가하려면 각 특정 데이터 세트에 대한 지침을 따르십시오. 지정된 대로 적절한 수의 GPU가 할당되었는지 확인하십시오.

> 1⃣️ 모델 평가를 위해 InternVL 및 VLMEvalKit 저장소를 동시에 사용합니다. 특히 DocVQA, ChartQA, InfoVQA, TextVQA, MME, AI2D, MMBench, CCBench, MMVet 및 SEED-Image에 대해 보고된 결과는 InternVL 저장소를 사용하여 테스트되었습니다. OCRBench, RealWorldQA, HallBench 및 MathVista는 VLMEvalKit을 사용하여 평가되었습니다.

> 2⃣️ InternVL 및 VLMEvalKit과 같은 서로 다른 테스트 툴킷을 사용하여 동일한 모델을 평가하면 약간의 차이가 발생할 수 있으며 이는 정상입니다. 코드 버전 업데이트 및 환경과 하드웨어의 변화 또한 결과에 약간의 차이를 유발할 수 있습니다.

> 3⃣️️ 참고, 데이터 세트 설명은 GPT-4에 의해 생성되었으며 오류가 포함될 수 있습니다.

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

평가하기 전에 제공된 학습된 모델을 다운로드하십시오.

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

디렉토리 구조는 다음과 같습니다.

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

## InternVL 코드베이스를 사용한 평가

### 데이터 준비

[여기에 제공된 지침](../get_started/eval_data_preparation.md)에 따라 평가 데이터를 준비하십시오.

### MME

MME는 다중 모드 대규모 언어 모델(MLLM)을 평가하기 위해 설계된 포괄적인 벤치마크로, 14개의 서로 다른 하위 작업에 걸쳐 인식 및 인지 능력을 모두 평가하여 이러한 모델에 대한 강력하고 다양한 테스트를 보장합니다.

`````{tabs}

````{tab} 1B

1개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=1 sh evaluate.sh pretrained/InternVL2-1B mme --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
=========== 인식 ===========
총점: 1346.1990796318528

         존재  점수: 175.0
         개수  점수: 113.33333333333334
         위치  점수: 135.0
         색상  점수: 138.33333333333331
         포스터  점수: 116.32653061224491
         유명인  점수: 144.70588235294116
         장면  점수: 143.25
         랜드마크  점수: 128.5
         예술작품  점수: 141.75
         OCR  점수: 110.0

=========== 인지 ===========
총점: 448.2142857142857

         상식적 추론  점수: 95.71428571428571
         수치 계산  점수: 57.5
         텍스트 번역  점수: 177.5
         코드 추론  점수: 117.5
```

````

````{tab} 2B

1개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=1 sh evaluate.sh pretrained/InternVL2-2B mme --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
=========== 인식 ===========
총점: 1439.6688675470189

         존재  점수: 200.0
         개수  점수: 128.33333333333334
         위치  점수: 145.0
         색상  점수: 163.33333333333334
         포스터  점수: 131.97278911564626
         유명인  점수: 118.52941176470588
         장면  점수: 157.0
         랜드마크  점수: 154.0
         예술작품  점수: 146.5
         OCR  점수: 95.0

=========== 인지 ===========
총점: 437.1428571428571

         상식적 추론  점수: 112.14285714285714
         수치 계산  점수: 45.0
         텍스트 번역  점수: 177.5
         코드 추론  점수: 102.5
```

````

````{tab} 4B

1개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=1 sh evaluate.sh pretrained/InternVL2-4B mme --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
=========== 인식 ===========
총점: 1532.31662665066

         존재  점수: 200.0
         개수  점수: 123.33333333333333
         위치  점수: 148.33333333333331
         색상  점수: 165.0
         포스터  점수: 155.78231292517006
         유명인  점수: 124.11764705882354
         장면  점수: 158.75
         랜드마크  점수: 165.0
         예술작품  점수: 144.5
         OCR  점수: 147.5

=========== 인지 ===========
총점: 531.7857142857142

         상식적 추론  점수: 129.28571428571428
         수치 계산  점수: 115.0
         텍스트 번역  점수: 170.0
         코드 추론  점수: 117.5
```

````

````{tab} 8B

1개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=1 sh evaluate.sh pretrained/InternVL2-8B mme --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
=========== 인식 ===========
총점: 1648.1331532613044

         존재  점수: 190.0
         개수  점수: 158.33333333333331
         위치  점수: 163.33333333333334
         색상  점수: 175.0
         포스터  점수: 167.68707482993196
         유명인  점수: 148.52941176470586
         장면  점수: 152.5
         랜드마크  점수: 176.5
         예술작품  점수: 153.75
         OCR  점수: 162.5

=========== 인지 ===========
총점: 562.1428571428571

         상식적 추론  점수: 147.14285714285714
         수치 계산  점수: 87.5
         텍스트 번역  점수: 192.5
         코드 추론  점수: 135.0
```

````

````{tab} 26B

1개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=1 sh evaluate.sh pretrained/InternVL2-26B mme --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
=========== 인식 ===========
총점: 1720.0325130052022

         존재  점수: 195.0
         개수  점수: 170.0
         위치  점수: 176.66666666666669
         색상  점수: 168.33333333333331
         포스터  점수: 176.87074829931973
         유명인  점수: 159.41176470588235
         장면  점수: 154.0
         랜드마크  점수: 179.5
         예술작품  점수: 162.75
         OCR  점수: 177.5

=========== 인지 ===========
총점: 540.7142857142858

         상식적 추론  점수: 145.71428571428572
         수치 계산  점수: 95.0
         텍스트 번역  점수: 185.0
         코드 추론  점수: 115.0
```

````

````{tab} 40B

8개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-40B mme --dynamic --auto
```

예상되는 테스트 결과는 다음과 같습니다.

```
=========== 인식 ===========
총점: 1715.390456182473

         존재  점수: 185.0
         개수  점수: 175.0
         위치  점수: 158.33333333333331
         색상  점수: 188.33333333333331
         포스터  점수: 187.41496598639458
         유명인  점수: 162.05882352941177
         장면  점수: 152.5
         랜드마크  점수: 180.25
         예술작품  점수: 171.5
         OCR  점수: 155.0

=========== 인지 ===========
총점: 599.6428571428571

         상식적 추론  점수: 152.14285714285714
         수치 계산  점수: 125.0
         텍스트 번역  점수: 177.5
         코드 추론  점수: 145.0
```

````

````{tab} 76B

8개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-Llama3-76B mme --dynamic --auto
```

예상되는 테스트 결과는 다음과 같습니다.

```
=========== 인식 ===========
총점: 1731.095538215286

         존재  점수: 200.0
         개수  점수: 175.0
         위치  점수: 168.33333333333331
         색상  점수: 185.0
         포스터  점수: 186.39455782312925
         유명인  점수: 169.11764705882354
         장면  점수: 152.0
         랜드마크  점수: 182.0
         예술작품  점수: 173.25
         OCR  점수: 140.0

=========== 인지 ===========
총점: 683.5714285714286

         상식적 추론  점수: 158.57142857142856
         수치 계산  점수: 185.0
         텍스트 번역  점수: 177.5
         코드 추론  점수: 162.5
```

````

`````

### OKVQA

OKVQA (Outside Knowledge Visual Question Answering)는 이미지에 보이는 것 이상의 외부 지식을 요구하는 시각적 질문 답변 작업을 위해 설계된 데이터 세트로, AI 모델의 추론 능력을 평가하기 위한 14,000개 이상의 질문을 제공합니다.

`````{tabs}

````{tab} 1B

8개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-1B vqa-okvqa-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
okvqa_val 0.48513674197383483
```

````

````{tab} 2B

8개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-2B vqa-okvqa-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
okvqa_val 0.5316290130796605
```

````

````{tab} 4B

8개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-4B vqa-okvqa-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
okvqa_val 0.6007530717399846
```

````

````{tab} 8B

8개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-8B vqa-okvqa-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
okvqa_val 0.6289734443123187
```

````

````{tab} 26B

8개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-26B vqa-okvqa-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
okvqa_val 0.6594530321046287
```

````

````{tab} 40B

8개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-40B vqa-okvqa-val --dynamic --auto
```

예상되는 테스트 결과는 다음과 같습니다.

```
okvqa_val 0.664288545382473
```

````

````{tab} 76B

8개의 GPU로 테스트를 수행하려면 다음 명령을 사용하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-Llama3-76B vqa-okvqa-val --dynamic --auto
```

예상되는 테스트 결과는 다음과 같습니다.

```
okvqa_val 0.683432421720166
```

````

`````

### TextVQA

TextVQA는 이미지 내에 존재하는 텍스트를 읽고 추론해야 하는 시각적 질문 답변 모델을 평가하기 위해 설계된 데이터 세트로, OpenImages 데이터 세트에서 가져온 28,408개의 이미지에 대한 45,336개의 질문을 포함합니다.

TextVQA 데이터 세트는 공식 OCR 결과, 특히 Rosetta OCR 토큰을 제공합니다. InstructBLIP 및 LLaVA 1.5로 테스트할 때 OCR 결과는 LLM에 프롬프트로 입력됩니다. Rosetta OCR 토큰을 입력하려면 다음 명령을 사용하십시오.

`````{tabs}

````{tab} 1B

Rosetta OCR 토큰을 사용하지 않으려면 이 명령을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-1B vqa-textvqa-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
textvqa_val 0.7052400000000033
```

````

````{tab} 2B

Rosetta OCR 토큰을 사용하지 않으려면 이 명령을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-2B vqa-textvqa-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
textvqa_val 0.7335600000000035
```

````

````{tab} 4B

Rosetta OCR 토큰을 사용하지 않으려면 이 명령을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-4B vqa-textvqa-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
textvqa_val 0.7437000000000039
```

````

````{tab} 8B

Rosetta OCR 토큰을 사용하지 않으려면 이 명령을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-8B vqa-textvqa-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
textvqa_val 0.773740000000004
```

````

````{tab} 26B

Rosetta OCR 토큰을 사용하지 않으려면 이 명령을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-26B vqa-textvqa-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
textvqa_val 0.8228200000000048
```

````

````{tab} 40B

Rosetta OCR 토큰을 사용하지 않으려면 이 명령을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-40B vqa-textvqa-val --dynamic --auto
```

예상되는 테스트 결과는 다음과 같습니다.

```
textvqa_val 0.8301600000000046
```

````

````{tab} 76B

Rosetta OCR 토큰을 사용하지 않으려면 이 명령을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-Llama3-76B vqa-textvqa-val --dynamic --auto
```

예상되는 테스트 결과는 다음과 같습니다.

```
textvqa_val 0.844100000000004
```

````

`````

### VizWiz

VizWiz VQA 데이터 세트는 시각 장애인이 제기한 시각적 질문에 답하는 데 도움이 되도록 만들어진 시각적 질문 답변 데이터 세트입니다. 사용자가 휴대폰을 사용하여 사진을 찍고 그것에 대한 음성 질문을 녹음한 31,000개 이상의 시각적 질문을 포함합니다. 각 질문에는 10개의 크라우드 소싱 답변이 제공됩니다. 이 데이터 세트는 시각적 질문에 대한 답변을 예측하고 시각적 질문에 답할 수 있는지 여부를 결정하는 등의 작업을 다룹니다.

`````{tabs}

````{tab} 1B

유효성 검사 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-1B vqa-vizwiz-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
vizwiz_val 0.5306783977772626
```

테스트 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-1B vqa-vizwiz-test --dynamic
```

테스트 세트의 경우 결과를 [평가 서버](https://eval.ai/web/challenges/challenge-page/2185/overview)에 제출하십시오.

````

````{tab} 2B

유효성 검사 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-2B vqa-vizwiz-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
vizwiz_val 0.47376707571196724
```

테스트 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-2B vqa-vizwiz-test --dynamic
```

테스트 세트의 경우 결과를 [평가 서버](https://eval.ai/web/challenges/challenge-page/2185/overview)에 제출하십시오.

````

````{tab} 4B

유효성 검사 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-4B vqa-vizwiz-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
vizwiz_val 0.622088446399631
```

테스트 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-4B vqa-vizwiz-test --dynamic
```

테스트 세트의 경우 결과를 [평가 서버](https://eval.ai/web/challenges/challenge-page/2185/overview)에 제출하십시오.

````

````{tab} 8B

유효성 검사 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-8B vqa-vizwiz-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
vizwiz_val 0.6290808057420708
```

테스트 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-8B vqa-vizwiz-test --dynamic
```

테스트 세트의 경우 결과를 [평가 서버](https://eval.ai/web/challenges/challenge-page/2185/overview)에 제출하십시오.

````

````{tab} 26B

유효성 검사 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-26B vqa-vizwiz-val --dynamic
```

예상되는 테스트 결과는 다음과 같습니다.

```
vizwiz_val 0.6839083121092873
```

테스트 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-26B vqa-vizwiz-test --dynamic
```

테스트 세트의 경우 결과를 [평가 서버](https://eval.ai/web/challenges/challenge-page/2185/overview)에 제출하십시오.

````

````{tab} 40B

유효성 검사 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-40B vqa-vizwiz-val --dynamic --auto
```

예상되는 테스트 결과는 다음과 같습니다.

```
vizwiz_val 0.6521880064829846
```

테스트 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-40B vqa-vizwiz-test --dynamic --auto
```

테스트 세트의 경우 결과를 [평가 서버](https://eval.ai/web/challenges/challenge-page/2185/overview)에 제출하십시오.

````

````{tab} 76B

유효성 검사 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-Llama3-76B vqa-vizwiz-val --dynamic --auto
```

예상되는 테스트 결과는 다음과 같습니다.

```
vizwiz_val 0.6767075711970381
```

테스트 세트의 경우 다음을 실행하십시오.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-Llama3-76B vqa-vizwiz-test --dynamic --auto
```

테스트 세트의 경우 결과를 [평가 서버](https://eval.ai/web/challenges/challenge-page/2185/overview)에 제출하십시오.

````

`````

### ChartQA

ChartQA 데이터 세트는 차트에 대한 질문에 답변하기 위한 포괄적인 벤치마크로, 시각적 및 논리적 추론을 모두 포함합니다. 9.6K개의 사람이 작성한 질문과 차트 요약에서 파생된 23.1K개의 기계 생성 질문이 혼합되어 있습니다. 이 데이터 세트는 복잡한 질문에 답변하기 위해 차트를 이해하고 분석할 수 있는 모델을 평가하기 위해 설계되었으며, 종종 여러 논리적 및 산술 연산과 차트의 시각적 특징을 참조해야 합니다.

`````{tabs}

````{tab} 1B

ChartQA 데이터 세트에는 `chartqa_test_human` 및 `chartqa_test_augmented`의 두 가지 테스트 세트가 있습니다. 모델 평가를 위한 최종 점수는 이 두 테스트 세트의 점수 평균으로 계산됩니다.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-1B vqa-chartqa-test --dynamic --max-num 12
```

예상되는 테스트 결과는 다음과 같습니다.

```
['chartqa_test_human', {'relaxed_accuracy': 0.5392}]
['chartqa_test_augmented', {'relaxed_accuracy': 0.9184}]

result = (53.92 + 91.84) / 2 = 72.88
```

````

````{tab} 2B

ChartQA 데이터 세트에는 `chartqa_test_human` 및 `chartqa_test_augmented`의 두 가지 테스트 세트가 있습니다. 모델 평가를 위한 최종 점수는 이 두 테스트 세트의 점수 평균으로 계산됩니다.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-2B vqa-chartqa-test --dynamic --max-num 12
```

예상되는 테스트 결과는 다음과 같습니다.

```
['chartqa_test_human', {'relaxed_accuracy': 0.5952}]
['chartqa_test_augmented', {'relaxed_accuracy': 0.9296}]

result = (59.52 + 92.96) / 2 = 76.24
```

````

````{tab} 4B

ChartQA 데이터 세트에는 `chartqa_test_human` 및 `chartqa_test_augmented`의 두 가지 테스트 세트가 있습니다. 모델 평가를 위한 최종 점수는 이 두 테스트 세트의 점수 평균으로 계산됩니다.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-4B vqa-chartqa-test --dynamic --max-num 12
```

예상되는 테스트 결과는 다음과 같습니다.

```
['chartqa_test_human', {'relaxed_accuracy': 0.6992}]
['chartqa_test_augmented', {'relaxed_accuracy': 0.9304}]

result = (69.92 + 93.04) / 2 = 81.48
```

````

````{tab} 8B

ChartQA 데이터 세트에는 `chartqa_test_human` 및 `chartqa_test_augmented`의 두 가지 테스트 세트가 있습니다. 모델 평가를 위한 최종 점수는 이 두 테스트 세트의 점수 평균으로 계산됩니다.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-8B vqa-chartqa-test --dynamic --max-num 12
```

예상되는 테스트 결과는 다음과 같습니다.

```
['chartqa_test_human', {'relaxed_accuracy': 0.7288}]
['chartqa_test_augmented', {'relaxed_accuracy': 0.9368}]

result = (72.88 + 93.68) / 2 = 83.28
```

````

````{tab} 26B

ChartQA 데이터 세트에는 `chartqa_test_human` 및 `chartqa_test_augmented`의 두 가지 테스트 세트가 있습니다. 모델 평가를 위한 최종 점수는 이 두 테스트 세트의 점수 평균으로 계산됩니다.

```bash
GPUS=8 sh evaluate.sh pretrained/InternVL2-26B vqa-chartqa-test --dynamic --max-num 12
```

예상되는 테스트 결과는 다음과 같습니다.

```
['chartqa_test_human', {'relaxed_accuracy': 0.7528}]
['chartqa_test_augmented', {'relaxed_accuracy': 0.9448}]

result = (75.28 + 94.48) / 2 = 84.88
```

````

````{tab} 40B

ChartQA 데이터 세트에는 `chartqa_test_
