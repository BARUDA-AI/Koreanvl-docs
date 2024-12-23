# 로컬 채팅 데모

이 문서는 InternVL 온라인 데모를 설정하는 방법에 대한 지침을 제공합니다. 시스템 아키텍처에는 웹 서버, 컨트롤러 및 여러 모델 워커가 포함됩니다.

## Streamlit 데모

`Streamlit`을 사용하여 [새로운 온라인 데모](https://internvl.opengvlab.com/)를 구축했습니다. 아래 그림과 같습니다.

![demo](./streamlit_demo.png)

아래 명령을 사용하여 실행할 수 있습니다. 환경 문제가 발생하면 필요한 종속성을 설치하십시오.

```bash
pip install -r requirements/streamlit_demo.txt
```

### 1단계: 변수 설정

터미널에서 다음 변수를 설정합니다.

```bash
export SD_SERVER_PORT=39999
export WEB_SERVER_PORT=10003
export CONTROLLER_PORT=40000
export CONTROLLER_URL=http://0.0.0.0:$CONTROLLER_PORT
export SD_WORKER_URL=http://0.0.0.0:$SD_SERVER_PORT
```

### 2단계: Streamlit 웹 서버 시작

다음 명령을 실행하여 `$WEB_SERVER_PORT` 포트에서 Streamlit 웹 서버를 시작합니다.

```bash
streamlit run app.py --server.port WEBSERVERPORT−−−−controllerurlWEB_SERVER_PORT -- --controller_url CONTROLLER_URL --sd_worker_url $SD_WORKER_URL
```

참고: `-- --`는 오타가 아니며 필수입니다.

### 3단계: 컨트롤러 시작

다음 명령을 실행하여 `$CONTROLLER_PORT` 포트에서 컨트롤러를 시작합니다.

```bash
python controller.py --host 0.0.0.0 --port $CONTROLLER_PORT
```

### 4단계: 모델 워커 시작

#### (선택 사항) Stable Diffusion 3 워커

그리기 기능을 활성화하려면 다음 명령을 실행하여 `$SD_SERVER_PORT` 포트에서 Stable Diffusion 3 워커를 시작합니다.

```bash
CUDA_VISIBLE_DEVICES=0 python sd_worker.py --port $SD_SERVER_PORT
```

#### InternVL2 워커

다음 명령을 실행하여 다양한 모델 크기로 다른 InternVL2 워커를 시작합니다.

- **InternVL2-1B 워커** (포트 40001):

  ```bash
  CUDA_VISIBLE_DEVICES=0 python model_worker.py --host 0.0.0.0 --controller $CONTROLLER_URL --port 40001 --worker http://0.0.0.0:40001 --model-path OpenGVLab/InternVL2-1B
  ```

- **InternVL2-2B 워커** (포트 40002):

  ```bash
  CUDA_VISIBLE_DEVICES=0 python model_worker.py --host 0.0.0.0 --controller $CONTROLLER_URL --port 40002 --worker http://0.0.0.0:40002 --model-path OpenGVLab/InternVL2-2B
  ```

- **InternVL2-4B 워커** (포트 40003):

  ```bash
  CUDA_VISIBLE_DEVICES=0 python model_worker.py --host 0.0.0.0 --controller $CONTROLLER_URL --port 40003 --worker http://0.0.0.0:40003 --model-path OpenGVLab/InternVL2-4B
  ```

- **InternVL2-8B 워커** (포트 40004):

  ```bash
  CUDA_VISIBLE_DEVICES=1 python model_worker.py --host 0.0.0.0 --controller $CONTROLLER_URL --port 40004 --worker http://0.0.0.0:40004 --model-path OpenGVLab/InternVL2-8B
  ```

- **InternVL2-26B 워커** (포트 40005):

  ```bash
  CUDA_VISIBLE_DEVICES=2 python model_worker.py --host 0.0.0.0 --controller $CONTROLLER_URL --port 40005 --worker http://0.0.0.0:40005 --model-path OpenGVLab/InternVL2-26B
  ```

- **InternVL2-40B 워커** (포트 40006, GPU 2개 사용):

  ```bash
  CUDA_VISIBLE_DEVICES=3,4 python model_worker.py --host 0.0.0.0 --controller $CONTROLLER_URL --port 40006 --worker http://0.0.0.0:40006 --model-path OpenGVLab/InternVL2-40B --device auto
  ```

- **InternVL2-Llama3-76B 워커** (포트 40007, GPU 3개 사용):

  ```bash
  CUDA_VISIBLE_DEVICES=5,6,7 python model_worker.py --host 0.0.0.0 --controller $CONTROLLER_URL --port 40007 --worker http://0.0.0.0:40007 --model-path OpenGVLab/InternVL2-Llama3-76B --device auto
  ```

## Gradio 데모

TODO

## LMDeploy 데모

TODO

<br>
<br>
