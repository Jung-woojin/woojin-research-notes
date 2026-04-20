# 2026 컴퓨터비전 기술 트렌드 리포트

기준일: 2026-04-20

이 문서는 2024~2026 흐름을 중심으로, 현재 컴퓨터비전(CV) 분야에서 실제로 연구 축을 바꾸고 있는 기술 트렌드를 정리한 보고서다.  
단순히 "요즘 뜨는 키워드"를 나열하는 대신, 아래 기준으로 정리했다.

- 연구 패러다임을 바꿨는가
- 산업 적용 속도가 빠른가
- 기존 CV 파이프라인을 대체하거나 흡수하고 있는가
- 2025~2026에도 성장세가 이어질 가능성이 높은가

---

## 한 줄 요약

지금 컴퓨터비전은 더 이상 `분류 / 검출 / 분할` 같은 개별 태스크 중심 분야가 아니다.  
핵심 축은 다음 세 가지로 재편되고 있다.

1. `비전 파운데이션 모델화`
2. `이미지 중심에서 비디오/시간/행동 중심으로 이동`
3. `2D 인식에서 3D/공간지능/물리세계 이해로 이동`

즉, 예전 CV가 "이미지 안에 뭐가 있나"를 맞히는 기술이었다면,  
지금 CV는 "이 장면이 어떻게 움직이고, 어디에 있고, 다음에 무엇이 일어날지, 그리고 그 이해를 실제 행동으로 연결할 수 있는가"로 넘어가고 있다.

---

## 1. 가장 큰 흐름: CV가 멀티모달 파운데이션 모델의 하위 모듈이 아니라 핵심 감각 계층이 되고 있다

### 예전 흐름

전통적인 CV는 보통 아래처럼 태스크가 분리되어 있었다.

- 이미지 분류
- 객체 검출
- 시맨틱/인스턴스 분할
- 포즈 추정
- OCR
- 추적

각 문제마다 데이터셋, 모델 구조, 헤드, 손실함수가 따로 있었다.  
좋은 엔지니어링은 "태스크별 최적화"였다.

### 지금 흐름

지금은 점점 아래 방식으로 바뀌고 있다.

- 하나의 비전 모델이 여러 태스크를 프롬프트로 수행
- 비전 출력이 더 이상 클래스 logits이 아니라 `텍스트`, `좌표`, `마스크`, `행동 토큰`, `공간 토큰`이 됨
- 비전 인코더가 LLM/VLM/VLA의 감각 프론트엔드 역할을 수행
- 대규모 약지도, 자동 주석, 웹 규모 데이터, 합성 데이터가 훈련 기반이 됨

대표적인 예가 `Florence-2`다. 이 모델은 detection, grounding, segmentation, captioning 등을 하나의 prompt-based representation으로 묶는다. Microsoft는 이를 위해 `FLD-5B`라는 54억 개 수준의 시각 주석을 1억 2,600만 장 이미지에 대해 구축했다고 보고했다.  
이건 단순히 "멀티태스크 모델 하나 나왔다" 수준이 아니라, CV를 점점 `instruction-following visual interface`로 바꾸는 흐름의 상징이다. [1]

### 왜 중요하나

- 다운스트림마다 모델을 따로 두는 방식의 비용이 너무 큼
- 실제 제품은 하나의 화면/영상에서 여러 종류의 판단을 동시에 해야 함
- LLM 기반 에이전트가 세계를 보려면 비전이 `언어와 연결 가능한 표현`을 줘야 함

### 실무적 의미

앞으로 CV 조직은 `detector 팀`, `segmenter 팀`, `OCR 팀`처럼 쪼개지기보다:

- 공통 vision encoder
- task prompting / adapter
- domain fine-tuning
- evaluation stack

중심으로 재편될 가능성이 높다.

---

## 2. "Open-vocabulary + promptable perception"이 기본값이 되고 있다

### 무엇이 바뀌었나

예전 검출/분할 모델은 미리 정의된 클래스 집합 안에서만 동작했다.

- COCO 80 classes
- VOC 20 classes
- 특정 의료 병변 class
- 특정 공장 불량 유형

이제는 사용자 프롬프트, 클릭, 박스, 예시 이미지, 텍스트 설명 등으로 대상 개념을 지정하는 `promptable perception`이 중심이 됐다.

### 핵심 신호

`SAM 2`는 이 전환을 상징하는 대표 사례다.  
Meta는 SAM 2를 이미지와 비디오 모두에서 동작하는 `promptable visual segmentation` foundation model로 제시했고, 실시간 비디오 처리를 위한 streaming memory를 도입했다. 논문에서는:

- 비디오 분할에서 기존 접근보다 더 적은 상호작용으로 더 높은 정확도
- 이미지 분할에서 기존 SAM 대비 더 정확하고 6배 빠름

을 보고했다. [2]

### 왜 이게 중요한가

컴퓨터비전이 점점 "고정 클래스 예측기"가 아니라 "시각적 질의응답 엔진"이 되기 때문이다.

예:

- `"붉은 우산만 찾아"`
- `"왼쪽 위에 있는 컵을 계속 추적해"`
- `"홍수 때문에 잠긴 도로만 골라"`
- `"이 의료 영상에서 병변처럼 보이는 영역만 표시해"`

이건 detection, grounding, segmentation, tracking이 하나로 합쳐지는 방향이다.

### 기술적으로 중요한 변화

- class-specific head보다 general prompt encoder가 중요해짐
- annotation도 closed-set label보다 interactive signal이 중요해짐
- 비디오에서는 memory design이 성능의 핵심이 됨

### 앞으로의 관전 포인트

- segmentation + tracking + grounding의 완전한 통합
- text prompt뿐 아니라 example prompt, multi-modal prompt 확장
- 의료 / 산업 / 로보틱스처럼 분포가 다른 도메인에서 얼마나 잘 적응하는가

---

## 3. CV의 무게중심이 이미지에서 비디오로 이동하고 있다

### 예전 문제 설정

과거 CV의 중심 벤치마크는 대부분 정지 이미지였다.

- ImageNet
- COCO
- ADE20K
- OpenImages

하지만 현실 세계는 정지 이미지가 아니라 `시간이 흐르는 연속 장면`이다.

### 지금 변화

2025년 전후의 가장 중요한 변화 중 하나는 `video-first representation learning`이다.

대표적으로 `V-JEPA 2`는:

- 100만 시간 이상의 인터넷 비디오와 이미지로 사전학습
- action-free joint-embedding-predictive architecture 사용
- motion understanding, action anticipation에서 강한 성능
- 더 나아가 planning과 physical reasoning 방향까지 연결

을 제시했다. [3]

### 왜 이 흐름이 중요한가

비디오는 단순히 이미지 여러 장이 아니다. 비디오는 다음 정보를 담는다.

- 시간적 일관성
- 움직임 패턴
- 원인과 결과의 단서
- 물체 상호작용
- 행동 전개

즉, 비디오는 `지각 + 예측 + planning`으로 이어지는 가장 자연스러운 데이터 형태다.

### 기술적 의미

비디오 중심 CV는 아래 문제들을 다시 중요하게 만든다.

- temporal correspondence
- long-context memory
- future prediction
- action anticipation
- object permanence
- partial observability

그리고 이건 결국 로보틱스, 자율주행, embodied AI와 직접 연결된다.

### 연구 패러다임 변화

중요한 포인트는 `pixel reconstruction` 자체보다,  
`representation space에서 무엇을 예측하느냐`가 더 중요해지고 있다는 점이다.

즉, "다음 프레임 픽셀을 똑같이 복원"하는 것보다:

- 어떤 상태가 유지되는가
- 어떤 변화가 일어날 것인가
- 행동 가능성은 무엇인가

를 latent level에서 잡는 방향이 커지고 있다.

---

## 4. Vision-Language Model은 강해졌지만, "공간 추론"은 여전히 큰 병목이다

### 착시를 주는 부분

요즘 VLM은 겉으로 보면 엄청 강하다.

- 이미지 설명
- OCR
- 차트 해석
- 시각 질의응답
- grounding

하지만 이 능력들이 `진짜 공간지능`을 의미하는 건 아니다.

### 현재 병목

`Mind the Gap: Benchmarking Spatial Reasoning in Vision-Language Models`는 2025년에 이 문제를 정면으로 보여줬다.  
이 연구는 spatial relations, orientation/navigation, mental rotation, spatial visualization 등으로 공간 추론을 나눠서 평가했고, 13개 최신 VLM을 분석한 결과 평균 성능이 거의 랜덤 추측 수준에 가깝다고 보고했다. [4]

이건 굉장히 중요하다.

왜냐하면 지금의 멀티모달 모델은:

- "무엇이 보이는가"는 꽤 잘함
- "어디에 있는가"는 어느 정도 함
- "3D로 어떤 관계인가"
- "시점이 바뀌면 어떻게 보일까"
- "왼쪽/오른쪽/앞/뒤/회전/가림 관계가 실제로 어떤가"

이런 부분에서는 아직 취약하다는 뜻이기 때문이다.

### 그래서 나타나는 연구 방향

이 병목 때문에 최근 CV/VLM 연구는 아래 방향으로 움직이고 있다.

- 카메라 파라미터와 geometry를 모델에 더 직접적으로 넣기
- depth / point / camera token 통합
- multi-view와 video를 함께 학습
- localization을 reasoning의 전단계로 보고 점진적으로 훈련
- dense feature를 복원해서 2D semantic과 3D structure를 함께 다루기

`SigLIP 2`가 localization, dense features, multilingual understanding, fairness까지 함께 개선한 것도 이런 흐름과 맞닿아 있다. 즉, 단순 contrastive embedding이 아니라, `downstream spatial/dense task에 바로 쓸 수 있는 representation`이 중요해지는 것이다. [5]

### 실무적 함의

공간지능이 약하면 아래에서 문제가 난다.

- 로봇이 "컵 뒤의 숟가락"을 못 찾음
- 자율주행이 장면은 이해하지만 폐쇄 루프 계획에서 실수함
- 멀티뷰 3D reconstruction consistency가 흔들림
- 영상 편집/생성에서 구조 보존이 약함

즉, 앞으로 CV의 진짜 격전지는 `semantic understanding`이 아니라 `grounded spatial intelligence`다.

---

## 5. 3D/4D 비전은 이제 "부가 분야"가 아니라 핵심 축이다

### 왜 3D가 다시 중요해졌나

오랫동안 3D 비전은 연구적으로 중요했지만, 실제 시스템에 넣기엔 너무 느리거나 복잡했다.

하지만 최근에는 상황이 크게 달라졌다.

- AR/VR/XR
- 로보틱스
- 자율주행
- 디지털 트윈
- 생성형 3D/비디오
- 공간 컴퓨팅

이 모두가 `2D 이해만으로는 부족한 문제`이기 때문이다.

### 구조적 전환점: Gaussian Splatting

`3D Gaussian Splatting for Real-Time Radiance Field Rendering`은 이 흐름의 분기점이었다.  
이 논문은:

- 장면을 3D Gaussian으로 표현
- density control과 anisotropic covariance 최적화
- visibility-aware rasterization 기반 실시간 렌더링

으로 고품질 novel view synthesis를 훨씬 실용적인 영역으로 끌어왔다. [6]

### 왜 3DGS가 큰 사건이었나

기존 NeRF 계열은 품질은 좋았지만 느리고 무거웠다.  
3DGS는 explicit representation으로 가면서:

- 학습 속도
- 렌더링 속도
- 시스템 통합 가능성

을 크게 끌어올렸다.

그래서 지금은 3DGS가 단순 렌더링 기법이 아니라:

- reconstruction
- SLAM
- telepresence
- simulation
- robotics perception
- dynamic scene modeling

까지 확장되는 기반 기술이 되고 있다.

### 다음 단계

현재 3DGS/scene representation 쪽의 다음 키워드는 아래다.

- compression
- dynamic / 4D scene
- lighting / HDR / exposure robustness
- semantics-aware scene representation
- editable scene representation
- robotics-ready scene memory

즉, 목표는 예쁜 view synthesis가 아니라 `기계가 사용할 수 있는 장면 메모리`다.

---

## 6. World Model과 Physical AI가 CV를 "보는 기술"에서 "예측하는 기술"로 밀고 있다

### 흐름의 핵심

컴퓨터비전은 이제 perception만 잘해서는 부족하다.  
실제 physical AI에서는 아래를 함께 풀어야 한다.

- 현재 상태 이해
- 미래 상태 예측
- 어떤 행동이 안전한지 추정
- 계획 결과를 평가

이 때문에 `world model`이 CV의 중심 키워드로 올라왔다.

### 왜 world model이 CV 트렌드인가

world model은 본질적으로 시각 세계의 구조를 학습하는 문제다.  
즉:

- object permanence
- dynamics
- causality의 근사
- action-conditioned prediction

을 다뤄야 하므로 CV와 떼려야 뗄 수 없다.

### 최근 신호

- `V-JEPA 2`는 self-supervised video model을 understanding, prediction, planning과 연결했다. [3]
- NVIDIA는 2025년에 `Cosmos World Foundation Models`와 synthetic data toolchain을 발표하면서 physical AI를 위한 world generation과 controllable synthetic data를 전면에 내세웠다. [7]

### 여기서 중요한 점

이 흐름은 단순 생성형 AI와 다르다.

목표가 "멋진 영상 만들기"가 아니라:

- 조작 가능한 미래 시나리오 생성
- simulation-based training
- post-training for robots / AV
- rare event augmentation
- safety evaluation

쪽에 있다는 점이 핵심이다.

### 앞으로의 핵심 질문

- 모델이 진짜 물리 법칙을 배우는가, 아니면 통계적 패턴만 배우는가
- 장기 계획에서 일관성이 유지되는가
- perception, action, simulation이 같은 latent space로 묶일 수 있는가

---

## 7. Robotics에서는 Vision-Language-Action(VLA)가 새로운 표준 축으로 떠오르고 있다

### 왜 CV 관점에서 중요하나

VLA는 로보틱스 모델처럼 보이지만, 사실 CV 입장에서는:

- 비전 표현이 행동으로 직접 연결되는 첫 대규모 실험장
- visual grounding의 실전형 벤치마크
- open-world perception이 행동 성공률로 검증되는 무대

라는 점에서 매우 중요하다.

### 핵심 전환

예전 로봇 시스템:

- perception module
- state estimator
- planner
- controller

가 분리되어 있었다.

지금 VLA는 이 일부를 하나의 모델 또는 밀접한 latent interface로 묶으려 한다.

### 대표 흐름

`OpenVLA`는 2024년 공개된 대표 오픈소스 VLA다.  
970k real-world robot demonstrations로 학습됐고, DINOv2와 SigLIP 특징을 쓰는 visual encoder를 기반으로 한다. 논문은 폐쇄형 대형 모델 대비 더 작은 파라미터로도 강한 조작 성능과 fine-tuning 가능성을 보여줬다. [8]

이어 2025년 `OpenVLA-OFT`는:

- action decoding 방식
- action representation
- learning objective

같은 adaptation design choice를 정리하면서, LIBERO benchmark에서 OpenVLA 평균 성공률을 76.5%에서 97.1%로 높이고 action generation throughput을 26배 높였다고 보고했다. [9]

### 이게 뜻하는 것

이제 비전 모델의 가치는:

- detection mAP
- segmentation IoU

같은 정적 지표만이 아니라,

- 실제 집기 성공률
- instruction following success
- long-horizon task completion

으로 검증된다.

### CV 쪽에 주는 메시지

- "좋은 비전 표현"의 정의가 바뀌고 있음
- semantic richness만으로는 부족하고 actionability가 필요
- latency, temporal consistency, robustness가 훨씬 중요해짐

---

## 8. 자율주행도 perception stack에서 reasoning + planning 통합 방향으로 이동 중이다

### 변화의 본질

자율주행은 오랫동안 CV의 대표 응용 분야였다.  
기존에는 보통:

- detection
- lane segmentation
- map perception
- tracking
- prediction
- planning

이 분리되어 있었다.

지금은 end-to-end 또는 foundation-model-augmented planning으로 이동 중이다.

### 최근 신호

`ORION`은 2025년 기준, VLM reasoning과 generative planner를 결합해 closed-loop driving 성능을 올리는 흐름을 보여준다. 논문은 reasoning space와 numerical trajectory output 사이의 간극을 문제로 지적하고, long-term context aggregation + LLM reasoning + generative planning을 함께 최적화했다. [10]

### 왜 중요한가

이건 자율주행에서 CV가 더 이상 `센서 인식기`에 머무르지 않는다는 뜻이다.

앞으로는:

- 장면 이해
- 위험 요인 설명 가능성
- counterfactual evaluation
- multi-objective tradeoff

까지 비전 표현이 연결되어야 한다.

### 향후 트렌드

- sensor fusion + VLM reasoning
- simulation-heavy closed-loop evaluation
- synthetic rare-event augmentation
- interpretable planning traces

---

## 9. 합성 데이터(synthetic data)는 이제 보조 수단이 아니라 핵심 인프라다

### 왜 커졌나

현실 데이터만으로는 아래가 너무 어렵다.

- 희귀 상황 수집
- 정밀 주석 비용
- 프라이버시/규제
- long-tail coverage
- 멀티뷰/3D/robotics 데이터 구성

그래서 synthetic data가 다시 전면으로 올라왔다.

### 예전과 다른 점

예전 합성 데이터는 "가짜 데이터라 품질이 낮다"는 이미지가 강했다.  
지금은 다르다.

- 시뮬레이션 품질 향상
- diffusion / generative rendering 발전
- domain randomization 고도화
- digital twin / Omniverse / world model 결합

으로 `대규모 controllable data factory` 개념으로 바뀌고 있다.

### 참고할 만한 흐름

2024년 synthetic data augmentation survey는 CV 전반에서 3D graphics, neural rendering, GAN/생성형 AI 기반 합성 데이터 전략을 체계적으로 정리했다. [11]

NVIDIA는 2025년에 robot/AV post-training용 `massive, controllable synthetic data generation engines`를 전면에 내세웠다. [7]

### 의미

앞으로 데이터 전략은 대체로 아래 조합이 된다.

- real data
- pseudo-labeled data
- synthetic data
- simulation rollouts
- user interaction data

즉, 모델 경쟁력은 architecture 못지않게 `data engine`에서 갈린다.

### 특히 강한 분야

- 로보틱스
- 자율주행
- 산업 검사
- 리테일/가상착장
- 원격탐사
- 의료영상의 일부 설정

---

## 10. CV는 점점 더 "도메인 파운데이션 모델"로 분화되고 있다

범용 모델이 강해졌다고 해서 도메인 특화가 사라지는 건 아니다.  
오히려 최근 흐름은:

- 범용 비전/멀티모달 모델을 베이스로 쓰되
- 특정 도메인에서 데이터, 평가, 안전 요구사항이 너무 달라
- 결국 domain foundation model이 따로 생기는 방향

이다.

### 10-1. 의료영상

Google는 2025년에 `MedGemma`와 `MedSigLIP`을 통해 의료용 멀티모달/인코더 계열 모델을 공개했다. 설명에 따르면:

- MedGemma는 report generation, VQA 같은 자유 텍스트 생성형 작업
- MedSigLIP은 classification, search, retrieval 같은 구조화 출력형 작업

에 적합하도록 분화되어 있다. [12]

이건 중요한 신호다.  
의료는 단순히 일반 VLM을 가져다 쓰는 것으로 끝나지 않고:

- 긴 문맥 임상 정보
- 시계열 EHR
- 다중 모달 검사
- 보수적 오류 비용

을 고려해야 하기 때문이다.

### 10-2. 원격탐사 / 지리공간 비전

Google의 `Google Earth AI` / `Remote Sensing Foundations` 쪽 흐름은 원격탐사도 완전히 foundation-model 시대로 들어갔다는 신호다.  
여기서는:

- vision-language models
- open-vocabulary object detection
- adaptable vision backbones

을 통해 자연어 질의 기반 위성영상 분석을 밀고 있다. [13]

예를 들어 `"storm 이후 침수된 도로를 찾아"` 같은 질의가 가능한 방향이다.

### 10-3. 자율주행 / embodied / 산업

이쪽은:

- 지각 정확도
- latency
- 폐쇄루프 안정성
- 합성데이터 적응성
- 안전 평가 가능성

이 중요하기 때문에, 범용 모델을 그대로 쓰기보다 도메인 적응된 foundation stack이 계속 늘어날 가능성이 높다.

---

## 11. 비전 모델도 "사용자 맞춤형 튜닝" 시대로 가고 있다

파운데이션 모델이 커질수록 오히려 모두가 공통 모델을 그대로 쓰지는 않는다.  
최근 중요한 흐름은 `작은 양의 시각 데이터로 비전 능력을 제품 맞춤형으로 튜닝`하는 것이다.

OpenAI는 2024년 `vision fine-tuning`을 도입하면서, 이미지와 텍스트로 GPT-4o 계열 비전 성능을 커스터마이즈할 수 있게 했다. 공개 사례에 따르면:

- Grab은 100개 예시로 lane count 정확도와 speed limit sign localization을 개선
- Automat은 UI element localization 성공률을 크게 끌어올림

같은 식의 vertical adaptation이 가능해졌다. [14]

### 이 흐름의 의미

앞으로 CV는 아래 두 층으로 나뉠 가능성이 크다.

- 공통 base visual foundation model
- 기업/도메인/제품별 visual adaptation layer

즉, "새 모델을 처음부터 훈련"보다 "적은 데이터로 빠르게 적응"이 더 중요한 경쟁 포인트가 된다.

---

## 12. 엣지/온디바이스 비전은 다시 매우 중요해지고 있다

### 이유

모델은 커졌지만, 실제 많은 비전 시스템은 아직도 현장에서 돌아야 한다.

- 스마트폰 카메라
- AR glasses
- 공장 설비
- 차량
- 드론
- 보안 카메라
- 웨어러블

이 환경에서는 latency, bandwidth, privacy, battery가 결정적이다.

### 최근 흐름

Google는 2025년 YouTube Shorts 실시간 효과 사례에서:

- 큰 모델의 능력을 작은 student model에 distill
- 단일 목적에 맞춰 scope를 좁혀
- 프레임 단위로 폰에서 직접 처리
- MediaPipe / Google AI Edge 파이프라인으로 배포

하는 구조를 설명했다. [15]

### 이게 의미하는 것

CV의 미래는 무조건 거대 클라우드 모델만이 아니다.  
오히려 아래의 조합이 유력하다.

- 클라우드의 강한 teacher / planner
- 엣지의 작은 fast student / executor

즉, `foundation model + distillation + edge deployment`가 실전형 구조가 된다.

### 기술 포인트

- token pruning / compact visual encoder
- knowledge distillation
- architecture search
- streaming inference
- memory-aware deployment
- mixed precision / quantization

---

## 13. 생성형 이미지/비디오 기술이 CV를 잠식하는 게 아니라 재정의하고 있다

### 흔한 오해

생성형 AI는 CV와 다른 분야처럼 보이지만, 실제로는 경계가 급격히 사라지고 있다.

왜냐하면 생성도 결국:

- 장면 이해
- 구조 보존
- 레이아웃 제어
- 객체 정체성 유지
- 시간 일관성

을 필요로 하기 때문이다.

### 최근 중요한 신호

OpenAI의 4o image generation 관련 설명은 단순히 이미지 생성기가 좋아졌다는 얘기가 아니라:

- instruction following
- 편집 정합성
- safety moderation
- provenance(C2PA)

가 함께 중요해졌다는 것을 보여준다. [16]

즉, 생성형 모델도 결국 강한 CV capability가 없으면 쓸 만한 편집기나 제작 도구가 되기 어렵다.

### CV 관점에서 보면

생성형 모델은 이제:

- 데이터 생성기
- augmentation 엔진
- annotation 보조기
- 3D/비디오 시뮬레이터
- 편집 기반 evaluation tool

로 들어오고 있다.

즉, 생성형 AI는 CV의 경쟁자가 아니라 CV 툴체인의 일부가 되고 있다.

---

## 14. 평가(Evaluation)가 정확도 중심에서 능력 중심으로 바뀌고 있다

예전에는 아래 지표가 중심이었다.

- top-1 accuracy
- mAP
- IoU
- F1

물론 지금도 중요하다. 하지만 그것만으로는 부족하다.

### 지금 중요해진 평가

- open-vocabulary generalization
- long-tail robustness
- spatial reasoning
- temporal consistency
- interaction efficiency
- closed-loop success
- latency / memory / energy
- safety / bias / fairness

예를 들어 spatial reasoning benchmark가 따로 중요해진 것 자체가, 이제 단순 recognition score로는 모델의 실제 능력을 설명할 수 없다는 뜻이다. [4]

### 앞으로 더 중요해질 평가

- embodied task completion
- multi-turn visual interaction
- multi-view consistency
- instruction-to-action faithfulness
- counterfactual robustness
- calibration under distribution shift

---

## 15. 안전, 공정성, 출처 증명(provenance)이 CV의 핵심 비기능 요구사항이 되고 있다

과거 CV는 "정확도만 높이면 된다"는 분위기가 강했다.  
이제는 그렇지 않다.

### 이유

- 얼굴/신원/행동 인식의 민감성
- 생성 이미지와 실제 이미지 구분 문제
- 의료/법/보안 문맥의 높은 오류 비용
- 웹 규모 데이터의 편향 문제

### 실제 변화

- 더 다양한 data curation, de-biasing을 전면에 내세우는 인코더 계열 등장 (`SigLIP 2`) [5]
- 생성 이미지에 C2PA 같은 provenance 메타데이터 부착 (`4o image generation`) [16]
- domain-specific governance가 필요한 의료/지리공간/공공영역 비전 모델 확대 [12][13]

### 앞으로의 핵심

좋은 비전 모델은 앞으로 아래를 같이 만족해야 한다.

- 성능
- 설명 가능성
- provenance
- privacy
- controllability
- regulatory fitness

---

## 16. 앞으로 1~3년을 보면 무엇이 더 강해질까

### 거의 확실한 방향

1. `이미지 전용 모델의 비중 감소`
- 이미지 모델이 사라진다는 뜻은 아니다.
- 다만 핵심 연구 관심은 video, multi-view, embodied setting으로 더 이동할 가능성이 높다.

2. `2D semantic understanding보다 spatial intelligence 경쟁 심화`
- 다음 큰 병목은 "보이는 걸 말하는 능력"이 아니라 "공간적으로 맞게 이해하는 능력"이다.

3. `CV와 로보틱스의 경계 약화`
- VLA, world model, physical AI 쪽이 계속 커질 가능성이 높다.

4. `synthetic data pipeline의 표준화`
- 데이터 수집보다 데이터 엔진 설계가 경쟁력이 된다.

5. `도메인 파운데이션 모델의 증가`
- 의료, 원격탐사, 산업, 자율주행 쪽은 일반 모델의 단순 재활용으로 끝나지 않을 가능성이 크다.

6. `온디바이스 비전의 재부상`
- 거대 모델 시대에도 엣지 추론은 오히려 더 중요해질 가능성이 높다.

### 아직 불확실한 방향

- world model이 실제 물리 이해까지 갈 수 있는가
- VLM이 spatial reasoning 한계를 얼마나 빨리 극복할 수 있는가
- end-to-end embodied model이 모듈형 시스템을 전면 대체할 수 있는가
- 생성형 데이터가 현실 데이터 의존도를 얼마나 줄일 수 있는가

---

## 17. 지금 시점에서 가장 중요한 기술 축 정리

### 핵심 축 A: Unified Vision Foundation Models

대표 키워드
- Florence-2
- prompt-based task unification
- open-vocabulary perception

핵심 의미
- 태스크별 모델 시대에서 표현 중심 시대로 이동

### 핵심 축 B: Promptable Segmentation and Tracking

대표 키워드
- SAM 2
- interactive segmentation
- streaming memory

핵심 의미
- 분할/추적/grounding 경계 붕괴

### 핵심 축 C: Video Self-Supervision and Predictive Learning

대표 키워드
- V-JEPA 2
- action anticipation
- latent prediction

핵심 의미
- perception에서 prediction/planning으로 이동

### 핵심 축 D: Spatial Intelligence and 3D Understanding

대표 키워드
- spatial reasoning bottleneck
- multi-view understanding
- dense features
- 3DGS

핵심 의미
- "무엇" 다음은 "어디/어떻게"다

### 핵심 축 E: Physical AI and VLA

대표 키워드
- OpenVLA
- OpenVLA-OFT
- robotics fine-tuning

핵심 의미
- 비전 표현이 행동 성공률로 평가되기 시작

### 핵심 축 F: Synthetic Data and Simulation

대표 키워드
- Omniverse / Cosmos
- controllable synthetic data
- digital twins

핵심 의미
- 데이터 부족을 메우는 수준이 아니라 학습 인프라 자체가 됨

### 핵심 축 G: Domain Foundation Models

대표 키워드
- MedGemma / MedSigLIP
- Remote Sensing Foundations

핵심 의미
- 범용 모델 위에 도메인별 foundation layer가 형성됨

### 핵심 축 H: Efficient / On-Device Vision

대표 키워드
- distillation
- compact student
- mobile inference
- MediaPipe / edge pipelines

핵심 의미
- 거대 모델 시대에도 현장 추론은 더 중요해짐

---

## 18. 개인적으로 봤을 때 가장 중요한 관찰

지금 컴퓨터비전의 본질적 변화는 이거다.

과거:
- 이미지를 받아 정답 라벨을 맞히는 분야

현재:
- 멀티모달 시스템의 감각 입력 계층

다가오는 단계:
- 물리 세계를 이해하고 예측하며 행동으로 연결하는 공간지능 계층

즉, CV는 독립된 태스크 묶음에서 `world interface`로 바뀌고 있다.

---

## 19. 참고 문헌 / 1차 소스

[1] Florence-2: Advancing a Unified Representation for a Variety of Vision Tasks  
https://arxiv.org/abs/2311.06242

[2] SAM 2: Segment Anything in Images and Videos  
https://arxiv.org/abs/2408.00714

[3] V-JEPA 2: Self-Supervised Video Models Enable Understanding, Prediction and Planning  
https://arxiv.org/abs/2506.09985

[4] Mind the Gap: Benchmarking Spatial Reasoning in Vision-Language Models  
https://arxiv.org/abs/2503.19707

[5] SigLIP 2: Multilingual Vision-Language Encoders with Improved Semantic Understanding, Localization, and Dense Features  
https://arxiv.org/abs/2502.14786

[6] 3D Gaussian Splatting for Real-Time Radiance Field Rendering  
https://arxiv.org/abs/2308.04079

[7] NVIDIA Announces Major Release of Cosmos World Foundation Models and Physical AI Data Tools  
https://nvidianews.nvidia.com/news/nvidia-announces-major-release-of-cosmos-world-foundation-models-and-physical-ai-data-tools

[8] OpenVLA: An Open-Source Vision-Language-Action Model  
https://arxiv.org/abs/2406.09246

[9] Fine-Tuning Vision-Language-Action Models: Optimizing Speed and Success  
https://arxiv.org/abs/2502.19645

[10] ORION: A Holistic End-to-End Autonomous Driving Framework by Vision-Language Instructed Action Generation  
https://arxiv.org/abs/2503.19755

[11] A survey of synthetic data augmentation methods in computer vision  
https://arxiv.org/abs/2403.10075

[12] MedGemma: Our most capable open models for health AI development  
https://research.google/blog/medgemma-our-most-capable-open-models-for-health-ai-development/

[13] Google Earth AI: Unlocking geospatial insights with foundation models and cross-modal reasoning  
https://research.google/blog/google-earth-ai-unlocking-geospatial-insights-with-foundation-models-and-cross-modal-reasoning/

[14] Introducing vision to the fine-tuning API  
https://openai.com/index/introducing-vision-to-the-fine-tuning-api/

[15] From massive models to mobile magic: The tech behind YouTube real-time generative AI effects  
https://research.google/blog/from-massive-models-to-mobile-magic-the-tech-behind-youtube-real-time-generative-ai-effects/

[16] Introducing 4o Image Generation  
https://openai.com/index/introducing-4o-image-generation/

---

## 20. 보고서 활용 팁

이 문서를 바로 실무 판단에 쓰려면 아래처럼 읽는 게 좋다.

- 연구 방향을 잡는다면:
  `video`, `spatial intelligence`, `3D/4D`, `world model` 축을 우선순위로 봐야 한다.

- 제품 개발 관점이라면:
  `promptable perception`, `small-data adaptation`, `on-device`, `synthetic data pipeline`이 중요하다.

- 장기 투자 관점이라면:
  `physical AI`, `domain foundation model`, `data engine`, `evaluation/safety`가 핵심이다.
