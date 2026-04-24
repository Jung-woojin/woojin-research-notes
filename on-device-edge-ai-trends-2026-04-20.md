# 2026 온디바이스 AI / 엣지 AI 트렌드 리포트

기준일: 2026-04-20

이 문서는 2024~2026 기준으로 `온디바이스 AI(on-device AI)`와 `엣지 AI(edge AI)`의 기술·제품·인프라 트렌드를 아주 상세하게 정리한 리포트다.

여기서는 두 용어를 엄밀하게 구분한다.

- `온디바이스 AI`: 스마트폰, 노트북, 태블릿, 브라우저, 웨어러블, 차량 내부 단말처럼 `사용자 가까운 종단 디바이스 자체`에서 모델이 추론되는 구조
- `엣지 AI`: 더 넓은 개념으로, 카메라 게이트웨이, 공장 장비, 점포 서버, 로봇, 차량, 현장 박스형 서버, 기지국 주변 컴퓨트 등 `클라우드보다 현장에 더 가까운 위치`에서 추론되는 구조

즉, 온디바이스 AI는 엣지 AI의 하위 집합으로 볼 수 있다.

---

## 한 줄 요약

지금 AI 시스템은 `무조건 클라우드에서 큰 모델을 돌리는 시대`에서 벗어나고 있다.  
핵심 전환은 다음과 같다.

1. `클라우드 단독`에서 `하이브리드 AI`로 이동
2. `GPU 중심 추론`에서 `NPU/TPU/DLA/전용 가속기 중심 추론`으로 이동
3. `큰 범용 모델 1개`에서 `작은 특화 모델 + 큰 상위 모델` 조합으로 이동
4. `모델 배포`보다 `모델 전달, 캐싱, 최적화, 타게팅, 업데이트`가 더 중요해짐
5. `정확도 경쟁`만이 아니라 `전력, 지연, 메모리, 프라이버시, 가용성`이 동등한 핵심 지표가 됨

짧게 말하면, AI의 중심축이 `training`에서 `inference`, 그리고 그중에서도 `현장 추론`으로 이동 중이다.

---

## 1. 왜 지금 온디바이스 / 엣지 AI가 다시 뜨는가

몇 년 전까지는 온디바이스 AI가 주로 다음 정도를 의미했다.

- 키워드 추천
- 카메라 필터
- 얼굴 인식
- 키보드 자동완성
- 간단한 음성 인식

즉, 대체로 작은 분류기나 경량 CV 모델 중심이었다.

하지만 생성형 AI 이후 상황이 바뀌었다.

이제 사용자는 다음을 기대한다.

- 오프라인에서도 돌아가는 AI
- 개인 데이터를 밖으로 안 보내는 AI
- 카메라와 마이크를 실시간으로 이해하는 AI
- 기기에서 바로 응답하는 초저지연 AI
- 구독 없이 기본 기능이 동작하는 AI
- 화면, 문서, 음성, 이미지, 센서를 동시에 보는 AI

이 기대를 전부 만족하려면, 적어도 일부 추론은 기기나 현장에 가까운 곳에서 돌아야 한다.

### 1-1. 가장 큰 경제적 이유: inference 비용 폭증

대규모 생성형 AI는 학습비도 비싸지만, 실제 사업을 무너뜨리는 건 종종 `추론비`다.

특히 다음이 비용을 폭증시킨다.

- 긴 컨텍스트
- 멀티모달 입력
- 스트리밍 오디오/비디오
- 고빈도, 저지연 사용자 인터랙션
- background agent loops

온디바이스/엣지는 이 비용 구조를 바꾼다.

- 일부 inference를 기기로 내려서 서버 비용 절감
- 개인화된 빈번한 요청을 현장에서 처리
- 서버는 어려운 요청만 맡는 하이브리드 구조

Qualcomm은 2025년 white paper 성격 자료에서, AI의 중심축이 cloud-only에서 on-device inference까지 확장되며 inference가 시장 가치의 핵심 축이 된다고 설명했다. [1]

### 1-2. UX 이유: latency가 지능의 일부가 됨

생성형 AI 시대에는 응답 품질뿐 아니라 `반응 속도`가 곧 지능처럼 느껴진다.

- 카메라 뷰파인더에서 실시간 효과
- 실시간 자막/통역
- 화면 위 즉시 요약/재작성
- 실시간 agent interaction

이런 기능은 왕복 네트워크 지연이 조금만 커도 UX가 급격히 무너진다.

Google은 YouTube Shorts 실시간 생성형 효과 사례에서, 대형 teacher 모델의 기능을 경량 student로 distill하여 폰에서 프레임 단위로 실행하는 구조를 상세히 설명했다. 이 프로젝트의 핵심 문제는 품질보다도 `실시간성`이었다. [2]

### 1-3. 프라이버시 이유: 데이터가 더 이상 쉽게 못 나간다

개인 장치에서 다루는 데이터는 점점 더 민감해지고 있다.

- 개인 메모
- 이메일 초안
- 사진
- 화면 내용
- 마이크 입력
- 생체/건강 데이터
- 웨어러블 시계열

온디바이스 추론은 이 문제를 구조적으로 줄인다.

Apple은 Apple Intelligence를 설명하며, 가능한 작업은 기기에서 먼저 처리하고 더 큰 계산이 필요할 때만 `Private Cloud Compute(PCC)`로 보내는 구조를 공식화했다. 그리고 PCC에서도 데이터는 저장되지 않고 요청 처리에만 사용된다고 설명한다. [2][3][4]

Chrome의 Built-in AI 문서도 client-side AI의 장점으로 민감 데이터의 로컬 처리와 향상된 latency를 전면에 둔다. [5]

### 1-4. 가용성 이유: 네트워크가 없어도 돌아가야 한다

실전에서는 항상 네트워크가 완벽하지 않다.

- 지하철
- 비행기
- 공장
- 병원
- 차량 이동 환경
- 저전력 웨어러블

이 경우 온디바이스 AI는 단순한 최적화가 아니라 `기능 가용성 확보`다.

Apple은 Foundation Models framework를 통해 앱이 인터넷 연결 없이도 on-device foundation model을 직접 사용할 수 있다고 명시한다. [6]

Google AI Edge도 edge deployment의 장점으로 `Reduce latency. Work offline. Keep your data local & private.`를 명시한다. [7]

---

## 2. 가장 중요한 개념 변화: "클라우드 vs 온디바이스"가 아니라 "하이브리드 AI"

초기 논의는 종종 이분법이었다.

- 클라우드 AI가 더 강하다
- 온디바이스 AI가 더 빠르고 프라이빗하다

하지만 지금 산업은 거의 확실히 `하이브리드`로 수렴하고 있다.

### 2-1. 하이브리드 AI의 기본 구조

보통 다음처럼 나뉜다.

기기/엣지에서 처리
- 고빈도 저지연 추론
- 개인화 추론
- 프라이버시 민감 입력
- 전처리 / 후처리
- 간단한 또는 특화된 모델

클라우드에서 처리
- 대형 범용 reasoning
- 긴 컨텍스트 통합
- 고난도 생성
- 다중 도구 orchestration
- 대규모 검색 / 지식 접근

### 2-2. Apple의 방식: On-device first + Private Cloud Compute

Apple은 이 구조를 가장 분명하게 제도화한 기업 중 하나다.

- 많은 요청은 온디바이스 처리
- 더 복잡한 요청은 Apple silicon 기반 서버로 확장
- 단, 서버도 일반 클라우드처럼 다루지 않고 PCC라는 보안 경계를 둠

Apple Newsroom은 Apple Intelligence가 온디바이스 처리를 기본으로 하고, 더 복잡한 요청은 Private Cloud Compute로 확장한다고 설명한다. 또한 independent experts가 서버 코드 검증이 가능하고, 기기는 공개적으로 기록된 소프트웨어를 실행하는 서버와만 통신한다고 밝힌다. [3]

이건 단순한 개인정보 홍보가 아니라, 향후 하이브리드 AI 설계의 대표 패턴이다.

### 2-3. Chrome / Web의 방식: Browser-managed on-device AI + graceful fallback

Chrome Built-in AI 쪽 문서도 완전한 클라이언트 단독 만능론을 주장하지 않는다.

- on-device는 privacy와 latency에 강함
- 하지만 모든 작업을 대체하지는 못함
- 따라서 graceful fallback과 use-case specific design이 중요

라고 명확히 적고 있다. [5]

### 2-4. Qualcomm의 방식: Hybrid AI를 산업 전략으로 명시

Qualcomm은 아예 `"The future of AI is hybrid"`를 반복적으로 강조해 왔고, on-device AI, edge gateway, connectivity, enterprise edge를 하나의 축으로 묶는다. [8]

### 핵심 메시지

앞으로 중요한 질문은:

- `클라우드가 낫냐 온디바이스가 낫냐`

가 아니라

- `무슨 작업을 어디서 언제 처리하게 나눌 것인가`

다.

---

## 3. 기술 스택 관점에서 보면, 온디바이스/엣지 AI는 5층 구조로 이해하는 게 좋다

온디바이스/엣지 AI는 단순히 "모델을 압축해서 폰에 넣는 것"이 아니다.  
실제로는 최소 5개 층이 있다.

1. 하드웨어
2. 모델 구조
3. 컴파일/런타임
4. 배포/업데이트
5. 개인정보/운영/관측

이 중 하나만 약해도 제품화가 어렵다.

---

## 4. 하드웨어 트렌드: GPU보다 NPU/전용 가속기 중심으로

### 4-1. 왜 NPU가 중요해졌나

온디바이스 AI에서 가장 큰 제약은 대개 세 가지다.

- 메모리
- 전력
- 지속 가능 latency

GPU는 강력하지만 모바일/PC/웨어러블에서 항상 최적은 아니다.  
그래서 최근 흐름은 `NPU`, `TPU`, `DLA`, `전용 matrix engine` 같은 AI 전용 가속기로 이동 중이다.

### 4-2. Windows Copilot+ PC와 NPU 기준선

Microsoft는 Copilot+ PC를 소개하면서 `40+ TOPS NPU`를 로컬 AI UX의 기준선처럼 제시했다.  
Phi Silica 소개 글은 Copilot+ PC의 NPU가 sustained AI workloads를 장시간 처리하고, emergent behavior를 보이는 3~7B급 SLM을 semi-continuous loop로 돌릴 수 있다고 설명한다. [9]

이건 중요하다.  
온디바이스 AI가 `간단한 classifier`에서 `background OS agent` 수준으로 넘어갔다는 뜻이기 때문이다.

### 4-3. Google Tensor / TPU류: 하드웨어-모델 공동 설계

Google은 Pixel 6 시절부터 이미 "모델을 하드웨어에 맞춰 설계"하는 흐름을 강조했다.  
`Improved On-Device ML on Pixel 6` 글은 Google Tensor의 TPU에 맞도록 NAS로 모델을 자동 탐색해 latency와 power 조건을 만족시키면서 품질을 높였고, MobileBERT-EdgeTPU가 온디바이스에서 데이터센터급 BERT 품질에 근접하도록 만들었다고 설명한다. [10]

즉, 앞으로 온디바이스 성능은 모델 자체보다:

- target hardware에 맞춘 공동 최적화
- compiler와 graph transformation
- memory traffic까지 고려한 architecture search

가 좌우한다.

### 4-4. Coral NPU: ultra-low-power always-on edge

Google이 2025년 발표한 `Coral NPU`는 엣지 AI의 또 다른 방향을 보여준다.

- ultra-low-power
- always-on
- AI-first architecture
- few mW 수준 소비 전력
- hearables, AR glasses, smartwatches 같은 ambient devices 목표

이 NPU는 base design 기준 512 GOPS급 성능을 몇 mW로 제공한다고 설명된다. [11]

이건 "스마트폰급 on-device GenAI"와는 다른 축이다.

- 폰/노트북: interactive on-device AI
- 웨어러블/센서: ambient, always-on edge AI

### 4-5. NVIDIA Jetson: 산업/로보틱스 엣지의 사실상 표준 축

NVIDIA Jetson 쪽 흐름은 모바일과는 성격이 다르다.

- 더 큰 전력 예산
- 로봇/카메라/산업 장비
- 멀티센서
- 실시간 영상 파이프라인
- VLM/LLM + vision analytics 결합

JetPack 7.1 관련 글은 TensorRT Edge-LLM을 Jetson Thor에서 지원한다고 설명하며, LLM/VLM을 로보틱스 같은 real-time edge systems에서 효율적으로 돌리는 open-source C++ SDK라고 소개한다. [12]

JetPack 6/Jetson Platform Services는 또 다른 방향을 보여준다.

- 단순 모델 실행이 아니라
- modular microservices
- reusable AI services
- secure edge-to-cloud connectivity

를 포함한 edge application platform으로 가고 있다. [13]

### 4-6. Qualcomm / Snapdragon / Dragonwing: 모바일과 산업 엣지의 연결

Qualcomm은 스마트폰, PC, XR, 산업 게이트웨이까지 하나의 edge AI 전략으로 묶는다.

- Snapdragon: 모바일/PC/XR
- Dragonwing: 산업/엔터프라이즈 edge

Qualcomm AI Research는 2025년 자료에서 on-device image-to-video, fast-switching LoRA on mobile, real-time video stylization, text-to-3D, agentic AI on mobile 등을 "proof-of-concepts"로 전면에 내세웠다. [14]

이건 단순 demo 자랑이 아니라, edge AI의 방향이 다음으로 옮겨갔다는 뜻이다.

- 1세대 엣지 AI: task-specific compressed models
- 2세대 엣지 AI: foundation VLM / LLM / multimodal generative AI

NVIDIA도 거의 같은 표현으로 이를 `Edge AI 2.0`이라 부른다. [15]

---

## 5. 모델 구조 트렌드: "작게 만드는 것"보다 "효율적으로 활성화하는 것"

온디바이스 AI에서 가장 단순한 접근은 모델을 작게 만드는 것이다.  
하지만 최근에는 더 정교한 접근이 중요해지고 있다.

### 5-1. parameter-efficient execution

대표 사례가 `Gemma 3n`이다.

Google는 Gemma 3n을 everyday devices용 모델로 설명하며, 다음 기술을 전면에 둔다.

- `PLE (Per-Layer Embedding) parameter caching`
- `MatFormer architecture`
- `Conditional parameter loading`
- audio / visual / text conditional activation

문서에 따르면 E2B 모델도 표준 실행 시 50억+ 파라미터가 로드되지만, PLE caching과 parameter skipping으로 effective memory load를 약 19.1억 수준까지 줄일 수 있다. [14]

이건 매우 중요한 전환이다.

과거:
- 모델 크기 = 고정

현재:
- 요청 종류에 따라 활성 파라미터, 메모리 사용량, 기능을 유동적으로 조정

즉, future on-device model은 `one model, many operating points` 형태로 가고 있다.

### 5-2. task-specific micro model

Microsoft의 `Mu`는 반대 방향의 사례다.

- 330M encoder-decoder
- settings agent라는 매우 구체적 목적
- NPU 완전 오프로딩
- 100 tok/s 이상

Windows 블로그는 Mu가 Phi Silica 운용 경험을 바탕으로, edge/NPU 환경에 맞게 처음부터 설계된 micro-sized, task-specific model이라고 설명한다. [16]

이건 중요한 시사점을 준다.

- 모든 걸 3B/7B 범용 모델로 하려 하지 않는다
- 특정 업무는 encoder-decoder micro-model이 더 맞을 수 있다
- on-device AI는 범용성보다 UX 임계치를 넘는 특화 모델이 더 실용적인 경우가 많다

### 5-3. projector/adapter 재사용

Microsoft의 multimodal Phi Silica 글은 vision capability를 넣을 때 별도 vision SLM을 새로 깔지 않고, 기존 텍스트 모델을 최대한 재사용하며 `80M projector` 정도만 추가했다고 설명한다. [17]

이건 edge AI 제품화에서 매우 현실적인 패턴이다.

- base language model 재사용
- 작은 multimodal projector 추가
- 디스크/메모리/compute budget 통제

### 5-4. teacher-student distillation

Google의 YouTube Shorts 글은 teacher model을 Imagen 같은 대형 모델로 두고, student를 MobileNet encoder가 포함된 경량 UNet으로 만들어 폰에서 실시간으로 돌린다. [2]

이 패턴은 거의 표준이 되고 있다.

- teacher: 큰 모델, 품질 중심
- student: 작은 모델, latency 중심
- deployment: 특정 task에 특화된 distillate

### 5-5. vision encoder 최적화

Apple의 `FastVLM`은 VLM에서 time-to-first-token의 병목이 종종 LLM이 아니라 `vision encoder latency`라고 지적한다.  
특히 고해상도 문서/GUI 같은 입력에서 이 문제가 커진다고 설명하고, hybrid vision encoder 구조로 accuracy-latency tradeoff를 개선했다. [18]

즉, 멀티모달 on-device AI의 병목은 단순 LLM 크기가 아니다.

- image resolution
- visual token count
- encoder latency
- prefill cost

까지 함께 봐야 한다.

---

## 6. 추론 최적화 트렌드: quantization, offloading, selective activation, hardware-aware design

온디바이스/엣지 AI를 이해하려면 모델 자체보다도 `어떻게 돌리느냐`가 중요하다.

### 6-1. quantization은 이제 옵션이 아니라 기본값

Apple은 2025 Tech Report에서 on-device Apple Intelligence 모델에 대해:

- 약 3B parameter
- KV-cache sharing
- `2-bit quantization-aware training`

을 명시한다. [17]

NVIDIA는 Cosmos Nemotron VLM family가 AWQ 4-bit quantization으로 edge 배포에 적합하다고 설명한다. [15]

즉, 8-bit조차 당연한 전제가 아니고, 4-bit, 2-bit까지 적극적으로 들어간다.

### 6-2. KV-cache sharing과 cache engineering

모바일/엣지 추론에서 cache는 엄청 중요하다.

- prompt 길이 증가
- streaming generation
- memory bandwidth 제약

이 세 가지가 겹치기 때문이다.

Apple이 on-device foundation model에서 KV-cache sharing을 전면에 둔 건, 이제 inference optimization이 단순 matmul 최적화만이 아니라 memory residency design으로 갔다는 뜻이다. [17]

### 6-3. parameter caching / storage offloading

Gemma 3n의 PLE caching은 파라미터 일부를 fast local storage에 캐시해 runtime memory load를 줄이는 방식이다. [14]

이건 앞으로 더 커질 트렌드다.

- DRAM 안에 다 못 넣으니
- SSD/NVMe/flash와 메모리 사이를 정책적으로 활용
- 일부 모달리티 파라미터는 필요할 때만 로드

즉, 모델 추론이 점점 `OS-level memory orchestration` 문제와 가까워진다.

### 6-4. hardware-aware neural architecture search

Google Pixel 6 사례는 모델 구조를 하드웨어에 맞춰 NAS로 찾는 방향을 보여준다. [10]

이제 좋은 모델은:

- FLOPs가 적은 모델

이 아니라

- 특정 NPU/TPU에서 실제 latency와 power가 좋은 모델

이다.

### 6-5. one-step / low-step generation

온디바이스 diffusion에서는 sampling step 수가 치명적이다.  
`MobileDiffusion`은 one-step sampling 계열 접근으로 512x512 이미지를 약 0.5초에 생성할 수 있다고 보고한다. [19]

즉, 생성 모델의 edge deployment는:

- fewer steps
- smaller UNet
- operator fusion
- mobile-friendly backbone

조합으로 간다.

---

## 7. 소프트웨어 스택 트렌드: 런타임과 배포가 승부를 가른다

온디바이스/엣지 AI의 핵심은 모델이 아니라 `스택`이다.

### 7-1. Google: AI Edge / LiteRT / MediaPipe / AICore / Play 배포

Google의 edge 스택은 꽤 명확하다.

- `LiteRT`: 모델 실행 런타임
- `MediaPipe`: low-code tasks + custom pipelines
- `Model Explorer`: 변환/양자화/최적화 디버깅
- `AICore / Gemini Nano`: Android on-device GenAI
- `Play for On-device AI`: 모델 배포/업데이트 채널

Google AI Edge는 LLM과 diffusion 모델까지 edge에서 실행할 수 있는 stack을 제공한다고 명시한다. [7]

`Play for On-device AI`는 특히 중요하다.  
이제 문제는 "모델을 어떻게 앱에 넣나"가 아니라:

- install-time
- fast-follow
- on-demand
- device-targeted variants
- staged rollout

같이 `앱스토어 수준의 모델 공급망`이다. 문서에 따르면 개별 AI pack은 최대 1.5GB, 앱 전체는 4GB까지 가능하며 device targeting과 patching도 지원한다. [20]

이건 온디바이스 AI가 진짜 플랫폼 레벨로 올라왔다는 신호다.

### 7-2. Apple: Foundation Models framework + App Intents + PCC

Apple은 개발자에게:

- Foundation Models framework
- Swift native integration
- App Intents
- Shortcuts에서 Use Model action

같은 상위 레벨 인터페이스를 준다. [6]

중요한 점은 Apple이 개발자에게 "모델 세부 최적화"를 직접 하게 하기보다,  
운영체제와 silicon에 최적화된 모델 접근 레이어를 제공한다는 것이다.

즉:

- 더 폐쇄적이지만
- UX 일관성과 privacy semantics가 강함

### 7-3. Windows: inbox model + Windows AI APIs

Microsoft는 `Phi Silica`를 inbox pre-tuned model로 제공하고, Windows AI APIs를 통해 summarize/rewrite/format 같은 skill과 text generation을 제공한다. [9][21]

이 접근의 장점은:

- 모델 최적화와 배포를 OS가 책임짐
- 개발자는 상위 API만 쓰면 됨
- hardware class(Copilot+ PC)와 tightly coupled

라는 점이다.

### 7-4. Qualcomm: AI Hub + Genie + optimized model catalog

Qualcomm AI Hub는:

- ready-made Qualcomm-compatible models
- Workbench for optimization
- sample apps
- ONNX Runtime / LiteRT submission

을 제공한다. [22]

이건 칩 회사가 단순 가속기만 파는 게 아니라, `model bring-up friction` 자체를 줄이는 방향으로 간다는 뜻이다.

### 7-5. NVIDIA: JetPack + TensorRT Edge-LLM + Jetson Platform Services

NVIDIA는 edge를 단순 임베디드 기기보다, 거의 작은 cloud node처럼 다룬다.

- JetPack
- TensorRT Edge-LLM
- Jetson Platform Services
- edge-to-cloud microservice architecture

이 조합은 로봇/비전/산업 엣지에서 특히 강하다. [12][13]

---

## 8. 플랫폼별 전략 비교

### 8-1. Apple

강점
- on-device first 철학이 명확
- silicon, OS, runtime, product UX 수직통합
- privacy narrative가 매우 강함
- PCC로 hybrid AI를 깔끔하게 제도화

약점/제약
- 개발자 제어 범위가 비교적 제한적
- 모델 선택의 자유도가 낮음
- Apple 플랫폼 안에서만 가장 강함

대표 신호
- Foundation Models framework [6]
- Apple Intelligence on-device model + PCC [3][4][17]

### 8-2. Google

강점
- Android, Web, Embedded까지 stack이 넓음
- AI Edge, LiteRT, MediaPipe, Chrome Built-in AI, Android AICore 등 계층이 촘촘
- Gemma/Gemma 3n 같은 open-ish 모델 생태계
- Play distribution까지 연결

약점/제약
- 생태계가 넓은 만큼 device fragmentation 대응이 어려움
- 플랫폼/제품 경로가 많아 개발자 입장에서 선택지가 복잡

대표 신호
- Google AI Edge [7]
- AI on Android [23]
- Play for On-device AI [20]
- Gemma 3n [14]
- Coral NPU [11]

### 8-3. Microsoft

강점
- Copilot+ PC로 NPU 기준선을 만듦
- inbox model + OS API 접근이 쉬움
- productivity / accessibility / OS agent use case에 강함

약점/제약
- 주로 Windows PC 축에 집중
- 모바일/embedded 범용성은 상대적으로 덜함

대표 신호
- Phi Silica [9][21]
- multimodal Phi Silica [17]
- Mu [16]

### 8-4. Qualcomm

강점
- 스마트폰, PC, XR, 산업 엣지까지 칩 포트폴리오 연결
- 온디바이스 generative AI proof-of-concept가 빠름
- AI Hub로 개발자 진입 장벽 완화

약점/제약
- 최종 UX는 OEM/파트너에 크게 의존
- 플랫폼 일관성은 Apple/Google보다 약할 수 있음

대표 신호
- AI Hub [22]
- on-device GenAI firsts [14]
- hybrid AI positioning [8]

### 8-5. NVIDIA

강점
- 로보틱스, vision AI, 산업 엣지의 표준적 존재감
- software stack completeness
- microservices + edge inference + VLM/LLM까지 폭넓음

약점/제약
- 소비자 모바일보다는 산업/로봇 중심
- 전력/비용 예산이 더 큰 영역에 최적

대표 신호
- JetPack 7.1 / TensorRT Edge-LLM [12]
- Jetson Platform Services [13]
- Edge AI 2.0 / Cosmos Nemotron [15]

---

## 9. 개인정보·보안·규제는 기능이 아니라 아키텍처 제약이다

온디바이스/엣지 AI가 강해지는 가장 큰 이유 중 하나는 privacy다.  
하지만 실무에서는 더 복잡하다.

### 9-1. 단순 로컬 추론만으로 끝나지 않음

현실 문제
- personalization은 필요함
- private data로 model 개선도 하고 싶음
- raw data는 수집하기 어려움

그래서 등장하는 것이:

- federated learning
- federated analytics
- differential privacy
- synthetic data for private distribution matching

### 9-2. Google의 private training 흐름

Google은 Gboard 등 production on-device language models에서 private training을 오랫동안 밀어왔다.  
`Advances in private training for production on-device language models`와 `Parfait` 관련 글은 federated compute, privacy-preserving infrastructure, on-device personalization tooling을 함께 묶는다. [24][25]

또 `Prompt Public LLMs to Synthesize Data for Private On-device Applications`는 public LLM이 private 분포에 가까운 synthetic pretraining data를 만들어, private on-device model의 사전학습 품질을 높이는 방향을 제시한다. [26]

### 9-3. Apple의 모델

Apple은 기기 내 추론을 우선하고, 복잡한 요청은 PCC로 보내되:

- 관련 데이터만 전송
- 저장 안 함
- Apple도 접근 불가
- independent verification 가능

를 강조한다. [3][4]

### 9-4. 웹 브라우저의 client-side AI

Chrome 문서는 client-side AI의 장점으로 privacy를 첫 번째 축 중 하나로 적는다. [5]

### 핵심 메시지

온디바이스/엣지 AI의 privacy는 단순 마케팅 문구가 아니라:

- 데이터 이동 최소화
- 공격면 축소
- 법규 대응 단순화
- 사용자 신뢰 확보

를 위해 구조적으로 중요하다.

---

## 10. 온디바이스 AI의 대표 use case는 어떻게 바뀌고 있나

### 10-1. 모바일 생산성

대표 기능
- 요약
- 재작성
- 표 변환
- 문서 이해
- 메모 정리

Phi Silica는 Word/Outlook 재작성/요약, Click to Do 같은 productivity 경험에 연결된다. [9]

### 10-2. 멀티모달 접근성

대표 기능
- 화면 내용 설명
- 시각 자료 요약
- 이미지+텍스트 이해
- assistive interaction

Microsoft는 multimodal Phi Silica가 accessibility와 productivity 경험을 연다고 설명한다. [17]

### 10-3. 실시간 카메라 / 미디어

대표 기능
- AR effect
- style transfer
- image generation
- image-to-video
- live VLM assistance

Google의 YouTube Shorts 사례, MobileDiffusion, Qualcomm의 real-time on-device image-to-video demo들이 여기에 해당한다. [2][14][19]

### 10-4. 브라우저와 웹

대표 기능
- 로컬 번역
- 로컬 문장 교정
- 브라우저 내 writing assistant
- client-side summarization

Chrome Built-in AI는 browser-managed Gemini Nano 기반 API를 제공한다. [5]

### 10-5. 웨어러블 / ambient AI

대표 기능
- always-on sensing
- 주변 맥락 이해
- health/wellness inference
- 초저전력 personal assistant

Coral NPU는 이 축을 겨냥한다. [11]

### 10-6. 산업 엣지 / 비전 AI

대표 기능
- 영상 분석
- 이벤트 감지
- 다중 카메라 요약
- 로봇 제어
- 공장/물류 현장 시각 에이전트

Jetson Platform Services, TensorRT Edge-LLM, Qualcomm Dragonwing는 이 방향을 강하게 민다. [12][13][27]

---

## 11. 배포(deployment)가 이제 연구보다 더 어려운 경우가 많다

과거에는 모델을 만드는 것이 가장 어려웠다.  
지금은 `배포`가 더 어렵다.

### 왜 어려운가

- 디바이스별 메모리 다름
- NPU/GPU/CPU capability 다름
- 운영체제 버전 다름
- 모델 다운로드 크기 민감
- 업데이트 전략 필요
- fallback 필요
- quantized variant가 여러 개 필요

### 11-1. distribution as infrastructure

Android의 `Play for On-device AI`는 이 문제를 정확히 겨냥한다.

- install-time / fast-follow / on-demand
- model variants targeting
- staged rollout
- patching

즉, 앱 배포 파이프라인이 아니라 `모델 공급망`이 된다. [20]

### 11-2. browser-managed models

Chrome Built-in AI는 브라우저가 모델 다운로드, 캐싱, 관리까지 맡는 구조를 지향한다. [5]

### 11-3. OS-managed inbox models

Windows의 Phi Silica, Apple의 Foundation Models도 비슷하다.

- 모델을 앱이 직접 번들링하지 않음
- OS가 model lifecycle을 관리
- 개발자는 stable API를 사용

### 의미

장기적으로는:

- 앱 개발자
- 모델 개발자
- 플랫폼 공급자

역할이 더 분리될 가능성이 크다.

---

## 12. 온디바이스 / 엣지 AI가 마주치는 진짜 병목

겉보기엔 모델만 작게 만들면 다 해결될 것 같지만, 실제 병목은 훨씬 복합적이다.

### 12-1. 메모리 대역폭

연산보다 메모리 이동이 더 큰 병목인 경우가 많다.

- KV cache
- activation movement
- high-resolution visual tokens
- multimodal projector overhead

### 12-2. thermal throttling

폰, 노트북, XR, 자동차 내부는 sustained performance가 중요하다.  
짧은 벤치마크보다 `5분~30분 지속 부하`에서의 성능 유지가 중요하다.

### 12-3. model management complexity

여러 모델이 함께 올라가는 경우:

- base SLM
- vision projector
- ASR
- TTS
- safety filter
- embedding model

메모리와 디스크가 빨리 부족해진다.

Microsoft가 multimodal Phi Silica를 설계할 때 separate vision SLM 대신 80M projector만 추가한 이유가 바로 이 점이다. [17]

### 12-4. hardware fragmentation

Android, Windows PC, XR, embedded는 가속기 구조가 전부 다르다.

그래서 model portability와 operator compatibility가 큰 문제다.

Google AI Edge가 multi-framework, cross-platform, hardware accelerators를 전면에 두는 이유도 이것이다. [7]

### 12-5. quality cliffs

모델을 줄이면 어느 순간 갑자기 품질이 무너진다.

- OCR
- long reasoning
- document parsing
- multilinguality
- tool use

특히 작은 온디바이스 모델은 "대충 되는 것처럼 보이다가 특정 task에서 급격히 깨지는" 현상이 흔하다.

### 12-6. observability 부족

클라우드와 달리 로컬 추론은:

- 로깅이 제한적
- 디버깅이 어려움
- 성능 분산이 심함
- 개인정보 때문에 telemetry도 제한됨

그래서 edge AI 운영은 model serving보다 field engineering에 가깝다.

---

## 13. 가장 중요한 기술 트렌드 10개만 압축하면

### 1. 하이브리드 AI가 표준이 된다

완전 온디바이스도, 완전 클라우드도 아니다.  
요청 난이도와 데이터 민감도에 따라 분산 처리하는 구조가 표준이 된다.

### 2. NPU가 제품 정의를 바꾼다

단순 가속기가 아니라, 어떤 UX를 가능하게 하느냐의 기준이 된다.

### 3. 3B~7B급 범용 SLM만이 답이 아니다

330M급 특화 모델(Mu)처럼 작은 encoder-decoder가 특정 UX에 더 적합할 수 있다. [16]

### 4. effective parameter 개념이 중요해진다

총 파라미터보다 실제로 메모리에 올려서 활성화하는 파라미터가 더 중요하다.  
Gemma 3n이 이 흐름의 대표 사례다. [14]

### 5. projector/adapter 재사용이 늘어난다

기존 모델을 유지한 채 작은 모듈을 덧붙여 multimodal/개인화 기능을 확장한다. [17]

### 6. distillation이 실전형 아키텍처가 된다

teacher는 클라우드, student는 기기.  
이 패턴은 거의 모든 real-time mobile generative AI에서 반복된다. [2]

### 7. deployment pipeline이 플랫폼화된다

Play for On-device AI, browser-managed models, OS inbox models가 대표적이다. [20][5]

### 8. privacy-preserving learning이 다시 중요해진다

federated learning, DP, synthetic-private data adaptation이 production issue로 재등장했다. [24][25][26]

### 9. edge AI가 generative / multimodal / agentic 방향으로 간다

NVIDIA가 부르는 Edge AI 2.0, Qualcomm의 on-device GenAI firsts, Windows의 multimodal SLM이 같은 방향이다. [14][15][17]

### 10. ultra-low-power ambient AI가 새로운 시장을 연다

Coral NPU 같은 구조는 "폰에서 큰 모델 돌리기"와는 다른, 상시 센싱형 엣지 AI 축을 연다. [11]

---

## 14. 향후 1~3년 전망

### 14-1. 스마트폰/PC는 "로컬 AI OS" 경쟁으로 간다

플랫폼 경쟁 포인트가 바뀐다.

- 더 큰 앱 생태계
- 더 좋은 로컬 모델 접근 API
- 더 강한 NPU
- 더 나은 hybrid routing

### 14-2. 모델보다 스택이 더 차별화된다

앞으로 차이는 base model보다:

- compiler
- quantization toolchain
- runtime
- package delivery
- personalization framework

에서 더 크게 날 가능성이 높다.

### 14-3. 멀티모달 온디바이스가 본격화된다

지금은 텍스트 중심 기능이 많지만, 앞으로는:

- screen understanding
- camera understanding
- live voice + vision
- local VLM

이 훨씬 늘어날 가능성이 높다.

### 14-4. edge AI는 산업 자동화의 중심이 된다

공장, 물류, 리테일, 보안, 로봇에서는:

- edge VLM
- edge agent
- microservice inference graph

가 기본 구조가 될 수 있다.

### 14-5. 항상 켜져 있는 ambient AI가 늘어난다

hearables, glasses, smartwatches, sensor hubs에서는:

- few mW
- always-on
- private
- context-aware

가 중요해진다.

### 14-6. 평가 기준이 바뀐다

앞으로 온디바이스/엣지 AI의 좋은 모델은 아래로 평가될 것이다.

- 평균 latency
- sustained latency
- energy per token / per frame
- memory footprint
- cold start / TTFT
- privacy guarantee
- model update friction
- offline availability
- user-perceived responsiveness

---

## 15. 실무 관점에서 꼭 기억할 것

### 제품 기획자라면

먼저 정해야 할 것
- 이 기능이 정말 온디바이스여야 하는가
- 아니면 hybrid가 맞는가
- latency/프라이버시/오프라인 요구 중 무엇이 핵심인가

### ML 엔지니어라면

먼저 정해야 할 것
- target hardware
- memory budget
- TTFT budget
- quantization budget
- fallback strategy

### 플랫폼/인프라 팀이라면

먼저 정해야 할 것
- model delivery 방식
- observability
- variant management
- secure update chain
- privacy posture

---

## 16. 최종 결론

온디바이스 AI / 엣지 AI는 더 이상 "작은 모델을 디바이스에 넣어보는 서브트랙"이 아니다.

이제는:

- 생성형 AI의 비용 문제
- 개인정보 문제
- 저지연 UX 문제
- 멀티모달 실시간 인터페이스 문제
- 산업 자동화 문제

를 동시에 푸는 핵심 시스템 전략이다.

한 문장으로 정리하면:

`AI는 점점 더 클라우드에서 만들어지고, 현장에서 실행되며, 둘 사이를 하이브리드로 오간다.`

그리고 그 과정에서 승부를 가르는 것은 단순 모델 크기가 아니라:

- 하드웨어-모델 공동 설계
- selective activation
- quantization / caching / routing
- 배포 공급망
- privacy-preserving architecture

다.

---

## 17. 참고 자료 / 1차 소스

[1] Qualcomm, `AI disruption is driving innovation in on-device inference`  
https://www.qualcomm.com/news/onq/2025/02/ai-disruption-is-driving-innovation-in-on-device-inference

[2] Google Research, `From massive models to mobile magic: The tech behind YouTube real-time generative AI effects`  
https://research.google/blog/from-massive-models-to-mobile-magic-the-tech-behind-youtube-real-time-generative-ai-effects/

[3] Apple Newsroom, `Introducing Apple Intelligence for iPhone, iPad, and Mac`  
https://www.apple.com/newsroom/2024/06/introducing-apple-intelligence-for-iphone-ipad-and-mac/

[4] Apple Support / Privacy, `Apple Intelligence and privacy`  
https://support.apple.com/guide/iphone/apple-intelligence-and-privacy-iphe3f499e0e/26/ios/26  
https://www.apple.com/legal/privacy/data/en/intelligence-engine/

[5] Chrome for Developers, `Why use client-side AI` / `Built-in AI`  
https://developer.chrome.com/docs/ai/why-on-device  
https://developer.chrome.com/docs/ai/built-in

[6] Apple Developer, `What’s New - Apple Intelligence`  
https://developer.apple.com/apple-intelligence/whats-new/

[7] Google AI Edge  
https://ai.google.dev/edge

[8] Qualcomm, `The future of AI is hybrid`  
https://www.qualcomm.com/content/dam/qcomm-martech/dm-assets/documents/Whitepaper-The-future-of-AI-is-hybrid-Part-1-Unlocking-the-generative-AI-future-with-on-device-and-hybrid-AI.pdf

[9] Microsoft, `Phi Silica, small but mighty on-device SLM`  
https://blogs.windows.com/windowsexperience/2024/12/06/phi-silica-small-but-mighty-on-device-slm/

[10] Google Research, `Improved On-Device ML on Pixel 6, with Neural Architecture Search`  
https://research.google/blog/improved-on-device-ml-on-pixel-6-with-neural-architecture-search/

[11] Google Research, `Coral NPU: A full-stack platform for Edge AI`  
https://research.google/blog/coral-npu-a-full-stack-platform-for-edge-ai/

[12] NVIDIA, `Accelerate AI Inference for Edge and Robotics with NVIDIA Jetson T4000 and NVIDIA JetPack 7.1`  
https://developer.nvidia.com/blog/accelerate-ai-inference-for-edge-and-robotics-with-nvidia-jetson-t4000-and-nvidia-jetpack-7-1/

[13] NVIDIA, `Power Cloud-Native Microservices at the Edge with NVIDIA JetPack 6.0, Now GA`  
https://developer.nvidia.com/blog/power-cloud-native-microservices-at-the-edge-with-nvidia-jetpack-6-0-now-ga/

[14] Qualcomm, `GenAI firsts: On-device AI at the edge`  
https://www.qualcomm.com/news/onq/2025/08/genai-firsts-redefining-whats-possible-at-the-edge

[15] NVIDIA, `Visual Language Intelligence and Edge AI 2.0 with NVIDIA Cosmos Nemotron`  
https://developer.nvidia.com/blog/visual-language-intelligence-and-edge-ai-2-0/

[16] Microsoft, `Introducing Mu language model and how it enabled the agent in Windows Settings`  
https://blogs.windows.com/windowsexperience/2025/06/23/introducing-mu-language-model-and-how-it-enabled-the-agent-in-windows-settings/

[17] Microsoft, `Enabling multimodal functionality for Phi Silica`  
https://blogs.windows.com/windowsexperience/2025/04/25/enabling-multimodal-functionality-for-phi-silica/

[18] Apple ML Research, `FastVLM: Efficient Vision Encoding for Vision Language Models`  
https://machinelearning.apple.com/research/fast-vision-language-models

[19] Google Research, `MobileDiffusion: Rapid text-to-image generation on-device`  
https://research.google/blog/mobilediffusion-rapid-text-to-image-generation-on-device/

[20] Android Developers, `Play for On-device AI (beta)`  
https://developer.android.com/google/play/on-device-ai

[21] Microsoft Learn, `Platform card - Phi Silica`  
https://learn.microsoft.com/en-us/windows/ai/cards/phi-silica-platform-card

[22] Qualcomm AI Hub  
https://aihub.qualcomm.com/

[23] Android Developers, `AI on Android`  
https://developer.android.com/ai

[24] Google Research, `Advances in private training for production on-device language models`  
https://research.google/blog/advances-in-private-training-for-production-on-device-language-models/

[25] Google Research, `Parfait: Enabling private AI with research tools`  
https://research.google/blog/parfait-enabling-private-ai-with-research-tools/

[26] Google Research Publications, `Prompt Public Large Language Models to Synthesize Data for Private On-device Applications`  
https://research.google/pubs/prompt-public-large-language-models-to-synthesize-data-for-private-on-device-applications/

[27] Qualcomm, `How Qualcomm Dragonwing powers industrial edge AI transformation across industries`  
https://www.qualcomm.com/news/onq/2026/03/how-qualcomm-dragonwing-powers-industrial-edge-ai

[28] Apple ML Research, `Apple Intelligence Foundation Language Models Tech Report 2025`  
https://machinelearning.apple.com/research/apple-foundation-models-tech-report-2025

[29] Google AI for Developers, `Gemma 3n model overview`  
https://ai.google.dev/gemma/docs/gemma-3n

[30] arXiv, `A Survey of Resource-efficient LLM and Multimodal Foundation Models`  
https://arxiv.org/abs/2401.08092
