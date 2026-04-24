# 2026 항만 해무 시정 데이터셋 실험 프로토콜 기술서

기준일: 2026-04-23

## 목적

이 문서는 현재 보유한 `항만 해무 이미지 데이터셋`을 이용해 논문 및 연구 실험을 수행할 때, `데이터 split`, `평가 규칙`, `누수 방지`, `기본 baseline`, `H200 GPU 2장 기준 실험 운영 방식`을 명확히 고정하기 위한 기술서다.

이 문서의 가장 중요한 전제는 아래 한 줄이다.

`각 항만의 데이터는 학습 단계에서 서로 섞지 않는다.`

즉 이 문서는 아래 질문에 답하기 위해 작성한다.

- 항별 독립 모델을 어떻게 공정하게 학습하고 평가할 것인가
- 다른 항으로의 전이는 어떤 방식으로만 측정할 것인가
- 같은 날짜, 비슷한 프레임, 연속 장면 누수는 어떻게 막을 것인가
- H200 GPU 2장 환경에서 실험 우선순위를 어떻게 잡을 것인가

## 빠른 결론

핵심 규칙은 5개다.

1. `항별 독립 학습`을 기본으로 한다.
2. `시간 기반 split`을 우선하며, 랜덤 split은 보조 실험으로만 사용한다.
3. `연속 프레임/유사 프레임 누수`를 반드시 막는다.
4. `정확도`보다 `Macro-F1`, `Fog Recall`, `Calibration`을 더 중시한다.
5. H200 2장은 `큰 모델`보다 `반복 실험, seed 반복, threshold sweep, calibration 분석`에 쓰는 편이 낫다.

---

## 1. 데이터셋 정의

### 1-1. 기본 태스크

태스크는 항만 CCTV 또는 준고정 시점 이미지에 대해 아래 3개 클래스를 분류하는 것이다.

- `보통시정`
- `저시정`
- `해무`

이 태스크는 단순 이미지 분류가 아니라, 실제 항만 운영상 의미 있는 `저가시성 감지` 문제로 본다.

### 1-2. 기본 단위

실험 단위는 아래처럼 정의하는 것이 좋다.

- `site`: 항만 ID
- `session`: 동일 날짜/시간대/기상 이벤트에 속하는 이미지 묶음
- `frame`: 개별 이미지

가능하면 각 이미지에 아래 메타정보를 붙인다.

- 항만 ID
- 촬영 시각
- 날짜
- 낮/밤
- 월/계절
- 카메라 ID
- 이벤트 ID 또는 session ID

메타정보가 부족해도 최소한 아래 둘은 확보해야 한다.

- 항만 ID
- 촬영 시각 또는 파일 생성 시각

## 2. 가장 중요한 원칙: 항만별 독립 학습

### 2-1. 허용되는 학습 방식

- Port A 모델은 Port A 데이터로만 학습
- Port B 모델은 Port B 데이터로만 학습
- Port C 모델은 Port C 데이터로만 학습
- Port D 모델은 Port D 데이터로만 학습

즉 `통합 train set`은 없다.

### 2-2. 허용되는 비교 방식

- 항별 모델 성능 비교
- Port A 모델을 Port B test에 적용하는 zero-shot transfer
- Port A 모델을 Port B의 소량 데이터로 미세조정하는 few-shot adaptation
- 항별 calibration, confusion, threshold 차이 비교

### 2-3. 금지하는 방식

- 4개 항 데이터를 합쳐 학습
- multi-port joint pretraining을 메인 실험으로 제시
- “전 항 통합 범용 모델”을 대표 결과로 제시

이 규칙을 어기면 지금 설정한 연구 프레임과 충돌한다.

## 3. 데이터 split 규칙

### 3-1. 기본 원칙

가장 먼저 막아야 할 것은 `장면 누수`다.

항만 CCTV 이미지는 보통 아래 문제가 있다.

- 같은 날, 같은 시간대의 연속 프레임이 매우 비슷함
- 1분 간격 또는 초 단위 저장 이미지가 거의 중복됨
- 같은 해무 이벤트에서 train/test가 동시에 잡히면 과대평가됨

따라서 random image-level split은 기본 평가로 쓰면 안 된다.

### 3-2. 권장 split 순서

각 항에 대해 아래 순서로 split을 만든다.

1. `session 단위 그룹화`
2. `시간 순서 기반 train/val/test 분리`
3. `클래스 분포 확인`
4. `유사 프레임 누수 점검`

### 3-3. 권장 비율

각 항 내부에서 아래 비율을 기본값으로 권장한다.

- train: 70%
- val: 15%
- test: 15%

단, 이 비율보다 중요한 것은 `시간 분리`와 `session 분리`다.

예:

- 1~8월: train
- 9~10월: val
- 11~12월: test

또는

- 초기 기간: train
- 중간 기간: val
- 후기 기간: test

### 3-4. 유사 프레임 제거 규칙

같은 세션 내 이미지가 과도하게 비슷하면 아래 중 하나를 적용한다.

- 일정 시간 간격 샘플링
- perceptual hash 기준 near-duplicate 제거
- 연속 프레임 중 대표 프레임만 남기기

최소한 아래는 해야 한다.

- test 세트와 train 세트 간 near-duplicate 제거
- val 세트와 train 세트 간 near-duplicate 제거

## 4. 핵심 평가 프로토콜

### 4-1. Protocol A: Within-Port Independent Evaluation

가장 기본이 되는 프로토콜이다.

- 각 항만별로 독립 train/val/test split 구성
- 같은 항 데이터만 사용해 학습
- 같은 항 test로 평가

이 프로토콜이 답하는 질문:

- 이 항에서 분류 문제가 얼마나 어려운가
- 어떤 backbone이 이 항에 잘 맞는가
- 어떤 클래스가 특히 어려운가

### 4-2. Protocol B: Cross-Port Zero-Shot Transfer

이 프로토콜은 학습은 섞지 않되, 전이 성능을 측정한다.

예:

- Port A로 학습한 모델 -> Port B test
- Port A로 학습한 모델 -> Port C test

이때 target port의 학습 데이터는 사용하지 않는다.

이 프로토콜이 답하는 질문:

- 항만 간 시각적 간극이 얼마나 큰가
- 항별 모델이 다른 항으로는 얼마나 쉽게 무너지는가
- 어떤 항이 source로 더 transferable한가

### 4-3. Protocol C: Cross-Port Few-Shot Adaptation

이 프로토콜은 joint training 없이 소량 적응만 허용한다.

예:

- Port A pretrained model
- Port B 1% / 5% / 10% labeled data로 fine-tuning
- Port B test로 평가

이 프로토콜이 답하는 질문:

- 한 항의 표현이 다른 항으로 어느 정도 재사용 가능한가
- 적은 라벨로 회복 가능한가
- from-scratch보다 실제로 유리한가

## 5. 평가 지표

### 5-1. 기본 분류 지표

- `Accuracy`
- `Balanced Accuracy`
- `Macro-F1`
- `Per-class Precision`
- `Per-class Recall`

이 중 기본 대표 지표는 아래를 권장한다.

- `Macro-F1`
- `Balanced Accuracy`

이유는 클래스 불균형 가능성이 높기 때문이다.

### 5-2. 운영 중심 지표

이 문제에서는 아래 지표가 더 중요하다.

- `Fog Recall`
- `Low-Visibility Recall`
- `Fog + Low-Visibility Combined Recall`

실전에서는 해무와 저시정을 놓치는 것이 더 치명적일 수 있다.

### 5-3. 신뢰도 지표

- `ECE`
- `Brier Score`
- `NLL`
- `Reliability Diagram`

이 연구에서는 정확도만큼 calibration이 중요하다.

### 5-4. 선택적 예측 지표

모델이 애매하면 답을 보류하는 정책을 볼 경우 아래를 추가한다.

- `Risk-Coverage Curve`
- `AUROC for Reject Decision`
- `Fog Recall at Fixed Coverage`

## 6. 기본 baseline 설계

### 6-1. 1차 baseline 세트

아래 4개면 충분하다.

- `ResNet-50`
- `EfficientNet-B3`
- `ConvNeXt-T`
- `MobileNetV3-Large`

이 구성의 장점:

- 고전 CNN, 현대화 ConvNet, 경량 모델을 모두 포함
- 실험 난도가 적당함
- H200 2장에서 반복실험이 쉬움

### 6-2. 선택적 비교 모델

필요하면 아래 중 1개만 추가한다.

- `Swin-T`

하지만 이 연구 프레임에서는 Transformer 비교가 핵심은 아니다.  
주요 메시지는 `항별 독립 문제`, `전이`, `불확실성`, `운영성`이다.

## 7. 학습 설정 기본값

### 7-1. 입력 해상도

기본:

- 224

선택 비교:

- 384

해상도는 2차 실험에서만 확대한다.

### 7-2. optimizer

권장 기본값:

- ConvNeXt, EfficientNet: `AdamW`
- ResNet, MobileNet: `SGD` 또는 `AdamW`

다만 backbone별 공정 비교를 위해 1차 실험은 가능한 한 단순화한다.

### 7-3. augmentation

기본 augmentation:

- random resized crop
- horizontal flip
- color jitter 약하게

선택:

- mixup
- cutmix
- random erasing

주의:

- 과한 augmentation은 실제 안개/해무 cue를 훼손할 수 있다
- color/contrast augmentation은 너무 세게 넣지 않는 편이 좋다

### 7-4. imbalance 대응

아래를 기본 후보로 둔다.

- class-balanced sampler
- weighted cross entropy
- focal loss

## 8. H200 GPU 2장 기준 실험 운영 전략

이번 프로젝트에서 H200 2장은 `과한 초대형 모델`보다 `풍부한 반복실험`에 쓰는 편이 더 낫다.

### 8-1. 1단계: 항별 baseline 스크리닝

각 항에 대해:

- backbone 4종
- 해상도 224
- seed 2개

목표:

- 항별 상위 모델 2개 선정

### 8-2. 2단계: 항별 최적화

각 항의 상위 2개 모델에 대해:

- augmentation 비교
- imbalance 대응 비교
- threshold tuning
- calibration 수행

### 8-3. 3단계: 전이 실험

후보 주제가 transfer라면:

- source-target 조합 전수 조사
- zero-shot
- 1% / 5% / 10% few-shot adaptation

### 8-4. 4단계: uncertainty 실험

후보 주제가 uncertainty라면:

- temperature scaling
- MC dropout
- deep ensemble 3~5개

즉 H200 2장은 아래에 쓰는 편이 맞다.

- fold 반복
- seed 반복
- source-target 반복
- threshold sweep
- calibration 반복

## 9. 논문용 표와 그림 구성

### 표 1. 데이터셋 요약

- 항만별 이미지 수
- 클래스 분포
- 기간
- 주야간 비율

### 표 2. Within-port baseline 성능

- 항만별
- backbone별
- Macro-F1 / Balanced Accuracy / Fog Recall

### 표 3. Cross-port zero-shot transfer

- source port x target port matrix

### 표 4. Few-shot adaptation

- 1% / 5% / 10%
- from-scratch vs transfer

### 표 5. Calibration / Selective prediction

- ECE
- Brier Score
- risk at fixed coverage

### 그림 1. 항별 confusion matrix

### 그림 2. reliability diagram

### 그림 3. feature embedding by port

### 그림 4. failure case gallery

## 10. 반드시 점검해야 할 체크리스트

실험 전에 아래를 반드시 확인한다.

- train/val/test가 시간 기준으로 분리됐는가
- 같은 이벤트가 train/test에 동시에 들어가지 않았는가
- near-duplicate가 제거됐는가
- 항별 데이터가 학습 단계에서 섞이지 않았는가
- class imbalance가 과도하지 않은가
- fog recall과 calibration을 함께 보고 있는가

## 11. 추천 실행 순서

가장 현실적인 순서는 아래다.

1. 항별 데이터 메타정보 정리
2. session 단위 split 생성
3. near-duplicate 제거
4. 항별 baseline 4종 학습
5. 항별 best model 선정
6. 후보 주제에 따라 transfer 또는 uncertainty 실험 확장

---

## 최종 정리

이 데이터셋의 올바른 실험 프레임은 `모든 항을 합쳐 범용 모델을 만드는 것`이 아니라, `항별 독립 모델을 정직하게 평가하고, 항 간 전이 가능성과 운영상 안전성을 추가로 분석하는 것`이다.

따라서 본 기술서의 핵심은 아래다.

- 항만별 데이터는 섞지 않는다
- 시간 기반 split으로 누수를 막는다
- Macro-F1, Fog Recall, Calibration을 핵심 지표로 둔다
- H200 2장은 대형 모델보다 반복 실험과 분석 밀도에 투자한다

이 원칙을 고정해 두면 이후 논문 주제 후보 1~4 모두를 일관된 프로토콜 위에서 비교할 수 있다.
