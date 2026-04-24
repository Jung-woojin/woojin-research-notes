# 2026 해무 시정 분류 논문 주제 후보 4선

기준일: 2026-04-22

## 목적

이 문서는 현재 보유한 `항만 해무 이미지 데이터`를 기준으로, `항만별 데이터를 서로 섞지 않는다`는 운영 제약 아래에서 실제로 논문으로 밀 수 있는 강한 주제 후보 4개를 다시 정리한 실전용 노트다.

현재 전제는 아래와 같다.

- 데이터는 `보통시정 - 저시정 - 해무`의 3단 분류 라벨을 가진 이미지 데이터셋이다
- 전국 11개 항 데이터가 있으나, 실제 논문 실험에 안정적으로 쓸 수 있는 항은 약 `4개 항` 정도다
- 각 항의 데이터는 `공동 학습(joint training)`에 섞지 않는다
- 즉 한 항의 모델은 그 항 데이터로만 학습하며, 다른 항 데이터는 학습에 쓰지 않는다
- 항 간 비교는 가능하지만, 그것은 `zero-shot transfer`, `few-shot adaptation`, `site-wise analysis` 형태여야 한다
- 연구자는 `CNN 기반 컴퓨터비전 / 영상처리 엔지니어` 관점에서 주제를 잡고 싶다
- 사용 가능한 연산 자원은 `H200 GPU 2장`이다

핵심 메시지는 아래다.

- 이 데이터의 가치는 단순 3분류가 아니다
- 동시에 `멀티사이트를 한데 모아 대형 일반화 모델을 만드는 것`도 현재 목표가 아니다
- 더 정직한 연구 프레임은 `항별 독립 배포`, `항 간 전이 난이도`, `site-specific uncertainty`, `경량 실전 운영성`이다

## 빠른 결론

지금 당장 가장 강한 후보는 아래 순서다.

1. `Port-Specific Fog Visibility Classification in Real-World Harbor Cameras`
2. `Cross-Port Transfer without Joint Training for Harbor Fog Classification`
3. `Uncertainty-Aware Fog Classification for Site-Specific Harbor Deployment`
4. `Lightweight CNNs for Real-Time Harbor Fog Monitoring`

이 중에서 첫 논문 1편을 고르라면 `후보 1`이 가장 안정적이다.  
데이터 제약을 과장하지 않으면서도, 각 항의 실제 난이도와 운영적 의미를 가장 정직하게 보여 줄 수 있기 때문이다.

---

## 1. 공통 연구 프레임

이번 프레임의 가장 중요한 규칙은 하나다.

`항만별 데이터를 학습 단계에서 서로 섞지 않는다.`

이 규칙이 의미하는 바는 아래와 같다.

### 학습 프로토콜

- Port A 모델은 Port A 데이터로만 학습
- Port B 모델은 Port B 데이터로만 학습
- Port C 모델은 Port C 데이터로만 학습
- Port D 모델은 Port D 데이터로만 학습

즉 `통합 train set`은 없다.

### 허용되는 비교

- 항별 독립 모델 성능 비교
- A항 모델을 B항 데이터에 그대로 적용하는 zero-shot transfer
- A항 모델을 B항 소량 데이터로 미세조정하는 few-shot adaptation
- 항별 error pattern, calibration, class imbalance 비교

### 허용되지 않는 핵심 설정

- 4개 항 전체를 섞은 공동 학습
- multi-port joint representation learning
- unified cross-port classifier를 메인 실험으로 쓰는 것

즉 이 연구의 중심은 `멀티사이트 공동학습`이 아니라 `site-specific learning and cross-site transfer analysis`다.

### 공통 권장 split

각 항에 대해 아래를 따로 만든다.

1. `Within-port train/val/test split`
- 같은 항 내부 분할
- 가능하면 시간 순서 기준 분할

2. `Cross-port transfer split`
- 예: Port A로 학습한 모델을 Port B test에 적용
- 이때 B의 train은 사용하지 않음

3. `Few-shot adaptation split`
- 예: Port A pretrained model을 Port B의 1%, 5%, 10% 데이터로만 적응

### 공통 핵심 metric

- `Macro-F1`
- `Balanced Accuracy`
- `Per-class Recall`
- `Fog Recall`
- `Low-Visibility Recall`
- `ECE`
- `Brier Score`
- `AUROC for abstention`

실전적으로는 아래 지표를 더 강하게 본다.

- 해무 recall
- `저시정 + 해무` 묶음 recall
- calibration under site change
- 적절한 threshold에서의 reject 성능

---

## 2. 후보 1: Port-Specific Fog Visibility Classification in Real-World Harbor Cameras

### 한 줄 정의

`각 항을 독립 데이터셋으로 보고, 항별로 최적의 CNN 기반 시정 분류 모델과 실패 패턴을 분석하는 논문`

### 왜 강한가

이 후보는 네 제약을 가장 정직하게 반영한다.

- 데이터를 섞지 않아도 된다
- 각 항의 난이도와 특성이 실제로 다르다는 점을 논문 메시지로 만들 수 있다
- “하나의 범용 모델” 대신 `site-specific deployment`를 전면에 세울 수 있다

핵심은 `항별로 문제가 다르다`는 사실을 보여 주는 것이다.

- 어떤 항은 배경 clutter가 큼
- 어떤 항은 역광/야간 문제가 큼
- 어떤 항은 해무와 저시정 경계가 애매함
- 어떤 항은 class imbalance가 심함

### 핵심 연구 질문

- 항만별로 같은 backbone의 성능 순위가 달라지는가
- 항별 최적의 augmentation, loss, threshold가 다른가
- 항별 실패 양상이 일관되지 않다면, 범용 모델보다 site-specific 모델이 더 자연스러운가

### 주장 구조

1. `항만 시정 분류는 site-specific 문제로 보는 편이 더 정확하다`
2. `같은 CNN이라도 항만별 성능과 오류 양상이 의미 있게 다르다`
3. `항별 최적화가 가능한 실전 배포형 모델링 프레임이 필요하다`

### 최소 실험 세트

각 항에 대해 아래 backbone을 따로 학습한다.

- ResNet-50
- EfficientNet-B3
- ConvNeXt-T
- MobileNetV3-Large

실험 구성:

- 항별 within-port train/val/test
- 항별 class distribution 분석
- 항별 confusion matrix
- 항별 failure case visualization
- 항별 optimal threshold 비교

### H200 2장 기준 실험 설계

이 주제는 `큰 모델`보다 `항별 반복실험`이 중요하다.

권장 방식:

1. 각 항별 backbone 4종 스크리닝
- 이미지 크기 224
- seed 2개

2. 항별 상위 2개 backbone만 재실험
- 224 / 384 비교
- augmentation 변경
- seed 3개

3. 결과 정리
- 항별 최고 모델
- 항별 hardest class
- 항별 calibration curve

즉 H200 2장은 각 항을 독립 실험축으로 두고, 많은 반복과 분석을 빠르게 끝내는 데 쓰는 편이 낫다.

### 예상 장점

- 가장 정직하다
- 데이터 제약과 운영 제약을 그대로 논문 메시지로 만들 수 있다
- 첫 논문으로 안정적이다

### 예상 약점

- novelty가 약해 보일 수 있다

보완 포인트:

- 단순 benchmark가 아니라 `항별 오류 구조 분석`
- class boundary ambiguity 분석
- time-of-day / season effect 분석

### 추천도

`최우선`

---

## 3. 후보 2: Cross-Port Transfer without Joint Training for Harbor Fog Classification

### 한 줄 정의

`한 항에서 학습한 CNN이 다른 항으로 얼마나 전이되는지, 그리고 소량 적응으로 얼마나 회복되는지 분석하는 전이학습 논문`

### 왜 강한가

이 후보는 `항을 섞지 않는다`는 제약을 지키면서도, 항 간 관계를 연구 질문으로 바꿀 수 있다.

즉 질문은 더 이상

- 여러 항을 같이 학습하면 잘 되나

가 아니라 아래가 된다.

- 한 항에서 배운 표현이 다른 항에도 통하는가
- 통하지 않는다면 그 차이는 무엇 때문인가
- 공동 학습 없이 소량 적응만으로 해결 가능한가

### 핵심 연구 질문

- Port A에서 학습한 모델은 Port B/C/D에서 얼마나 무너지는가
- 전이 성능은 항의 시각적 유사도와 관계가 있는가
- few-shot fine-tuning이 from-scratch 학습보다 효율적인가

### 주장 구조

1. `항만 간 전이는 가능하지만 매우 비대칭적이다`
2. `전이 실패는 단순 정확도 하락보다 calibration 붕괴와 fog miss 증가로 먼저 드러난다`
3. `공동 학습 없이도 few-shot adaptation으로 상당 부분 회복 가능하다`

### 최소 실험 세트

- source port 4개
- target port 3개씩
- 총 12개 transfer direction

baseline:

- source-only zero-shot
- target from-scratch
- source-pretrained -> target few-shot fine-tuning

few-shot regime:

- 1%
- 5%
- 10%

backbone:

- ConvNeXt-T
- EfficientNet-B3
- MobileNetV3-Large

### H200 2장 기준 실험 설계

이 주제는 실험 수가 많다.  
따라서 backbone 수를 3개 이하로 줄이는 게 좋다.

권장 방식:

1. 각 항 within-port 결과로 backbone 2~3개 선정

2. 전이 실험 실행
- 12개 방향
- zero-shot
- 1/5/10% adaptation

3. 상위 조합만 seed 3회

이 주제에서 H200 2장의 강점은 `많은 전이 방향`과 `few-shot grid`를 현실적으로 돌릴 수 있다는 점이다.

### 예상 장점

- joint training 없이도 site gap을 연구할 수 있다
- transferability라는 더 큰 질문으로 확장 가능하다
- 후속으로 domain adaptation 논문으로 이어가기 좋다

### 예상 약점

- target 데이터가 너무 적으면 분산이 커질 수 있다
- 항 수가 4개라서 통계적 일반화 주장을 과장하면 안 된다

보완 포인트:

- 질적 사례 분석
- 항 간 유사도 시각화
- feature space overlap 분석

### 추천도

`매우 높음`

---

## 4. 후보 3: Uncertainty-Aware Fog Classification for Site-Specific Harbor Deployment

### 한 줄 정의

`각 항만별 독립 모델에서 불확실성과 거부(reject) 전략을 결합해, 현장 배포 안전성을 높이는 논문`

### 왜 강한가

항만 시정 분류는 단순 classification accuracy 문제로만 보기 어렵다.

- 해무를 놓치면 운영 위험이 크다
- 경계 사례에서는 억지로 답하는 것보다 `모르겠다`고 하는 것이 낫다
- 항마다 데이터 특성이 달라 calibration도 항별로 달라질 수 있다

즉 이 주제는 `site-specific deployment safety`를 전면에 세울 수 있다.

### 핵심 연구 질문

- 항별 독립 학습 모델은 calibration 관점에서 얼마나 불안정한가
- uncertainty-aware method가 해무 미탐을 줄이는가
- abstention 정책이 실제 운영에서 의미 있는 coverage-risk tradeoff를 제공하는가

### 주장 구조

1. `항별 모델은 정확도만으로는 충분히 평가할 수 없다`
2. `같은 항 내부에서도 confidence miscalibration이 존재한다`
3. `uncertainty와 selective prediction을 붙이면 운영 안전성이 개선된다`

### 최소 실험 세트

backbone:

- ConvNeXt-T
- EfficientNet-B3

uncertainty method:

- softmax baseline
- temperature scaling
- MC dropout
- deep ensemble

평가:

- within-port calibration
- cross-port zero-shot에서의 calibration 붕괴
- risk-coverage curve
- fog recall at fixed coverage

### H200 2장 기준 실험 설계

이 주제는 deep ensemble 때문에 GPU 사용량이 늘어난다.  
하지만 모델 크기가 크지 않아서 H200 2장이면 충분하다.

권장 방식:

1. backbone 2개만 사용
2. 각 항에 대해 독립 학습
3. 각 항 모델의 calibration 평가
4. 항 간 zero-shot 적용 시 confidence 붕괴 분석
5. ensemble 3~5개 모델로 selective policy 구축

즉 H200은 여기서 `앙상블 + 반복 calibration` 실험을 가능하게 해 주는 자원이다.

### 예상 장점

- 실무성과 논문성이 동시에 있다
- 운영기관 설득이 좋다
- 후속 active learning, human review, alarm policy 설계로 이어진다

### 예상 약점

- 데이터가 너무 쉬우면 uncertainty 차이가 약하게 보일 수 있다

보완 포인트:

- hard subset 정의
- ambiguous sample 분석
- 낮/밤, 계절, 역광 등 조건별 calibration 분석

### 추천도

`높음`

---

## 5. 후보 4: Lightweight CNNs for Real-Time Harbor Fog Monitoring

### 한 줄 정의

`항별 독립 배포를 전제로, 경량 CNN이 실시간 항만 시정 모니터링에 어느 정도까지 충분한지 보여 주는 deployment-oriented 논문`

### 왜 강한가

이 후보는 학술 novelty보다 실용성이 강하다.

- 현장 서버나 edge 장치 배포를 상정하기 쉽다
- 각 항별 독립 모델 운용과 잘 맞는다
- 작은 모델을 항만별로 따로 튜닝하는 전략이 자연스럽다

### 핵심 연구 질문

- MobileNet/EfficientNet/소형 ConvNet이 항별 독립 배포에 충분한가
- 항별로 latency-accuracy 최적점이 다른가
- lightweight model에 reject option을 붙이면 운영성이 개선되는가

### 주장 구조

1. `항만 시정 분류는 항별 독립 경량 모델로도 충분히 운영 가능하다`
2. `대형 backbone보다 항별 튜닝된 경량 backbone이 더 실용적일 수 있다`
3. `선택적 예측을 결합하면 경량 모델의 위험을 줄일 수 있다`

### 최소 실험 세트

backbone:

- MobileNetV3-Large
- EfficientNet-B0/B2
- ResNet-18
- 소형 ConvNeXt 계열 1개

평가:

- accuracy
- fog recall
- latency
- model size
- memory footprint
- reject option 포함 시 risk-coverage

### H200 2장 기준 실험 설계

이 주제는 훈련보다 탐색이 중요하다.

권장 방식:

1. 항별로 작은 backbone 여러 개를 빠르게 스크리닝
2. 상위 2개에 대해
- 해상도 sweep
- threshold tuning
- optional INT8 PTQ

3. 결과를 항별 deployment table로 정리

H200 2장은 과한 자원일 수 있지만, 여러 항과 해상도 조건을 빠르게 돌릴 수 있다는 장점이 있다.

### 예상 장점

- 현장 적용성이 강하다
- 보고서/과제/사업화와 연결되기 좋다
- 논문보다 실증 보고서 성격도 동시에 가질 수 있다

### 예상 약점

- 첫 대표 논문으로는 후보 1~3보다 약하다

보완 포인트:

- 단순 경량화가 아니라 `site-specific deployment policy`
- selective prediction 결합
- zero-shot portability 비교

### 추천도

`중상`

---

## 6. 네 후보의 우선순위 비교

### 후보 1이 가장 좋은 이유

- 제약을 가장 정직하게 반영한다
- 논문 구조가 가장 안정적이다
- 첫 논문으로 실패 확률이 낮다

### 후보 2가 두 번째로 강한 이유

- “섞지 않고도” 항 간 관계를 연구할 수 있다
- 전이학습과 적응이라는 더 큰 질문으로 확장 가능하다

### 후보 3이 의미 있는 이유

- safety-critical framing이 분명하다
- 단순 정확도 논문보다 훨씬 강하다

### 후보 4가 유용한 이유

- 제품화와 실전 적용엔 매우 좋다
- 다만 대표 학술 논문으로는 약간 뒤다

## 7. 첫 논문으로 무엇을 고를까

가장 추천하는 1편은 아래다.

`Port-Specific Fog Visibility Classification in Real-World Harbor Cameras`

이 주제의 장점은 아래와 같다.

- 데이터를 섞지 않는다는 조건과 완전히 일치한다
- 각 항의 실제 난이도와 운영 조건을 분석 중심으로 보여 줄 수 있다
- 후속으로 후보 2와 후보 3을 자연스럽게 이어 갈 수 있다

가장 추천하는 2편 연속 시나리오는 아래다.

1. 첫 논문: `후보 1`
2. 후속 논문: `후보 2`

즉

- 1편에서는 `항별 독립 배포 문제`를 정의하고
- 2편에서는 `joint training 없이 가능한 전이와 적응`을 다루는 흐름이 가장 자연스럽다

---

## 8. H200 2장 기준 전체 연구 운영 전략

네 환경에서는 아래 방식이 가장 효율적이다.

### 단계 1. 항별 데이터 진단

- 각 항 샘플 수 정리
- 클래스 분포 정리
- 시간대/계절 분포 정리
- 연속 프레임 또는 near-duplicate leakage 점검

### 단계 2. 항별 baseline 확정

각 항에서 아래 backbone을 독립적으로 평가한다.

- ResNet-50
- EfficientNet-B3
- ConvNeXt-T
- MobileNetV3-Large

### 단계 3. 항별 최고 모델 결정

- 항만별 최고 backbone 선정
- 항만별 최적 threshold와 augmentation 선정

### 단계 4. 메인 주제 선택

- 후보 1이면 항별 분석 깊이 강화
- 후보 2면 transfer + few-shot grid 실행
- 후보 3이면 uncertainty + abstention 분석
- 후보 4면 deployment sweep 강화

### 단계 5. 자원 사용 원칙

이번 문제에서 H200 2장은 `foundation model 대형 학습`용이 아니다.  
오히려 아래에 쓰는 편이 낫다.

- 항별 반복실험
- seed 반복
- transfer direction 반복
- ensemble 실험
- calibration과 threshold sweep

즉 이 프로젝트에서는 `모델 크기`보다 `실험의 밀도와 정직한 프로토콜`이 훨씬 중요하다.

---

## 최종 정리

이 데이터에서 가장 약한 프레임은 `여러 항을 억지로 합쳐 범용 모델을 만드는 논문`이고, 가장 강한 프레임은 `항별 독립 배포를 전제로 site-specific learning, cross-port transfer, uncertainty, deployment를 다루는 논문`이다.

따라서 추천 순서는 아래다.

1. `Port-Specific Classification`
2. `Cross-Port Transfer without Joint Training`
3. `Uncertainty-Aware Site-Specific Deployment`
4. `Lightweight Real-Time Monitoring`

첫 논문은 `후보 1`, 두 번째 논문은 `후보 2`로 가는 흐름이 가장 안정적이다.
