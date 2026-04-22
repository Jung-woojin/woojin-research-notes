# 2026 Data-Centric Vision and Synthetic Data 트렌드 리포트

기준일: 2026-04-22

## 조사 목적

이 문서는 `모델 아키텍처 자체보다 데이터 파이프라인이 성능과 배포성을 더 크게 좌우하는 시점`에서, 컴퓨터비전 연구와 실무가 데이터 중심으로 어떻게 이동하고 있는지를 정리한 리포트다.

다루는 범위는 다음과 같다.

- data curation, filtering, reweighting
- noisy label, long-tail, class imbalance
- synthetic data와 simulation data
- synthetic pretraining, dataset distillation, subset selection
- 산업검사·의료영상·희귀 이벤트 탐지에서의 실전 함의

핵심 질문은 아래다.

- 2026년 기준으로 비전 성능 향상의 핵심 병목은 모델보다 데이터에 더 가까운가
- synthetic data는 어디까지 실제 데이터를 대체할 수 있고, 어디서부터 한계가 분명해지는가
- CNN 기반 CV 엔지니어가 실전에 가져가야 할 data-centric 사고방식은 무엇인가

## 빠른 결론

1. `지금 비전 성능 차이의 상당 부분은 아키텍처보다 데이터 구성, 품질 관리, 학습 샘플링 전략에서 난다.`
2. `synthetic data는 특히 희귀 클래스, 위험 상황, 라벨 비용이 큰 태스크, 산업용 결함 검출에서 강력하지만, 자동으로 natural robustness를 주지는 않는다.`
3. `좋은 synthetic pipeline은 “이미지 생성”만이 아니라 scene design, label fidelity, coverage control, domain gap 측정, real-data mixing까지 포함한다.`
4. `dataset distillation과 subset selection은 단순 압축이 아니라, 앞으로 low-cost retraining과 edge deployment를 위한 핵심 기술 축이 될 가능성이 크다.`
5. `실전에서는 synthetic-only보다 real + synthetic + filtering + uncertainty-aware evaluation 조합이 훨씬 현실적이다.`

---

## 1. 왜 지금 data-centric vision을 따로 봐야 하나

한동안 비전 연구의 중심은 backbone과 head architecture였다.

- ResNet에서 EfficientNet으로
- EfficientNet에서 ViT/Swin으로
- detector는 Faster R-CNN에서 DETR 계열로
- segmentation은 U-Net 계열에서 foundation segmentation으로

하지만 실제 현장에서는 아래 문제가 더 자주 성능을 결정한다.

- 라벨 오류가 얼마나 많은가
- rare case가 학습셋에 들어 있는가
- 클래스 비율이 배포 환경과 비슷한가
- 테스트 환경의 조명, 배경, 장비, 각도, 속도가 훈련셋에 반영되어 있는가
- hard negative가 충분한가

즉 실전의 병목은 종종 `모델 표현력 부족`이 아니라 `학습 데이터가 문제 구조를 제대로 덮지 못하는 것`이다.

이 흐름은 특히 아래 영역에서 강하다.

- 산업검사
- 의료영상
- 원격탐사
- 자율주행/로보틱스
- 보안/감시

이 분야들은 공통적으로 `라벨 비싸고, 실패 비용 크고, 희귀 사례 중요하고, 분포 이동이 잦다`는 특징을 갖는다.

## 2. data-centric vision은 무엇을 뜻하나

data-centric vision은 단순히 “데이터 더 많이 모으자”가 아니다.  
더 정확한 의미는 `모델을 고정하거나 약하게 바꾸더라도 데이터 파이프라인을 설계해 성능을 끌어올리는 접근`이다.

핵심 구성은 아래처럼 보는 편이 실용적이다.

### 2-1. 수집과 커버리지 설계

- 어떤 조건을 반드시 포함할지 정의
- rare case와 failure case를 의도적으로 모으기
- 클래스별 수량보다 nuisance factor coverage를 관리

### 2-2. 정제와 품질 관리

- 오라클 재검수
- noisy label 탐지
- near-duplicate 제거
- 잘못된 bounding box / mask 수정

### 2-3. 학습 샘플링과 재가중치

- long-tail 보정
- hard example mining
- domain-balanced sampling
- curriculum 또는 confidence-aware weighting

### 2-4. synthetic augmentation과 synthetic generation

- classical augmentation
- simulator 기반 생성
- generative model 기반 생성
- rare case 보강용 scenario synthesis

### 2-5. 데이터 축소와 압축

- coreset selection
- dataset distillation
- subset selection

결국 data-centric vision은 모델 바깥의 전처리 작업이 아니라, `모델과 같은 급의 연구 대상`이 되고 있다.

## 3. synthetic data를 한 덩어리로 보면 자주 틀린다

synthetic data는 종류가 매우 다르다. 이걸 구분하지 않으면 기대치가 과장되기 쉽다.

### 3-1. simulator / rendering 기반 synthetic data

이 방식은 장면과 라벨을 정밀하게 제어할 수 있다.

- 카메라 위치
- 광원
- 재질
- 객체 배치
- 배경 분포
- 정답 마스크 / depth / pose

이 계열의 장점은 `label fidelity`와 `coverage control`이다.  
예를 들어 `BEHAVIOR Vision Suite`는 시뮬레이션 기반으로 시각 데이터 생성과 체계적 평가를 연결하려는 방향을 보여 준다.[1]

이런 방식은 특히 아래에 강하다.

- 로봇 조작
- embodied perception
- multi-view geometry
- 산업 부품 검사

### 3-2. generative model 기반 synthetic data

확산모델이나 생성모델을 이용한 synthetic data는 실제 이미지를 대량으로 대체하거나 보강하려는 방향이다.

장점은 분명하다.

- 클래스별 생산 속도가 빠르다
- 비용이 낮다
- 희귀 상황을 빠르게 늘릴 수 있다
- text prompt나 scene prompt로 데이터 다양성을 통제할 수 있다

하지만 약점도 분명하다.

- 텍스처와 배경 shortcut이 비현실적으로 고정될 수 있다
- 물리적 일관성이 깨질 수 있다
- class semantics는 맞지만 localization label은 부정확할 수 있다
- 생성 편향이 robustness를 악화시킬 수 있다

`SynCLR`은 synthetic image만으로도 경쟁력 있는 representation learning이 가능함을 보여 준 대표 사례다.[2]  
다만 이 결과를 곧바로 `실전 downstream도 synthetic-only로 충분하다`로 해석하면 과하다.

### 3-3. task-specific synthetic pretraining

일부 영역에서는 synthetic data가 “최종 훈련셋”보다 `pretraining source`로 특히 강하다.

`Label-Free Synthetic Pretraining of Object Detectors`는 detection에서 synthetic data를 사전학습 재료로 활용하는 방향을 보여 줬다.[3]

이 접근이 실전적인 이유는 다음과 같다.

- synthetic 쪽에서는 값싼 coverage 확보
- real 쪽에서는 소량 고품질 fine-tuning
- 둘을 섞어 real annotation 비용을 줄임

즉 synthetic data의 가장 현실적인 위치는 종종 `완전 대체재`가 아니라 `representation warm-start`다.

## 4. synthetic data가 실제로 강한 곳

synthetic data는 모든 영역에서 같은 힘을 내지 않는다.  
특히 아래 조건에서 효과가 크다.

### 4-1. rare event와 safety-critical case

실제 데이터로는 거의 못 모으는 케이스다.

- 사고 직전 장면
- 특정 결함 패턴
- 극단적 기상 조건
- 드문 질환 소견

이런 조건에서는 “평균적인 자연 이미지 추가 수집”보다 `의도적으로 만든 synthetic rare case`가 더 큰 가치가 있다.

### 4-2. 산업검사와 결함 탐지

산업검사는 data-centric 접근과 synthetic data가 특히 잘 맞는다.

- 정상 데이터는 많다
- 결함 데이터는 적다
- 라벨이 비싸다
- 카메라, 조명, 재질이 통제 가능하다

`Synthetic Data for Defect Segmentation on Complex Metal Surfaces`는 실제 산업 부품 검사에서 synthetic pretraining이 유효함을 보여 준다.[4]

이 영역에서 중요한 것은 단순 다양성보다 아래다.

- 반사광 모델링
- 재질 일관성
- 결함 morphology realism
- multi-view coverage

### 4-3. representation learning

synthetic data는 최종 supervised training보다 representation learning에서 더 넓게 먹히는 경우가 있다.

- pretraining
- contrastive learning
- masked modeling
- synthetic caption 기반 alignment

이 경우 핵심은 개별 픽셀 realism보다 `semantic coverage와 transformation diversity`다.

## 5. 그러나 synthetic data는 robustness의 공짜 답이 아니다

가장 흔한 오해는 아래다.

- synthetic 데이터가 많으면 distribution shift도 해결된다
- synthetic pretraining이 강하면 calibration도 좋아진다
- 렌더링 realism만 높이면 실전 배포가 해결된다

이 셋은 대체로 성립하지 않는다.

`Is Synthetic Data all We Need? Benchmarking the Robustness of Models Trained with Synthetic Images`는 synthetic-only 학습의 robustness를 별도로 따져야 한다는 점을 보여 준다.[5]

핵심 함의는 다음과 같다.

- clean accuracy와 robust accuracy는 분리해서 봐야 한다
- synthetic coverage가 넓어 보여도 nuisance factor 분포가 실제와 다를 수 있다
- 생성 파이프라인의 bias가 그대로 학습 bias가 된다

특히 CNN 기반 비전 시스템에서는 아래 failure mode를 자주 본다.

- 배경과 클래스가 잘못 결합됨
- 조명/반사 패턴이 과도하게 규칙적임
- sensor noise가 실제와 다름
- object boundary나 defect morphology가 비현실적임

즉 synthetic data는 “데이터를 싸게 만드는 기술”이 아니라 `분포를 설계하는 기술`로 봐야 한다.

## 6. synthetic data보다 더 자주 문제를 일으키는 것: noisy label과 long-tail

실전 CV 엔지니어링에서 synthetic data보다 먼저 처리해야 할 때가 많은 문제가 두 가지다.

- noisy label
- long-tailed distribution

`Learning to Learn From Noisy Labeled Data`는 noisy label 학습이 별도 연구 축이어야 함을 보여 준 고전적 기준선이다.[6]

최근에는 이 문제가 long-tail과 결합된 형태로 더 자주 나타난다.  
`Label-Noise Learning with Intrinsically Long-Tailed Data`는 label noise와 tail imbalance가 동시에 있을 때 표준 기법이 쉽게 무너질 수 있음을 보여 준다.[7]

실전적으로 중요한 이유는 간단하다.

- 희귀 클래스일수록 라벨이 더 불안정하다
- pseudo-label을 쓰면 tail이 더 악화되기 쉽다
- synthetic 샘플을 추가해도 real label noise를 덮지는 못한다

즉 data-centric vision의 우선순위는 종종 아래 순서가 맞다.

1. 라벨 품질 정리
2. 클래스/도메인 불균형 보정
3. hard case 추가 수집
4. 그다음 synthetic data

## 7. dataset distillation과 subset selection은 왜 중요해졌나

dataset distillation은 큰 데이터를 작은 대표 synthetic set으로 압축하려는 분야다.  
처음엔 장난감 문제처럼 보였지만, 지금은 의미가 훨씬 커졌다.

- 반복 학습 비용 절감
- edge용 재학습 파이프라인 단순화
- 데이터 저장 비용 감소
- 빠른 ablation과 hyperparameter search

`Scaling Up Dataset Distillation to ImageNet-1K with Constant Memory`는 이 주제를 대규모 이미지셋으로 끌어올린 중요한 작업이다.[8]  
`SelMatch`는 selection과 distillation을 결합하는 방향이 더 강할 수 있음을 보여 준다.[9]

이 흐름의 함의는 명확하다.

- 앞으로 “어떤 데이터를 학습에 넣을지”가 아키텍처 선택만큼 중요해진다
- 모든 데이터를 매번 다 쓰는 훈련 파이프라인은 비효율적일 수 있다
- 현장 배포용 모델 업데이트는 full retraining보다 data-compressed retraining 쪽으로 갈 수 있다

## 8. CNN 기반 CV 엔지니어 입장에서의 실전 프레임

CNN 기반 영상처리 엔지니어라면 synthetic data를 너무 거창하게 볼 필요는 없다.  
아래처럼 보는 편이 현실적이다.

### 8-1. 먼저 실제 데이터의 실패 분포를 계측한다

- 어떤 조명에서 깨지는가
- 어떤 배경에서 오탐이 나는가
- 어떤 결함 크기에서 미탐이 늘어나는가
- 어느 장비/센서에서 붕괴하는가

### 8-2. synthetic data는 그 실패 분포를 메우는 용도로 쓴다

- rare case 확장
- hard negative 생성
- annotation 비용이 큰 segmentation mask 보강
- extreme condition 보강

### 8-3. real과 synthetic을 섞되 분리 평가한다

- real-only baseline
- real + synthetic
- synthetic pretrain + real finetune

이 세 조건을 분리해서 봐야 무엇이 실제로 도움이 되는지 알 수 있다.

### 8-4. 최종 평가는 항상 real deployment proxy에서 한다

- natural shift
- calibration
- false positive cost
- latency under real pipeline

synthetic validation이 좋아도 real deployment proxy가 나쁘면 그 실험은 실패다.

## 9. 앞으로 중요한 연구 질문

지금 시점에서 특히 값어치가 큰 질문은 아래다.

1. `synthetic diversity`와 `realistic nuisance fidelity` 중 무엇이 더 중요한가
2. synthetic pretraining 이득은 어떤 도메인에서 longest-lasting transfer로 남는가
3. dataset distillation은 분류를 넘어 detection/segmentation까지 얼마나 실용화될 수 있는가
4. noisy label 정제와 synthetic augmentation 중 어느 쪽이 투자 대비 이득이 큰가
5. synthetic data가 calibration과 uncertainty estimation에 주는 영향은 무엇인가

## 10. 읽기 우선순위

이 주제를 빠르게 잡으려면 아래 순서가 좋다.

1. noisy label / long-tail 학습  
데이터 품질 문제가 얼마나 크고 복합적인지 이해

2. synthetic pretraining과 synthetic-only learning  
synthetic data의 현실적 쓰임새 파악

3. 산업/도메인 특화 synthetic 사례  
어디서 진짜 잘 먹히는지 확인

4. dataset distillation / subset selection  
앞으로의 효율화 방향 이해

5. robustness evaluation  
synthetic 성능을 어디까지 믿어야 하는지 점검

---

## 최종 정리

2026년의 data-centric vision은 `모델보다 데이터를 보자`는 구호 수준을 이미 넘었다.

더 정확한 표현은 아래에 가깝다.

- 모델은 점점 범용화된다
- 성능 차이는 점점 데이터 파이프라인에서 벌어진다
- synthetic data는 매우 강력하지만, 분포 설계를 잘못하면 오히려 착시를 준다
- 실제 경쟁력은 `real data quality + synthetic coverage + honest evaluation` 조합에서 나온다

즉 좋은 비전 시스템은 더 이상 `강한 backbone 하나`가 아니라, `좋은 데이터 생성·정제·선택·평가 루프`를 가진 시스템이다.

---

## 참고문헌

[1] Ge et al., *BEHAVIOR Vision Suite: Customizable Dataset Generation via Simulation* (CVPR 2024)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2024/html/Ge_BEHAVIOR_Vision_Suite_Customizable_Dataset_Generation_via_Simulation_CVPR_2024_paper.html)

[2] Tian et al., *Learning Vision from Models Rivals Learning Vision from Data* (CVPR 2024)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2024/html/Tian_Learning_Vision_from_Models_Rivals_Learning_Vision_from_Data_CVPR_2024_paper.html)

[3] Law and Deng, *Label-Free Synthetic Pretraining of Object Detectors* (WACV 2024)  
[CVF Open Access](https://openaccess.thecvf.com/content/WACV2024/html/Law_Label-Free_Synthetic_Pretraining_of_Object_Detectors_WACV_2024_paper.html)

[4] Fulir et al., *Synthetic Data for Defect Segmentation on Complex Metal Surfaces* (CVPR Workshops 2023)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2023W/VISION/html/Fulir_Synthetic_Data_for_Defect_Segmentation_on_Complex_Metal_Surfaces_CVPRW_2023_paper.html)

[5] Singh et al., *Is Synthetic Data all We Need? Benchmarking the Robustness of Models Trained with Synthetic Images* (CVPR Workshops 2024)  
[CVF Open Access PDF](https://openaccess.thecvf.com/content/CVPR2024W/SyntaGen/papers/Singh_Is_Synthetic_Data_all_We_Need_Benchmarking_the_Robustness_of_CVPRW_2024_paper.pdf)

[6] Li et al., *Learning to Learn From Noisy Labeled Data* (CVPR 2019)  
[CVF Open Access](https://openaccess.thecvf.com/content_CVPR_2019/html/Li_Learning_to_Learn_From_Noisy_Labeled_Data_CVPR_2019_paper.html)

[7] Lu et al., *Label-Noise Learning with Intrinsically Long-Tailed Data* (ICCV 2023)  
[CVF Open Access](https://openaccess.thecvf.com/content/ICCV2023/html/Lu_Label-Noise_Learning_with_Intrinsically_Long-Tailed_Data_ICCV_2023_paper.html)

[8] Cui et al., *Scaling Up Dataset Distillation to ImageNet-1K with Constant Memory* (ICML 2023)  
[PMLR](https://proceedings.mlr.press/v202/cui23e.html)

[9] Lee and Chung, *SelMatch: Effectively Scaling Up Dataset Distillation via Selection-Based Initialization and Partial Updates by Trajectory Matching* (ICML 2024)  
[PMLR](https://proceedings.mlr.press/v235/lee24g.html)
