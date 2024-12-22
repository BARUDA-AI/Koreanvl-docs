# InternVL2 시리즈 퀵 스타트

`transformers`를 사용하여 InternVL2 시리즈를 실행하는 예제 코드를 제공합니다.

또한 [온라인 데모](https://internvl.opengvlab.com/)에서 InternVL2 시리즈 모델을 경험해 보시기 바랍니다.

> 모델이 정상적으로 작동하려면 transformers==4.37.2를 사용하세요.

## 모델 준비

| 모델 이름           | 타입 | 파라미터 | 다운로드                                                            |  크기  |
| -------------------- | ---- | ----- | ------------------------------------------------------------------- | :----: |
| InternVL2-1B         | MLLM | 0.9B  | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-1B)         | 1.8 GB |
| InternVL2-2B         | MLLM | 2.2B  | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-2B)         | 4.2 GB |
| InternVL2-4B         | MLLM | 4.2B  | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-4B)         | 7.8 GB |
| InternVL2-8B         | MLLM | 8.1B  | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-8B)         | 16 GB  |
| InternVL2-26B        | MLLM | 25.5B | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-26B)        | 48 GB  |
| InternVL2-40B        | MLLM | 40.1B | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-40B)        | 75 GB  |
| InternVL2-Llama3-76B | MLLM | 76.3B | 🤗 [HF 링크](https://huggingface.co/OpenGVLab/InternVL2-Llama3-76B) | 143 GB |

필요에 따라 위 모델 가중치를 다운로드하여 `pretrained/` 폴더에 넣으십시오.

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

### 모델 로딩

#### 16-bit (bf16 / fp16)

`````{tabs}

````{tab} 1B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-1B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval().cuda()
```
````

````{tab} 2B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-2B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval().cuda()
```
````

````{tab} 4B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-4B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval().cuda()
```
````

````{tab} 8B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-8B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval().cuda()
```
````

````{tab} 26B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-26B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval().cuda()
```
````

````{tab} 40B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-40B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval().cuda()
```
````

````{tab} 76B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-Llama3-76B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval().cuda()
```
````

`````

#### BNB 8-bit 양자화

`````{tabs}

````{tab} 1B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-1B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    load_in_8bit=True,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval()
```

````

````{tab} 2B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-2B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    load_in_8bit=True,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval()
```

````

````{tab} 4B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-4B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    load_in_8bit=True,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval()
```

````

````{tab} 8B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-8B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    load_in_8bit=True,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval()
```

````

````{tab} 26B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-26B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    load_in_8bit=True,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval()
```

````

````{tab} 40B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-40B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    load_in_8bit=True,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval()
```

````

````{tab} 76B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-Llama3-76B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    load_in_8bit=True,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval()
```

````

`````

#### BNB 4-bit 양자화

`````{tabs}

````{tab} 1B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-1B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    load_in_4bit=True,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval()
```

````

````{tab} 2B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-2B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    load_in_4bit=True,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval()
```

````

````{tab} 4B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-4B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    load_in_4bit=True,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval()
```

````

````{tab} 8B

```python
import torch
from transformers import AutoTokenizer, AutoModel
path = "OpenGVLab/InternVL2-8B"
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    load_in_4bit=True,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True).eval()
```

````

````{tab} 26B

> **⚠️ 경고:** InternViT-6B에서 BNB 4-bit 양자화의 심각한 양자화 오류로 인해 모델이 비정상적인 출력을 생성하고 이미지를 이해하지 못할 수 있습니다. 따라서 BNB 4-bit 양자화를 사용하지 마십시오.

````

````{tab} 40B

> **⚠️ 경고:** InternViT-6B에서 BNB 4-bit 양자화의 심각한 양자화 오류로 인해 모델이 비정상적인 출력을 생성하고 이미지를 이해하지 못할 수 있습니다. 따라서 BNB 4-bit 양자화를 사용하지 마십시오.

````

````{tab} 76B

> **⚠️ 경고:** InternViT-6B에서 BNB 4-bit 양자화의 심각한 양자화 오류로 인해 모델이 비정상적인 출력을 생성하고 이미지를 이해하지 못할 수 있습니다. 따라서 BNB 4-bit 양자화를 사용하지 마십시오.

````

`````

#### 다중 GPU

코드를 이렇게 작성하는 이유는 다중 GPU 추론 중에 텐서가 동일한 장치에 있지 않아 발생하는 오류를 피하기 위함입니다. 대규모 언어 모델(LLM)의 첫 번째 레이어와 마지막 레이어가 동일한 장치에 있도록 보장함으로써 이러한 오류를 방지합니다.

```python
import math
import torch
from transformers import AutoTokenizer, AutoModel

def split_model(model_name):
    device_map = {}
    world_size = torch.cuda.device_count()
    num_layers = {
        'InternVL2-1B': 24, 'InternVL2-2B': 24, 'InternVL2-4B': 32, 'InternVL2-8B': 32,
        'InternVL2-26B': 48, 'InternVL2-40B': 60, 'InternVL2-Llama3-76B': 80}[model_name]
    # 첫 번째 GPU는 ViT에 사용되므로 절반의 GPU로 취급합니다.
    num_layers_per_gpu = math.ceil(num_layers / (world_size - 0.5))
    num_layers_per_gpu = [num_layers_per_gpu] * world_size
    num_layers_per_gpu[0] = math.ceil(num_layers_per_gpu[0] * 0.5)
    layer_cnt = 0
    for i, num_layer in enumerate(num_layers_per_gpu):
        for j in range(num_layer):
            device_map[f'language_model.model.layers.{layer_cnt}'] = i
            layer_cnt += 1
    device_map['vision_model'] = 0
    device_map['mlp1'] = 0
    device_map['language_model.model.tok_embeddings'] = 0
    device_map['language_model.model.embed_tokens'] = 0
    device_map['language_model.output'] = 0
    device_map['language_model.model.norm'] = 0
    device_map['language_model.lm_head'] = 0
    device_map[f'language_model.model.layers.{num_layers - 1}'] = 0

    return device_map
```

`````{tabs}

````{tab} 1B
```python
path = "OpenGVLab/InternVL2-1B"
device_map = split_model('InternVL2-1B')
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True,
    device_map=device_map).eval()
```
````

````{tab} 2B
```python
path = "OpenGVLab/InternVL2-2B"
device_map = split_model('InternVL2-2B')
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True,
    device_map=device_map).eval()
```
````

````{tab} 4B
```python
path = "OpenGVLab/InternVL2-4B"
device_map = split_model('InternVL2-4B')
model = AutoModel.from_pretrained(
    path,
    torch_dtype=torch.bfloat16,
    low_cpu_mem_usage=True,
    use_flash_attn=True,
    trust_remote_code=True,
    device_map=device_map).eval()
```
````

````{tab} 8B
```python
path = "OpenGVLab/InternVL2-8B"
device_map = split_model('InternVL2-8B')
model = AutoModel.from_pretrained(
    path,
    torch
