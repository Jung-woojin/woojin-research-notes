# 2026 멀티모달 AI 트렌드 리포트

기준일: 2026-04-20

이 문서는 2024~2026 흐름을 중심으로, 현재 멀티모달 AI 분야에서 실제로 패러다임을 바꾸고 있는 기술 축들을 정리한 상세 리포트다.

단순히 "이미지 넣고 답하는 모델" 수준이 아니라, 아래 전체를 멀티모달 AI의 범위로 본다.

- 텍스트 + 이미지
- 텍스트 + 이미지 + 오디오
- 텍스트 + 이미지 + 비디오
- 이해(understanding) + 생성(generation) 통합
- 인식(perception) + 추론(reasoning) + 행동(action) 연결
- 범용 foundation model + 도메인 특화 멀티모달 모델

---

## 한 줄 요약

지금 멀티모달 AI는 `텍스트 기반 LLM에 이미지 인코더를 붙인 보조 기능` 단계를 지나,  
점점 `여러 감각을 공통 표현 공간에서 처리하고, 장면을 이해하고, 생성하고, 도구를 쓰고, 행동까지 연결하는 범용 인터페이스`로 바뀌고 있다.

핵심 변화는 여섯 가지다.

1. `late fusion`에서 `native / omni multimodality`로 이동
2. `정지 이미지 이해`에서 `비디오·오디오·실시간 상호작용`으로 이동
3. `이해 모델`과 `생성 모델`의 경계가 약해짐
4. `보이는 것 설명`에서 `공간 추론·시간 추론·행동 연결`로 이동
5. `범용 VLM` 위에 `도메인 특화 멀티모달 foundation model`이 쌓임
6. `대형 클라우드 모델`과 동시에 `엣지/오픈소스/소형화`가 빠르게 진행 중

---

## 1. 멀티모달 AI는 어떻게 여기까지 왔나

멀티모달 AI의 최근 흐름을 이해하려면 기술 계보를 먼저 짚는 게 좋다.

### 1-1. 1세대: 표현 정렬 중심

초기 멀티모달의 핵심은 "이미지와 텍스트를 같은 임베딩 공간에 올리는 것"이었다.

대표 특징
- 이미지-텍스트 검색
- zero-shot 분류
- contrastive learning
- dual encoder 구조

이 시기의 대표적인 사고방식은:

- 텍스트와 이미지를 함께 넣어 모델이 길게 추론하게 하는 것이 아니라
- 두 모달리티를 같은 의미 공간에 정렬해 재사용 가능한 표현을 만들자는 방향

이다.

이 계보는 여전히 중요하다. 지금도 강한 VLM/VLM encoder의 뿌리는 여기에 있다.

### 1-2. 2세대: vision-language model(VLM)

다음 단계는 이미지와 텍스트를 받아 텍스트를 생성하는 모델이다.

대표 특징
- captioning
- VQA
- OCR + reasoning
- grounding
- instruction following

이 단계에서 멀티모달은 retrieval에서 conversation으로 넘어갔다.

### 1-3. 3세대: multimodal large language model(MLLM)

이제는 단순 이미지 설명을 넘어:

- 차트/문서/표/GUI 이해
- 여러 이미지 비교
- 긴 비디오 분석
- 멀티턴 시각 대화
- 코드/도구/에이전트 연결

까지 요구된다.

즉, 모델은 더 이상 "이미지 QA 모델"이 아니라 `멀티모달 범용 추론기`를 지향한다.

### 1-4. 4세대: omni / unified / agentic multimodality

최신 흐름은 여기에 더 가깝다.

- 입력: 텍스트, 오디오, 이미지, 비디오
- 출력: 텍스트, 오디오, 이미지, 비디오, 행동
- 메모리: 긴 컨텍스트와 다중 턴 유지
- 실행: 도구 사용, UI 조작, 로봇 제어, 검색, 계획

즉, 멀티모달 AI는 "모달리티 몇 개 더 붙은 LLM"이 아니라  
`세계와 상호작용하는 일반 인터페이스 계층`으로 이동하고 있다.

---

## 2. 가장 중요한 트렌드: Late Fusion에서 Native / Omni Multimodality로

### 과거 방식

많은 초기 MLLM은 사실상 다음 구조였다.

- 이미지 인코더
- 텍스트 LLM
- 둘을 잇는 adapter / projector

이 구조는 강력했지만 한계도 분명했다.

- 모달리티 간 상호작용이 얕다
- 시공간적 정보 통합이 약하다
- 음성/비디오/생성 출력을 자연스럽게 통합하기 어렵다
- "이미지를 읽고 텍스트를 말하는 모델" 느낌이 강하다

### 현재 방식

지금은 점점 더 `native` 또는 `omni`라는 표현이 중요해지고 있다.

OpenAI는 GPT-4o를 발표하면서 이를 "omni model"로 설명했고, 텍스트·오디오·이미지·비디오를 어떤 조합으로든 입력받고 텍스트·오디오·이미지 출력을 생성할 수 있는 방향을 제시했다. [1]

Google도 Gemini를 "natively multimodal" 계열로 밀고 있다. Gemini 2.5 Flash 소개 페이지 역시 이 표현을 전면에 둔다. [2]

### 왜 이게 중요한가

멀티모달 AI의 품질은 단순히 이미지 인코더 성능만으로 결정되지 않는다.

중요한 것은:

- modality alignment가 얼마나 깊은가
- 중간 표현이 공유되는가
- 추론 중에 모달리티가 상호 보강되는가
- 출력도 멀티모달로 자연스럽게 이어지는가

이다.

### 이 흐름이 실제로 바꾸는 것

1. 모델 설계
- projector를 붙이는 수준에서 끝나지 않음
- shared token space / unified latent / common decoder 구조가 중요해짐

2. 제품 UX
- "사진 업로드하고 질문"에서 끝나지 않음
- 음성 대화, 실시간 스트리밍, 이미지 편집, 멀티턴 refinement가 자연스러운 기본 경험이 됨

3. 데이터 전략
- 단순 image-caption pair만으로는 부족
- text-image, text-audio, text-video, interleaved data, action trajectories가 필요

---

## 3. 멀티모달의 무게중심이 이미지에서 비디오와 시간으로 이동 중

### 왜 비디오가 중요한가

현실 세계는 정지 이미지가 아니라 시계열이다.

비디오가 제공하는 정보
- 시간적 일관성
- 움직임
- 원인과 결과의 단서
- 장면 전환
- 객체 지속성(object permanence)
- 행동 전개

즉, 비디오는 perception과 reasoning 사이를 메우는 핵심 데이터다.

### 최근 신호 1: long-video understanding

`Video-MME`는 멀티모달 LLM을 비디오 분석 기준으로 평가하는 대표 벤치마크다.  
이 벤치마크는 11초에서 1시간짜리 영상, 자막과 오디오까지 포함한 900개 영상과 2,700 QA를 통해 모델의 비디오 이해를 측정한다. 논문은 당시 상용 모델이 오픈소스보다 강하지만, 긴 시퀀스와 다중 모달 데이터 처리에 여전히 큰 개선 여지가 있다고 지적했다. [3]

### 최근 신호 2: self-supervised video world model

`V-JEPA 2`는 100만 시간 이상의 인터넷 비디오와 이미지로 사전학습된 action-free video model이다. 이 모델은 video understanding을 넘어 prediction과 planning까지 겨냥한다. [4]

이건 단순한 video captioning이 아니다.  
의미는 다음과 같다.

- 비디오를 "묘사 대상"이 아니라 "세계 동역학 학습 원천"으로 보기 시작했다
- 멀티모달 모델이 future-aware / planning-aware 방향으로 이동하고 있다

### 최근 신호 3: 오픈소스 long-video VLM 강화

`Qwen2.5-VL`은 long-video comprehension, second-level event localization, dynamic resolution processing을 전면에 내세웠다. 기술 보고서는 이미지 크기와 영상 길이가 크게 달라지는 실제 입력을 다루기 위해 native dynamic-resolution ViT와 absolute time encoding을 도입했다고 설명한다. [5]

### 의미

지금 멀티모달 AI의 중요한 경쟁 포인트는:

- 짧은 이미지 QA를 얼마나 잘하나

에서

- 긴 비디오를 얼마나 일관되게 이해하나
- 시점/시간/사건 전개를 추적하나
- 필요한 구간을 정확히 찾아내나

로 옮겨가고 있다.

---

## 4. 오디오는 보조 모달리티가 아니라 인터페이스의 중심이 되고 있다

### 과거 오디오

예전의 오디오는 다음처럼 분리되어 있었다.

- ASR
- TTS
- speaker model
- separate voice pipeline

즉, 멀티모달 시스템의 "앞단/뒷단" 정도였다.

### 현재 오디오

지금은 오디오가 대화 인터페이스의 중심으로 올라왔다.

OpenAI는 2025년에 `gpt-4o-transcribe`, `gpt-4o-mini-transcribe`, `gpt-4o-mini-tts`를 공개하면서, 음성 인식과 음성 합성이 더 이상 별도 파이프라인이 아니라 `voice agent`의 핵심 구성요소라고 설명했다. 특히 speech-to-text 정확도와 multilingual robustness, 그리고 TTS steerability를 강조했다. [6]

### 왜 중요한가

음성은 멀티모달 AI를 "사용하는 방식" 자체를 바꾼다.

- 텍스트 입력보다 마찰이 적다
- 시각 정보를 보면서 동시에 대화 가능하다
- hands-free 상황에서 결정적이다
- 실시간 인터랙션, 접근성, 고객지원, 교육, 모바일 UX에 직접 연결된다

### 결과적으로 바뀌는 것

멀티모달 AI 제품은 점점:

- vision + audio + reasoning + tools

조합으로 설계된다.

텍스트 챗은 여전히 중요하지만, `spoken multimodal interface`가 점점 기본 UX가 된다.

---

## 5. 이해와 생성의 경계가 약해지고 있다

### 예전

모델이 보통 둘 중 하나였다.

- 이해형 모델: 이미지 보고 설명/답변/분류
- 생성형 모델: 텍스트로 이미지/비디오 생성

### 지금

최근 가장 중요한 변화 중 하나는 `understanding`과 `generation`이 같은 시스템 안에서 통합되고 있다는 점이다.

OpenAI의 `4o image generation`은 이 흐름의 대표 사례다. OpenAI는 이를 "native image generation"으로 설명하며, 모델이 텍스트와 이미지를 분리된 체계가 아니라 하나의 지식 체계로 다루기 때문에 더 일관된 편집, 텍스트 렌더링, 멀티턴 refinement가 가능하다고 설명한다. [7]

Apple의 `MANZANO`도 이 흐름을 다른 방식으로 보여준다. Apple은 단일 shared vision encoder 위에 이해용 continuous embedding과 생성용 discrete token을 함께 두는 hybrid vision tokenizer 구조를 제안했다. [8]

Apple의 `AToken`은 더 나아가 이미지, 비디오, 3D를 공통 4D latent space로 encode하는 unified visual tokenizer를 제시했다. 이 모델은 reconstruction과 semantic understanding을 함께 겨냥한다. [9]

### 왜 중요한가

이 흐름은 장기적으로 아래를 의미한다.

- 생성형 모델도 강한 perception이 필요
- 이해형 모델도 internal generation / imagination 능력이 필요
- "분석 모델"과 "생성 모델"이 따로 있는 구조는 점점 비효율적

### 실제 응용에서 보이는 변화

- 이미지 편집: 이해 + 생성 + 일관성 유지
- 영상 생성: 시간 동역학 이해 + 생성
- UI 생성: 화면 구조 이해 + 코드/이미지 생성
- scientific reasoning: 도식 이해 + 시각적 intermediate 생성

즉, multimodal AI는 점점 `analyze`와 `create`를 동시에 하는 쪽으로 간다.

---

## 6. 멀티모달 AI는 더 강해졌지만, "깊은 추론"은 아직 병목이다

### 겉으로는 매우 강해 보이는 이유

최근 모델들은 잘한다.

- OCR
- 차트 해석
- 문서 질의응답
- 이미지 캡션
- GUI 설명
- 시각 상식 문제

그래서 쉽게 착각하게 된다.

"이미 보는 것도 하고, 말도 잘하니까 이제 진짜로 이해하는 거 아닌가?"

### 하지만 실제 병목

여전히 큰 문제가 남아 있다.

- 공간 추론
- 긴 비디오 인과 추론
- 관측-추론-검증 루프의 부족
- hallucination
- 시각 정보와 언어 추론의 얕은 결합

`Mind the Gap`은 VLM의 spatial reasoning 성능이 여전히 매우 낮다는 점을 지적했다. 13개 최신 모델을 평가한 결과 평균 정확도가 랜덤 추측에 가까운 수준이라는 결론은, 멀티모달 모델이 "보이는 걸 말하는 능력"과 "공간적으로 이해하는 능력"이 다르다는 걸 보여준다. [10]

또한 `MMMU`와 `MMMU-Pro` 계열 벤치마크는 전문가 수준의 멀티모달 지식 추론이 여전히 어렵다는 점을 보여준다.  
특히 MMMU는 30개 과목과 다양한 이미지 유형을 포함하며, 초기 결과에서 GPT-4V와 Gemini Ultra조차 충분히 높은 정확도에 도달하지 못했다고 보고했다. [11]  
MMMU-Pro는 텍스트-only shortcut을 제거하고 질문 자체를 이미지 안에 넣어 OCR+reasoning을 동시에 강제함으로써, 실제 성능이 더 낮아짐을 보여줬다. [12]

### 현재 연구 커뮤니티의 해석

`Mind with Eyes` 같은 2025년 survey들은, 최근 멀티모달 reasoning을:

- language-centric multimodal reasoning
- collaborative multimodal reasoning

으로 나누어 설명한다. 즉 많은 시스템이 아직은 "언어 추론이 메인이고, 시각은 근거 자료" 수준에 가깝다는 뜻이다. [13]

### 핵심 메시지

오늘날 멀티모달 AI의 핵심 병목은:

- pixel을 읽을 수 있는가

가 아니라

- 여러 모달리티를 사용해 일관된 internal world model을 만들 수 있는가

이다.

---

## 7. 시각 정보의 표현 자체가 바뀌고 있다: encoder, tokenizer, dense features의 재발명

### 7-1. contrastive encoder의 진화

`SigLIP 2`는 단순 image-text alignment에서 한 단계 더 나아간다.

핵심 포인트
- captioning-based pretraining
- self-distillation
- masked prediction
- online data curation
- multilingual
- localization과 dense prediction 개선

즉, retrieval encoder가 더 이상 retrieval만을 위한 것이 아니라, `dense visual understanding의 기반 표현`이 되고 있다. [14]

### 7-2. visual tokenizer의 통합

앞서 본 `AToken`은 이미지, 비디오, 3D를 하나의 시각 토큰 체계로 묶는다. [9]

이 방향은 왜 중요할까?

- 서로 다른 입력 형식을 하나의 모델로 다루기 쉬워짐
- 생성과 이해를 같은 토큰 공간에서 연결하기 쉬워짐
- multimodal model이 modality-specific front-end를 덜 필요로 하게 됨

### 7-3. hybrid tokenizer / unified model

`MANZANO`, `4M`, `4M-21` 같은 Apple 계열 연구는 "어떻게 vision 토크나이저를 설계해야 any-to-any multimodal model이 가능한가"를 밀고 있다. [8][15][16]

### 의미

앞으로 멀티모달 경쟁력은 단순히 LLM 본체만이 아니라:

- visual tokenizer
- vision encoder
- modality routing
- shared latent design

에서 크게 갈릴 가능성이 높다.

---

## 8. 문서, 표, 차트, GUI, 인포그래픽이 핵심 전장으로 떠올랐다

### 이유

실제 업무에서 멀티모달 AI가 많이 다루는 것은 자연 이미지보다도:

- PDF
- 슬라이드
- 표
- 인포그래픽
- 대시보드
- 앱 화면
- 웹 UI

같은 "인간이 정보를 구조화한 시각물"이다.

### ScreenAI가 상징하는 것

Google의 `ScreenAI`는 UI와 infographics를 이해하는 vision-language model이다. 이 모델은 Screen Annotation이라는 새로운 태스크를 만들어 UI element의 타입, 위치, 설명을 텍스트로 기술하게 하고, 그 위에 QA, navigation, summarization 데이터를 대규모로 생성한다. 5B 모델 규모로도 UI/문서/인포그래픽 태스크에서 강한 성능을 보였다. [17]

### 왜 이게 멀티모달의 핵심인가

이 영역은 단순 object recognition보다 훨씬 복잡하다.

필요한 능력
- OCR
- layout understanding
- reading order
- chart semantics
- element grounding
- actionability

즉, 실무형 멀티모달 AI는 자연 이미지 captioning보다 `structured visual artifacts` 이해가 더 중요해지고 있다.

### 이어지는 흐름: GUI agents

이 위에서 GUI agent 연구가 급격히 커진다.

- ScreenAI
- CogAgent
- ShowUI
- OSWorld

이 계열은 결국 "화면을 보고 조작하는 agent"를 목표로 한다. [18][19][20]

멀티모달 AI가 브라우저/앱/OS를 다룰 수 있으려면, document AI와 GUI understanding이 매우 중요하다.

---

## 9. 멀티모달 AI가 에이전트화(agentic)되고 있다

### 예전 시스템

- 이미지 이해
- 질의응답
- 캡션

정도에서 멈췄다.

### 지금 시스템

이제는 다음이 중요하다.

- 무엇을 더 봐야 하는가
- 어떤 도구를 써야 하는가
- 화면 어디를 클릭해야 하는가
- 어떤 정보를 검색해 보완해야 하는가
- 불확실할 때 어떻게 검증할 것인가

즉 `observe -> reason -> act -> verify` 루프가 들어가기 시작했다.

### GUI agents가 상징하는 방향

`OSWorld`는 멀티모달 에이전트가 실제 운영체제 환경에서 얼마나 작업을 수행할 수 있는지 평가하는 벤치마크다. [21]

`ShowUI`는 아예 GUI visual agent를 위한 one VLA model을 표방한다. [20]

### 왜 중요한가

멀티모달 AI의 다음 경쟁은 "정답을 아는가"가 아니라:

- task를 끝낼 수 있는가
- 시각 입력을 행동으로 연결할 수 있는가
- 도구와 UI를 통해 외부 세계를 조작할 수 있는가

가 된다.

---

## 10. 멀티모달 AI는 물리 세계로 확장되고 있다: robotics와 VLA

### 디지털 화면을 넘어서

지금 멀티모달 AI의 확장은 두 방향이다.

1. 디지털 인터페이스 조작
2. 물리 세계 행동

### 대표 신호

Google DeepMind는 2025년 `Gemini Robotics`를 공개하면서, Gemini 2.0 기반 모델에 physical actions를 새로운 출력 modality로 추가해 robot control을 지향한다고 밝혔다. [22]

이건 중요하다.  
왜냐하면 멀티모달의 끝이 "이미지를 보고 설명하는 것"이 아니라:

- 보고
- 이해하고
- 계획하고
- 행동하는 것

으로 이동하고 있다는 뜻이기 때문이다.

### 이 흐름이 말해주는 것

멀티모달 AI는 장기적으로:

- text-image model
- speech model
- vision-language model

같은 분리된 제품군을 넘어,

- vision-language-action model
- world model
- embodied agent

방향으로 수렴할 가능성이 크다.

---

## 11. 도메인 특화 멀티모달 foundation model이 빠르게 늘고 있다

범용 모델이 강해졌다고 해서, 도메인 특화 모델이 사라지는 건 아니다.  
오히려 최근 흐름은:

- 범용 멀티모달 모델을 기반으로 하되
- 도메인 데이터와 제약을 반영한 특화 모델을 따로 두는 구조

가 더 강해지고 있다.

### 11-1. 의료

Google의 `MedGemma`와 `MedSigLIP`은 이 흐름의 대표 사례다.  
Google은 MedGemma를 자유 텍스트 생성형 작업(report generation, VQA 등)에, MedSigLIP을 classification, search, retrieval 같은 구조화 출력 작업에 적합하다고 설명한다. 또한 이들 모델은 single GPU 또는 일부는 mobile hardware에서 돌아갈 수 있다고 밝혔다. [23]

또 Google은 `AMIE gains vision`과 `Advancing Multimodal Medical Capabilities` 같은 흐름을 통해, 의료 대화와 진단에서 멀티모달 데이터 통합의 중요성을 보여주고 있다. [24][25]

### 11-2. 지리공간 / 원격탐사

Google Earth AI / Remote Sensing Foundations는 자연어 질의 기반 위성/항공 이미지 분석을 멀티모달 foundation model 관점에서 접근한다. Google은 vision-language models, open-vocabulary object detection, adaptable vision backbones를 결합해 지리공간 분석을 가속한다고 설명한다. [26]

### 왜 중요하나

도메인 특화 멀티모달은 다음 특징을 갖는다.

- 데이터 구조가 다름
- 오류 비용이 다름
- 설명 가능성 요구가 다름
- 규제/프라이버시 제약이 다름

따라서 범용 MLLM이 모든 걸 대체하기보다는, 각 산업별 foundation layer가 생기는 방향이 현실적이다.

---

## 12. 오픈소스 생태계가 멀티모달 혁신 속도를 매우 빠르게 만들고 있다

과거에는 강한 멀티모달 모델이 거의 모두 폐쇄형에 가까웠다.

하지만 지금은:

- Qwen2.5-VL
- InternVL3
- Llama 3.2 Vision 계열
- CogAgent
- ShowUI

등으로 오픈소스 생태계가 훨씬 두꺼워졌다. [5][27]

### 특히 Qwen2.5-VL이 상징하는 것

Qwen2.5-VL은:

- long-video understanding
- precise localization
- robust document parsing
- chart / diagram / layout understanding
- interactive visual agent use cases

를 전면에 내세운다. [5]

이건 오픈소스 멀티모달이 더 이상 "간단한 이미지 QA" 수준이 아니라,  
실무형 document AI, screen AI, long-video AI까지 들어왔다는 뜻이다.

### 의미

이제 멀티모달 트렌드는 상용 모델만 보면 충분하지 않다.

- 상용 모델은 제품 UX와 omni integration을 이끈다
- 오픈소스 모델은 빠른 실험, vertical adaptation, edge deployment를 이끈다

두 흐름이 동시에 산업을 밀고 있다.

---

## 13. 효율화와 온디바이스가 동시에 중요해지고 있다

거대 멀티모달 모델이 강해질수록, 반대로 다음 질문도 더 중요해진다.

- 이걸 폰에서 돌릴 수 있나
- 지연시간은 얼마나 되나
- 해상도를 올리면 토큰 비용이 얼마나 늘어나나
- 문서/차트/GUI 같은 고해상도 입력을 어떻게 감당할까

### 최근 신호

Google Research는 I/O 2025에서 `Gemma3n`을 2GB RAM 수준에서도 돌아갈 수 있는 on-device 모델로 강조했다. [28]

Apple은 `FastVLM`에서 vision encoding latency와 time-to-first-token 문제를 정면으로 다룬다. 특히 text-rich image understanding은 해상도 요구가 커서 비전 인코딩 병목이 심하다는 점을 분석한다. [29]

Google는 YouTube Shorts 실시간 효과 사례에서 큰 teacher model의 능력을 작은 student model로 distill해, 폰에서 직접 프레임 단위 처리하는 구조를 공개했다. [30]

### 이 흐름의 의미

멀티모달 AI는 무조건 더 큰 모델로만 가지 않는다.

실제 시스템은 점점 다음 이원 구조를 취한다.

- 클라우드: 큰 omni / reasoning / planning model
- 엣지: 작고 빠른 specialized model

즉, 미래의 멀티모달 UX는 `거대 범용 모델 + 경량 실행 모델`의 조합일 가능성이 높다.

---

## 14. 멀티모달 안전성은 더 어려워지고 있다

텍스트 모델 안전성도 어렵지만, 멀티모달은 더 복잡하다.

이유
- 이미지 입력이 포함됨
- 비디오와 오디오로 맥락이 길어짐
- 생성 결과가 시각적으로 매우 설득력 있음
- 현실 세계의 사람/문서/의료/공공 정보를 다루게 됨

### 14-1. hallucination

멀티모달 hallucination은 여전히 핵심 문제다.

`AMBER`는 MLLM hallucination을 존재, 속성, 관계 차원에서 평가할 수 있는 benchmark를 제안했다. [31]

`Visual Hallucinations of Multi-modal LLMs`는 VHTest를 통해 다양한 시각적 hallucination 사례를 생성했고, 기존 모델들이 여전히 큰 비율로 잘못된 세부사항을 상상한다고 보고했다. [32]

2025년 survey인 `A Survey of Multimodal Hallucination Evaluation and Detection`은 이 문제를 faithfulness와 factuality 관점에서 체계화했다. [33]

### 14-2. provenance와 생성 콘텐츠 안전

OpenAI는 4o image generation에서 생성 이미지에 `C2PA metadata`를 포함한다고 밝혔다. [7]

이건 중요하다.  
멀티모달 생성이 보편화될수록:

- 어디서 만들어졌는가
- 편집 이력은 무엇인가
- 실제 사진과 합성 이미지를 어떻게 구분할 것인가

가 핵심 사회적 이슈가 되기 때문이다.

### 14-3. factuality

Google Research는 2025 I/O 관련 글에서 multimodal factuality를 Imagen4, Gemini 2.5, AI avatars in Vids 등과 연결해 강조했다. [28]

즉, 앞으로 멀티모달 품질에서 "그럴듯함"만으로는 부족하고:

- factually grounded
- visually grounded
- temporally grounded

해야 한다.

---

## 15. 평가 기준이 달라지고 있다

예전에는 image caption BLEU, retrieval recall, VQA accuracy 정도가 많이 쓰였다.  
지금은 그것만으로 모델을 설명할 수 없다.

### 현재 중요한 평가 축

- multimodal knowledge reasoning
- OCR + layout + chart understanding
- long-video analysis
- spatial reasoning
- hallucination
- screen / UI interaction
- agent task completion
- multimodal factuality
- latency / memory / deployability

### 대표 벤치마크 흐름

- `MMMU`: 전문가 수준 멀티모달 이해/추론 [11]
- `MMMU-Pro`: 텍스트 shortcut 제거, 더 엄격한 평가 [12]
- `Video-MME`: 장/단기 영상, 자막/오디오 포함 평가 [3]
- `AMBER`: hallucination 평가 [31]
- `OSWorld`: 멀티모달 에이전트의 실제 OS 작업 수행 [21]
- `Mind the Gap`: spatial reasoning 평가 [10]

### 의미

앞으로 "좋은 멀티모달 모델"은 단일 스코어로 설명되지 않는다.  
최소한 아래 두 축은 분리해서 봐야 한다.

- perception quality
- grounded reasoning / action quality

---

## 16. 2024~2026 트렌드를 기술 축별로 다시 압축하면

### 축 A. Native / Omni Multimodality

대표 사례
- GPT-4o [1]
- Gemini 계열 [2][31]

핵심 의미
- 멀티모달이 add-on이 아니라 모델 본체가 됨

### 축 B. Video-First Multimodal AI

대표 사례
- Video-MME [3]
- V-JEPA 2 [4]
- Qwen2.5-VL [5]

핵심 의미
- 이미지 중심에서 시간 중심으로 이동

### 축 C. Unified Understanding + Generation

대표 사례
- 4o image generation [7]
- AToken [9]
- MANZANO [8]

핵심 의미
- 보고 이해하는 능력과 만들어내는 능력이 결합

### 축 D. Structured Visual Intelligence

대표 사례
- ScreenAI [17]
- chart/doc/layout parsing 계열

핵심 의미
- 자연 이미지보다 문서/화면/표/도식 이해가 더 중요해짐

### 축 E. Agentic Multimodality

대표 사례
- OSWorld [21]
- ShowUI [20]
- CogAgent [19]

핵심 의미
- 멀티모달 모델이 답만 하는 게 아니라 작업을 수행함

### 축 F. Embodied / Action-Oriented Multimodality

대표 사례
- Gemini Robotics [22]
- VLA 계열

핵심 의미
- perception에서 action으로 연결

### 축 G. Domain Multimodal Foundations

대표 사례
- MedGemma / MedSigLIP [23]
- biomedical Gemini / Mosaic [24]
- geospatial multimodal models [26]

핵심 의미
- 산업별 멀티모달 스택이 분화

### 축 H. Efficient / Open / Edge Multimodality

대표 사례
- Gemma3n [28]
- FastVLM [29]
- on-device distillation pipelines [30]
- Qwen2.5-VL / InternVL3 [5][27]

핵심 의미
- 폐쇄형 대모델과 별개로 소형화·오픈화도 가속

---

## 17. 앞으로 12~24개월 동안 특히 볼 만한 변화

### 17-1. 비디오와 오디오가 텍스트+이미지보다 더 중요해질 가능성

현재 많은 사람들이 멀티모달을 "이미지 첨부 챗봇" 정도로 이해하지만,  
실제 차세대 승부처는:

- long video
- real-time audio
- streaming interaction

일 가능성이 높다.

### 17-2. 멀티모달 reasoning은 perception보다 더 오래 어려울 가능성

OCR, captioning, doc parsing은 계속 좋아지겠지만:

- 공간 추론
- 장기 인과 추론
- uncertainty-aware multimodal reasoning

은 더 느리게 개선될 가능성이 높다.

### 17-3. GUI agent와 physical agent가 멀티모달의 큰 응용 축이 될 가능성

디지털 세계에서는:
- screen/OS/browser agent

물리 세계에서는:
- robot / VLA / embodied assistant

가 멀티모달 AI의 주류 응용이 될 수 있다.

### 17-4. 도메인 특화 멀티모달 모델 수요 확대

의료, 법률, 제조, 공공, 지리공간, 금융 같은 분야는:

- 자체 데이터
- 자체 평가
- 자체 규제

가 있어서, 범용 멀티모달 모델만으로는 충분하지 않을 가능성이 높다.

### 17-5. unified model vs modular system 경쟁

앞으로 중요한 질문은 이거다.

- 하나의 거대한 omni model이 다 할 것인가
- 아니면 encoder / tokenizer / planner / tool / executor가 느슨하게 결합된 시스템이 더 실용적인가

현재는 둘 다 공존 중이다.

---

## 18. 개인적 결론

지금 멀티모달 AI의 본질적 변화는 다음 한 문장으로 정리할 수 있다.

`모델이 여러 감각을 처리하는 단계`에서  
`세계와 상호작용할 수 있는 공통 인터페이스를 만드는 단계`로 넘어가고 있다.

즉, 멀티모달 AI는 더 이상 "이미지도 읽는 LLM"이 아니다.

이제는:

- 보고
- 듣고
- 읽고
- 이해하고
- 만들고
- 조작하고
- 행동하는

시스템 계층으로 이동 중이다.

그리고 그 과정에서 가장 중요한 병목은 단순 perception이 아니라  
`grounded reasoning`, `temporal consistency`, `actionability`, `factuality`다.

---

## 19. 실무 관점에서 읽는 법

### 제품 기획자라면

우선 봐야 할 것
- omni UX
- voice interface
- doc/screen understanding
- agent workflows
- provenance / safety

### 연구자라면

우선 봐야 할 것
- unified tokenizer
- video self-supervision
- spatial reasoning
- multimodal hallucination
- benchmark design

### 스타트업 / 엔지니어라면

우선 봐야 할 것
- open-source VLM ecosystem
- long-video/document models
- fine-tuning / adapter / distillation
- edge deployment
- tool-using agent architecture

---

## 20. 1차 소스 / 주요 참고 자료

[1] OpenAI, `Hello GPT-4o`  
https://openai.com/index/hello-gpt-4o

[2] Google DeepMind, `Gemini 2.5 Flash`  
https://deepmind.google/en/models/gemini/flash/

[3] `Video-MME: The First-Ever Comprehensive Evaluation Benchmark of Multi-modal LLMs in Video Analysis`  
https://arxiv.org/abs/2405.21075

[4] `V-JEPA 2: Self-Supervised Video Models Enable Understanding, Prediction and Planning`  
https://arxiv.org/abs/2506.09985

[5] `Qwen2.5-VL Technical Report`  
https://arxiv.org/abs/2502.13923

[6] OpenAI, `Introducing next-generation audio models in the API`  
https://openai.com/index/introducing-our-next-generation-audio-models/

[7] OpenAI, `Introducing 4o Image Generation`  
https://openai.com/index/introducing-4o-image-generation/

[8] Apple, `MANZANO: A Simple and Scalable Unified Multimodal Model with a Hybrid Vision Tokenizer`  
https://machinelearning.apple.com/research/manzano

[9] Apple, `AToken: A Unified Tokenizer for Vision`  
https://machinelearning.apple.com/research/atoken

[10] `Mind the Gap: Benchmarking Spatial Reasoning in Vision-Language Models`  
https://arxiv.org/abs/2503.19707

[11] `MMMU: A Massive Multi-discipline Multimodal Understanding and Reasoning Benchmark for Expert AGI`  
https://arxiv.org/abs/2311.16502

[12] `MMMU-Pro: A More Robust Multi-discipline Multimodal Understanding Benchmark`  
https://arxiv.org/abs/2409.02813

[13] `Mind with Eyes: from Language Reasoning to Multimodal Reasoning`  
https://arxiv.org/abs/2503.18071

[14] `SigLIP 2: Multilingual Vision-Language Encoders with Improved Semantic Understanding, Localization, and Dense Features`  
https://arxiv.org/abs/2502.14786

[15] Apple, `4M: Massively Multimodal Masked Modeling`  
https://machinelearning.apple.com/research/massively-multimodal

[16] Apple, `4M-21: An Any-to-Any Vision Model for Tens of Tasks and Modalities`  
https://machinelearning.apple.com/research/vision-model

[17] Google Research, `ScreenAI: A visual language model for UI and visually-situated language understanding`  
https://research.google/blog/screenai-a-visual-language-model-for-ui-and-visually-situated-language-understanding/

[18] `A Survey on (M)LLM-Based GUI Agents`  
https://arxiv.org/abs/2504.13865

[19] `CogAgent: A Visual Language Model for GUI Agents`  
https://arxiv.org/abs/2312.08914

[20] `ShowUI: One Vision-Language-Action Model for GUI Visual Agent`  
https://arxiv.org/abs/2411.17465

[21] `OSWorld: Benchmarking Multimodal Agents for Open-Ended Tasks in Real Computer Environments`  
https://arxiv.org/abs/2404.07972

[22] Google DeepMind, `Gemini Robotics brings AI into the physical world`  
https://deepmind.google/blog/gemini-robotics-brings-ai-into-the-physical-world/

[23] Google Research, `MedGemma: Our most capable open models for health AI development`  
https://research.google/blog/medgemma-our-most-capable-open-models-for-health-ai-development/

[24] Google Research, `AMIE gains vision: A research AI agent for multi-modal diagnostic dialogue`  
https://research.google/blog/amie-gains-vision-a-research-ai-agent-for-multi-modal-diagnostic-dialogue/

[25] `Advancing Multimodal Medical Capabilities`  
https://arxiv.org/abs/2405.03162

[26] Google Research, `Google Earth AI: Unlocking geospatial insights with foundation models and cross-modal reasoning`  
https://research.google/blog/google-earth-ai-unlocking-geospatial-insights-with-foundation-models-and-cross-modal-reasoning/

[27] `InternVL3: Exploring Advanced Training and Test-Time Recipes for Open-Source Multimodal Models`  
https://arxiv.org/abs/2504.10479

[28] Google Research, `Google Research at Google I/O 2025`  
https://research.google/blog/google-research-at-google-io-2025/

[29] Apple, `FastVLM: Efficient Vision Encoding for Vision Language Models`  
https://machinelearning.apple.com/research/fast-vision-language-models

[30] Google Research, `From massive models to mobile magic: The tech behind YouTube real-time generative AI effects`  
https://research.google/blog/from-massive-models-to-mobile-magic-the-tech-behind-youtube-real-time-generative-ai-effects/

[31] `AMBER: An LLM-free Multi-dimensional Benchmark for MLLMs Hallucination Evaluation`  
https://arxiv.org/abs/2311.07397

[32] `Visual Hallucinations of Multi-modal Large Language Models`  
https://arxiv.org/abs/2402.14683

[33] `A Survey of Multimodal Hallucination Evaluation and Detection`  
https://arxiv.org/abs/2507.19024

---

## 21. 짧은 결론

멀티모달 AI는 2026년 현재 세 가지 경쟁을 동시에 하고 있다.

1. `누가 더 많이 볼 수 있는가`
2. `누가 더 깊게 이해할 수 있는가`
3. `누가 더 잘 행동할 수 있는가`

오늘 기준으로는 1번은 많이 좋아졌고,  
2번은 아직 병목이 많으며,  
3번은 agent와 robotics 쪽에서 막 본격적으로 열리고 있다.

따라서 지금 멀티모달 AI를 볼 때는 "이미지도 되는 모델"이 아니라  
`perception -> reasoning -> generation -> action` 전체 파이프라인으로 보는 것이 가장 정확하다.
