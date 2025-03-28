# LLM Inference Engine Benchmark
> 다양한 오픈소스 LLM 추론 프레임워크를 사용하여 `google/gemma-3-4b-it` 모델 기준 성능을 측정하고 비교한 리포트입니다.
최근의 LLM 환경에서는 모델 자체보다 **어떻게 빠르고 효율적으로 서빙할 것인가**가 점점 더 중요한 문제가 되고 있습니다.  
**추론 속도, 응답 시간, GPU 효율성**을 기준으로 여러 오픈소스를 비교하였습니다.

<br/>

## 실험 환경

| 항목             | 사양 / 버전                      |
|------------------|----------------------------------|
| GPU              | 4x NVIDIA RTX 3090 (24GB each)   |
| CUDA / Driver    | CUDA 12.4 / Driver 550.120       |
| Python           | 3.11                             |
| 모델             | `google/gemma-3-4b-it`           |
| 실험 반복 횟수   | 각 환경당 최소 5회 반복 평균 측정 |

> 실험은 동일한 입력 조건, 동일한 프롬프트, 동일한 하드웨어 리소스에서 수행하였습니다.  
> 프롬프트는 실제 서비스에서 사용할 법한 다양한 길이와 복잡도를 갖도록 구성하였습니다.

<br/>

## 측정 지표 (Performance Metrics)

### 1. TTFT (Time To First Token)

- 모델이 첫 토큰을 응답하기까지 걸리는 시간 (초).
- 실사용 시 사용자 체감 속도에 가장 직접적인 영향을 줍니다.
- warm-up 시간 포함.

### 2. TPS (Tokens Per Second)

- 전체 토큰 처리 속도:  
  `TPS = (Input Tokens + Output Tokens) / Total Time`

### 3. GPU Usage

- **Volatile GPU Utilization (%)**  
  모델이 얼마나 GPU 자원을 활용하고 있는지 측정 (peak, 평균, p90 기준).
- **GPU Memory Usage (MB)**  
  모델 로딩 시 기본 점유 메모리, 생성 중 peak 메모리 사용량 측정.

<br/>

## 테스트 Runtime 목록

| runtime         | TPS | TTFT | GPU Memory (MB) | GPU Util (%) | 사용성 | 모델 지원 |
|-----------------|-----|------|------------------|---------------|--------|------------|
| DeepSpeed-MII   |     |      |                  |               |        |            |
| tensorRT-LLM    |     |      |                  |               |        |            |
| vllm            |     |      |                  |               |        |            |


<br/>
## 테스트 구성

- **Prompt 다양성 확보**
  - 짧은 프롬프트, 중간 길이, 10K 토큰급 긴 문서 입력 포함
  - 질의 복잡도 다양화 (단답형, 요약, 분석 등)
  - 실제 유저 사용 예시 기반 시나리오 포함

- **Tokenization 차이 주의**
  - SentencePiece vs TikToken 등 tokenizer 종류에 따라 토큰 수 편차가 존재
  - 동일 입력에서도 성능 차이 발생 가능 → 비교 시 주의

- **실험 신뢰도 확보**
  - 각 프레임워크별 최소 5회 이상 반복 실행
