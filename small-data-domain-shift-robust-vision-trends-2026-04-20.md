# 2026 Small-Data / Domain Shift / Robust Vision 트렌드 리포트

기준일: 2026-04-20

## 조사 목적

이 문서는 `데이터가 적고, 분포가 흔들리고, 실제 배포 조건이 깨끗하지 않은 환경`에서 비전 모델 연구가 어디로 가고 있는지를 정리한 리포트다.

다루는 범위는 다음과 같다.

- small-data regime
- domain shift와 OOD generalization
- domain generalization, domain adaptation, test-time adaptation
- uncertainty와 calibration
- 의료영상·산업검사·원격탐사 같은 실제 응용에서의 함의

핵심 질문은 간단하다.

- large-scale benchmark에서 강한 모델이 실제 small-data와 shift 환경에서도 강한가
- 어떤 구조와 학습 전략이 성능뿐 아니라 신뢰성까지 가져오는가

## 빠른 결론

1. `small-data와 domain shift는 여전히 비전의 핵심 미해결 문제다.`
2. `대규모 사전학습이 분명히 중요하지만, 그것만으로 robust deployment 문제가 해결되지는 않는다.`
3. `자연적 분포 이동과 synthetic corruption은 서로 다른 문제이며, 한쪽에서의 개선이 다른 쪽으로 자동 전이되지 않는다.`
4. `CNN/ViT 비교는 이제 단순 정확도 비교가 아니라, sample efficiency, calibration, adaptation stability, compute budget까지 포함해 봐야 한다.`
5. `현실적인 연구는 foundation model + domain-specific adaptation + uncertainty estimation + realistic evaluation을 함께 다뤄야 한다.`

---

## 1. 왜 이 주제가 계속 중요한가

대부분의 실제 비전 문제는 ImageNet식 clean IID 환경이 아니다.

- 라벨이 적다
- 클래스가 불균형하다
- 장비, 병원, 카메라, 조명, 배경, 계절, 지리적 위치가 바뀐다
- test 시점에 본 적 없는 nuisance가 들어온다
- confidence score가 맞지 않는다

즉 실제 문제는 대개 `정확도 최대화`보다 `배포 후 성능 붕괴를 얼마나 늦추는가`가 더 중요하다.

WILDS는 바로 이 지점을 공식적으로 드러낸 대표 benchmark다. 이 벤치마크는 병원 간 이동, 카메라 트랩 위치 변화, 위성 영상의 시간/공간 변화 등 현실에서 자연스럽게 생기는 shift를 모아 놓았다.[1]

## 2. 핵심 구분 1: synthetic robustness와 natural distribution shift는 다르다

`Benchmarking Neural Network Robustness to Common Corruptions and Perturbations`는 ImageNet-C를 통해 noise, blur, weather, digital corruption 등 synthetic corruption robustness를 표준화했다.[2]

이 benchmark는 지금도 매우 중요하다.

- 재현 가능하다
- corruption type별 failure mode 분석이 쉽다
- architecture와 augmentation 효과를 빠르게 비교할 수 있다

하지만 `Measuring Robustness to Natural Distribution Shifts in Image Classification`는 다른 중요한 사실을 보여 줬다. 여러 robustness intervention이 synthetic shift에서는 좋아 보여도 natural shift에서는 같은 식으로 이득을 주지 않는 경우가 많았고, 특히 `추가적이고 더 다양한 데이터`가 여전히 가장 강한 축이었다.[3]

즉 지금 시점의 해석은 다음과 같아야 한다.

- ImageNet-C 개선은 유용하다
- 하지만 그것만으로 real-world shift robustness를 주장하면 위험하다
- synthetic shift와 natural shift는 분리해서 평가해야 한다

## 3. 핵심 구분 2: small-data 문제와 shift 문제는 겹치지만 같지 않다

small-data 환경에서 모델이 약해지는 이유는 여러 가지다.

- 추정 분산 증가
- representation overfitting
- shortcut feature 의존
- class imbalance
- label noise 민감도

domain shift는 여기에 추가로 아래 문제가 붙는다.

- train/test support mismatch
- acquisition protocol mismatch
- nuisance factor change
- spurious correlation 붕괴

즉 `데이터가 적다`와 `분포가 바뀐다`는 별개의 축이다. 실제 응용에서는 둘이 동시에 온다.

특히 의료영상, 산업검사, 현장 로봇 비전은 아래 형태가 흔하다.

- source는 한 기관
- 라벨은 소수
- target은 장비나 센서가 다른 기관
- target label은 없음

그래서 실전 연구는 대체로 아래 네 문제를 동시에 만난다.

- low-data learning
- domain generalization
- domain adaptation
- uncertainty under shift

## 4. architecture 관점: CNN, ViT, hybrid를 어떻게 봐야 하나

### 4-1. small-data에서는 구조적 bias가 아직 중요하다

CNN은 locality, weight sharing, multi-scale prior를 강하게 제공한다. 이 bias는 small-data에서 구조적 regularizer처럼 작동한다.

ViT는 더 유연하지만 대체로 더 많은 data support, stronger pretraining, stronger recipe tuning에 기대는 경우가 많다. `When Vision Transformers Outperform ResNets without Pre-training or Strong Data Augmentations`는 scratch regime에서도 ViT가 좋아질 수 있음을 보였지만, 그 과정은 regularization과 optimization에 매우 민감했다.[4]

### 4-2. hybrid 접근이 자주 유리한 이유

의료영상 같은 영역에서는 CNN과 Transformer를 결합한 semi-supervised segmentation 연구가 꾸준히 나온다. `Semi-Supervised Medical Image Segmentation via Cross Teaching between CNN and Transformer`는 두 구조가 서로 다른 표현 편향을 제공하며, 그것을 교차 지도 신호로 활용했다.[5]

이런 결과는 다음을 시사한다.

- CNN과 ViT는 같은 데이터를 다른 방식으로 요약한다
- 두 표현의 오류 상관이 완전히 같지 않을 수 있다
- small-data에서는 단일 거대 backbone보다 상호 보완성이 더 가치 있을 수 있다

### 4-3. foundation model은 도움이 되지만 공짜는 아니다

대규모 사전학습 representation은 분명히 low-data 문제를 많이 완화한다. 의료영상에서도 self-supervised 또는 generic pretraining이 transferability를 개선한다는 보고가 계속 나왔다.[6][7][8]

하지만 여기에는 함정이 있다.

- source pretraining 데이터와 target 도메인의 차이가 크면 이득이 불안정할 수 있다
- linear probe에서 좋던 표현이 fine-tuning 후 calibration까지 좋다고 보장되지 않는다
- 특정 도메인에서는 modality-specific pretraining이 generic natural-image pretraining보다 더 유리할 수 있다

## 5. domain generalization: 아직도 기대보다 어렵다

`In Search of Lost Domain Generalization`은 많은 DG 알고리즘이 강한 ERM baseline을 안정적으로 이기지 못한다는 점을 보여 준 대표적인 논문이다.[9]

이 교훈은 지금도 유효하다.

- 비교가 불공정하면 DG 알고리즘이 과대평가되기 쉽다
- model selection과 hyperparameter protocol이 결과를 크게 흔든다
- 일부 이득은 알고리즘 자체가 아니라 tuning과 reporting에서 올 수 있다

## 6. domain adaptation과 test-time adaptation: 유용하지만 brittle하다

라벨 없는 target 데이터가 일부라도 있으면 adaptation은 여전히 강한 옵션이다. 그중 가장 상징적인 예가 Tent다. Tent는 source 데이터를 다시 쓰지 않고 test 시점에 entropy minimization으로 모델을 적응시키는 fully test-time adaptation setting을 제시했다.[10]

하지만 실제 적용에는 한계가 뚜렷하다.

- online stream 가정에 민감하다
- class imbalance나 class appearance/disappearance에 취약할 수 있다
- 잘못 적응하면 catastrophic drift가 발생할 수 있다
- calibration을 꼭 개선하는 것은 아니다

즉 adaptation은 만능 복구기가 아니라 `프로토콜이 매우 중요한 동적 추론 문제`다.

## 7. uncertainty와 calibration은 부가 기능이 아니라 핵심 기능이다

배포 환경에서 accuracy만큼 중요한 것이 uncertainty quality다. `Can You Trust Your Model's Uncertainty? Evaluating Predictive Uncertainty under Dataset Shift`는 shift 상황에서 uncertainty estimation이 얼마나 어려운지, 그리고 단순 post-hoc calibration이 충분하지 않은 경우가 많음을 보여 줬다.[11]

최근에도 `Beyond Deep Ensembles`는 WILDS 기반 대규모 평가에서 distribution shift 하의 generalization과 calibration을 함께 점검했다.[12]

따라서 robust vision 연구는 아래를 함께 보고 가야 한다.

- accuracy
- calibration
- OOD detection
- abstention utility

## 8. benchmark 관점: 어떤 평가 세트가 중요한가

이 주제를 깊게 보려면 벤치마크를 역할별로 분리해야 한다.

### 8-1. synthetic corruption

- ImageNet-C[2]

장점은 통제 가능성과 빠른 반복 실험이다. 단점은 natural shift 대용으로 과잉 해석되기 쉽다는 점이다.

### 8-2. natural distribution shift

- ImageNet-V2, ImageNet-A, consistency shift 계열[3]
- ObjectNet[13]
- WILDS[1]

ObjectNet은 pose, viewpoint, background, rotation 등에 대한 모델 약점을 드러낸 대표적 bias-controlled dataset이다.[13]

### 8-3. domain-specific shift benchmark

- 병원 간 의료 데이터
- 현미경/병리 슬라이드 스캐너 차이
- 산업 라인 카메라 차이
- 위성 센서/지역/시기 차이

이런 벤치마크가 정말 중요한 이유는 `실제 논문 주장의 external validity`를 여기서 확인할 수 있기 때문이다.

## 9. 응용 분야별 해석

### 9-1. 의료영상

의료는 대표적인 small-data + shift 환경이다.

- 기관이 적다
- 장비가 다르다
- annotation cost가 높다
- distribution shift가 임상적으로 치명적이다

여기서 self-supervised pretraining과 transfer는 분명 도움이 된다.[6][7][8] 하지만 modality gap, acquisition protocol gap, annotation sparsity 때문에 여전히 domain-specific adaptation과 calibration 연구가 중요하다.

### 9-2. 산업검사

산업검사는 background가 단조롭지만 defect는 희귀하고 shift는 자주 생긴다.

- 조명 변화
- 카메라 교체
- 제품 revision
- rare anomaly

이 영역에서는 표준 large-scale pretraining보다 `작은 데이터에서의 안정성`과 `false alarm control`이 훨씬 중요하다.

### 9-3. 원격탐사와 현장 로봇 비전

계절, 날씨, 고도, 센서, 시간에 따른 shift가 매우 크다. 여기서는 domain generalization과 test-time adaptation이 특히 중요하고, 멀티스케일 구조와 uncertainty-aware prediction의 값이 크다.

## 10. 지금 연구자가 바로 쓸 수 있는 질문

### 10-1. representation question

generic foundation model의 표현이 small-data 도메인에서 실제로 무엇을 전달하는가.

- texture prior
- shape prior
- semantic grouping
- calibration prior

### 10-2. adaptation question

adaptation이 accuracy를 올릴 때 calibration도 좋아지는가, 아니면 오히려 confidence만 왜곡하는가.

### 10-3. data question

추가 데이터의 양보다 `데이터 다양성`이 더 중요한가. 자연적 shift robustness에서는 이 질문이 매우 중요하다.[3]

### 10-4. architecture question

small-data + shift 환경에서 아래 중 무엇이 가장 안정적인가.

- CNN fine-tuning
- ViT fine-tuning
- hybrid
- frozen backbone + lightweight adapter
- prompt / LoRA / linear probe

## 11. 실험 설계 제안

이 주제를 연구 프로젝트로 밀어붙일 때는 아래 축이 좋다.

- regime split: 1%, 5%, 10%, full label, source-only, source + unlabeled target, streaming target
- evaluation split: in-domain accuracy, synthetic corruption, natural shift, calibration, OOD detection, rejection curve
- model split: ResNet / ConvNeXt, ViT / Swin, CNN-Transformer hybrid, SSL pretrained vs supervised pretrained
- protocol split: frozen backbone, full fine-tune, parameter-efficient tuning, test-time adaptation on/off

핵심은 `한 종류의 shift`만 보고 결론 내리지 않는 것이다.

## 12. 연구 방향 요약

이 분야는 앞으로 아래 방향으로 더 중요해질 가능성이 크다.

- large foundation model을 가져와도 realistic robustness gap을 줄이지 못하는 문제
- adaptation이 성능은 올리지만 신뢰도는 무너뜨리는 문제
- small-data 도메인에서 어떤 bias가 실제로 도움이 되는지 분리하는 문제
- uncertainty를 성능 지표가 아니라 운영 지표로 다루는 문제

즉 2026년의 robust vision 연구는 `더 큰 모델`의 문제가 아니라 `더 나은 evaluation protocol과 더 현실적인 deployment objective`의 문제에 가깝다.

---

## 최종 정리

small-data와 domain shift는 주변부 문제가 아니다. 오히려 실제 비전 시스템이 연구실 밖으로 나갈 때 가장 먼저 맞닥뜨리는 문제다.

따라서 좋은 연구 질문은 더 이상

- benchmark top-1을 얼마나 올릴까

가 아니라 아래가 되어야 한다.

- 데이터가 적고 환경이 바뀔 때 무엇이 남는가
- 모델이 모른다는 사실을 얼마나 잘 알 수 있는가
- adaptation이 실제 배포에서 얼마나 안전한가

---

## 참고문헌

[1] Koh et al., *WILDS: A Benchmark of in-the-Wild Distribution Shifts* (ICML 2021)  
[PMLR](https://proceedings.mlr.press/v139/koh21a.html)

[2] Hendrycks and Dietterich, *Benchmarking Neural Network Robustness to Common Corruptions and Perturbations* (ICLR 2019)  
[OpenReview](https://openreview.net/forum?id=HJz6tiCqYm)

[3] Taori et al., *Measuring Robustness to Natural Distribution Shifts in Image Classification* (NeurIPS 2020)  
[NeurIPS PDF](https://proceedings.neurips.cc/paper/2020/file/d8330f857a17c53d217014ee776bfd50-Paper.pdf)

[4] Chen et al., *When Vision Transformers Outperform ResNets without Pre-training or Strong Data Augmentations* (ICLR 2022)  
[OpenReview PDF](https://openreview.net/pdf/97b8505eb7034c4bfaee9c7d480a9f605be6fea8.pdf)

[5] Luo et al., *Semi-Supervised Medical Image Segmentation via Cross Teaching between CNN and Transformer* (MIDL 2022)  
[PMLR](https://proceedings.mlr.press/v172/luo22b.html)

[6] Truong et al., *How Transferable are Self-supervised Features in Medical Image Classification Tasks?* (MLHC 2021)  
[PMLR](https://proceedings.mlr.press/v158/truong21a.html)

[7] Sowrirajan et al., *MoCo Pretraining Improves Representation and Transferability of Chest X-ray Models* (MIDL 2021)  
[PMLR](https://proceedings.mlr.press/v143/sowrirajan21a.html)

[8] Jiang and Veeraraghavan, *Self-supervised pretraining in the wild imparts image acquisition robustness to medical image transformers* (MIDL 2024)  
[PMLR](https://proceedings.mlr.press/v250/jiang24b.html)

[9] Gulrajani and Lopez-Paz, *In Search of Lost Domain Generalization* (ICLR 2021)  
[OpenReview PDF](https://openreview.net/pdf?id=lQdXeXDoWtI)

[10] Wang et al., *Tent: Fully Test-Time Adaptation by Entropy Minimization* (ICLR 2021)  
[OpenReview](https://openreview.net/pdf?id=uXl3bZLkr3c)

[11] Ovadia et al., *Can You Trust Your Model's Uncertainty? Evaluating Predictive Uncertainty under Dataset Shift* (NeurIPS 2019)  
[NeurIPS](https://proceedings.neurips.cc/paper/2019/hash/8558cb408c1d76621371888657d2eb1d-Abstract.html)

[12] Mukhoti et al., *Beyond Deep Ensembles: A Large-Scale Evaluation of Bayesian Deep Learning under Distribution Shift* (NeurIPS 2023)  
[NeurIPS PDF](https://proceedings.neurips.cc/paper_files/paper/2023/file/5d97b7e62022c859347397f6c1e8d0f9-Paper-Conference.pdf)

[13] Barbu et al., *ObjectNet: A large-scale bias-controlled dataset for pushing the limits of object recognition models* (NeurIPS 2019)  
[OpenReview](https://openreview.net/forum?id=SkgnRNHgIS)
