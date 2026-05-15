# 항만 CCTV 해무 분류 ERF 논문 최종 레퍼런스 정리

이 문서는 지금까지 정리한 문헌을 실제 논문 집필 기준으로 다시 번호를 매겨 정리한 **최종 reference map**이다.  
목표는 다음 네 가지다.

1. 각 논문에 **최종 번호**를 부여한다.
2. 각 논문이 **논문의 어느 파트**에 들어갈지 정리한다.
3. 각 논문이 **어떤 논리**를 담당하는지 정리한다.
4. 각 논문이 **논문 본문에서 어떤 식의 문장으로 들어갈지**, 그리고 그 문장의 **원논문 근거 표현**이 무엇인지 정리한다.

주의:

- 아래 `원문 근거(핵심 표현)`은 최종 본문에 넣기 위한 **짧은 근거 표현** 위주로 정리했다.
- 긴 직인용을 피하고, 실제 투고 직전에는 원 PDF에서 표현과 페이지를 한 번 더 대조하는 것이 좋다.
- 이번 문서는 **메인 본문에 실제로 쓰일 가능성이 높은 논문만** 남긴 최종 집필용 목록이다.

---

## 1. 최종 번호 체계

### A. Introduction 중심

| 최종 번호 | 논문 | 역할 |
|---|---|---|
| [R1] | Choi et al. 2018 | CCTV 기반 해무/시정 판단의 실용성 |
| [R2] | Han et al. 2021 | 한국 항만 해무 문제의 운영 중요성, 연구 연속성 |
| [R3] | KMA Visibility Standard | 시정 등급 기준 |
| [R4] | ICAO Visibility Definition | 운영 안전과 visibility의 직접 연결 |
| [R5] | KHOA Sea Fog Observation Data | 데이터 출처와 서비스 맥락 |
| [R6] | Geirhos et al. 2020 | shortcut learning 프레임 |
| [R7] | Geirhos et al. 2019 texture bias | texture-biased CNN 근거 |
| [R8] | Brendel and Bethge 2019 BagNet | local patch evidence reliance 근거 |
| [R9] | Luo et al. 2016 ERF | actual ERF가 작다는 이론적 근거 |
| [R10] | Ding et al. 2022 RepLKNet | large kernel / broader context 동기 |
| [R11] | Huang et al. 2022 correlation context | sea fog에 context가 중요함 |
| [R12] | Xu et al. 2022 UDA sea fog | domain gap과 annotation difficulty |
| [R13] | VISOR-NET 2022 | visibility의 ordinal structure |
| [R14] | Weakly supervised visibility 2023 | lowvis-seafog 경계와 uneven visibility |
| [R15] | Multi-feature fusion under complex background 2025 | global context와 long-range dependency의 도메인 근거 |

### B. Related Work 중심

| 최종 번호 | 논문 | 역할 |
|---|---|---|
| [R16] | ConvNeXt 2022 | modern CNN의 larger depthwise kernel 흐름 |
| [R17] | Xception 2017 | depthwise separable backbone 원조 |
| [R18] | MobileNetV3 2019 | 실용적 small-kernel backbone |
| [R19] | EfficientNetV2 2021 | 효율적 modern CNN backbone |
| [R20] | Swin Transformer 2021 | broader context baseline |
| [R21] | Spatio-Temporal Network 2022 | CCTV sea fog forecasting 선행연구 |
| [R22] | Multi-satellite matching 2025 | ground truth 구축과 label quality 중요성 |
| [R23] | Sakaridis et al. 2018 | foggy scene annotation difficulty, synthetic fog |
| [R24] | CMAda 2019 | synthetic-to-real fog adaptation |

### C. Methods / Training / Evaluation 중심

| 최종 번호 | 논문 | 역할 |
|---|---|---|
| [R25] | Grad-CAM 2017 | 시각화 해석 도구 |
| [R26] | CAM 2016 | discriminative localization 계보 |
| [R27] | ImageNet challenge 2015 | 사전학습 정당화 |
| [R28] | Two-step transfer learning 2026 | 2-stage 학습 전략 정당화 |
| [R29] | AdamW 2019 | optimizer 선택 정당화 |
| [R30] | Sokolova and Lapalme 2009 | macro-F1 등 지표 정당화 |
| [R31] | Powers 2011 | recall / F-measure / 다면 평가 정당화 |
| [R32] | Visualizing and Understanding ConvNets 2013 | perturbation / ablation 계열 해석의 배경 |

---

## 2. 파트별 추천 배치

### Introduction

- [R1], [R2], [R3], [R4], [R5]
- [R6], [R7], [R8]
- [R9], [R10]
- [R11], [R12], [R13], [R14], [R15]

### Related Work

- [R16], [R17], [R18], [R19], [R20]
- [R21], [R22], [R23], [R24]
- [R11], [R12], [R15] 일부 재사용 가능

### Methods

- [R25], [R26], [R27], [R28], [R29], [R30], [R31], [R32]
- [R17], [R18], [R19], [R20]은 backbone provenance 설명용으로 같이 언급 가능

### Discussion

- [R7], [R8], [R10], [R11], [R13], [R14], [R15], [R23], [R24], [R32]

---

## 3. 최종 상세 레퍼런스 맵

## [R1] Choi et al. 2018

### 기본 정보

- 원래 목록 번호: 33
- 제목: *Automatic Sea Fog Detection and Estimation of Visibility on CCTV*

### 넣을 파트

- Introduction

### 담당 논리

- CCTV 기반 sea fog / visibility 판단은 이미 실용적 문제로 다뤄져 왔다.
- 본 연구의 문제 설정 자체를 정당화한다.

### 본문에 넣을 문장

- 기존 연구는 CCTV 영상을 이용한 sea fog detection과 visibility estimation의 가능성을 이미 제시하였으며, 이는 항만 감시 영상 기반 시정 분류가 실용적 응용 가치를 가진 문제임을 보여준다.

### 원문 근거(핵심 표현)

- `"Sea Fog Detection"`
- `"Estimation of Visibility on CCTV"`

---

## [R2] Han et al. 2021

### 기본 정보

- 원래 목록 번호: 34
- 제목: *Sea Fog Dissipation Prediction in Incheon Port and Haeundae Beach Using Machine Learning and Deep Learning*

### 넣을 파트

- Introduction
- Related Work

### 담당 논리

- 한국 항만/해안 환경에서 sea fog는 실제 운영 문제다.
- 본 연구의 데이터 맥락과 연구 연속성을 설명한다.

### 본문에 넣을 문장

- 한국 항만 및 연안 환경에서 sea fog는 실제 운영과 안전에 직접 영향을 주는 현상이며, 기존의 예측 연구 역시 인천항과 해운대 해역을 대상으로 이러한 중요성을 보고한 바 있다.

### 원문 근거(핵심 표현)

- `"reduces the visibility"`
- `"Incheon port"`
- `"Haeundae beach"`

---

## [R3] Korea Meteorological Administration visibility standard

### 기본 정보

- 원래 목록 번호: 68

### 넣을 파트

- Introduction
- Dataset / Methods

### 담당 논리

- normal, lowvis, seafog를 operational visibility 기준과 연결한다.
- 라벨 설계의 제도적 근거다.

### 본문에 넣을 문장

- 본 연구의 시정 등급 구분은 국내 기상 기준에서 사용되는 visibility class definition을 참고하여 설정하였다.

### 원문 근거(핵심 표현)

- `"Grade Standards for Fog and Haze"`

---

## [R4] ICAO visibility definition

### 기본 정보

- 원래 목록 번호: 69

### 넣을 파트

- Introduction

### 담당 논리

- visibility는 안전 운항/운용과 직결되는 operational metric이다.

### 본문에 넣을 문장

- Visibility는 단순한 시각적 품질 변수가 아니라 항행 안전과 직접 연결된 operational indicator이므로, sea fog classification은 실질적인 운용 상태 인식 문제로 볼 수 있다.

### 원문 근거(핵심 표현)

- `"Visibility Definitions"`

---

## [R5] KHOA sea fog observation data

### 기본 정보

- 원래 목록 번호: 70

### 넣을 파트

- Introduction
- Dataset

### 담당 논리

- 데이터 출처와 실제 서비스 연결성을 설명한다.

### 본문에 넣을 문장

- 본 연구는 KHOA의 sea fog observation framework와 연결된 데이터 맥락 위에서 수행되며, 이는 결과의 현장 적용 가능성을 높여준다.

### 원문 근거(핵심 표현)

- `"Sea Fog Observation Data"`

---

## [R6] Geirhos et al. 2020

### 기본 정보

- 원래 목록 번호: 11
- 제목: *Shortcut Learning in Deep Neural Networks*

### 넣을 파트

- Introduction
- Related Work

### 담당 논리

- 모델이 목표 개념 대신 쉬운 proxy cue에 의존할 수 있다는 큰 프레임을 제공한다.
- strong-edge shortcut, weak-texture shortcut 정의의 이론적 출발점이다.

### 본문에 넣을 문장

- 본 연구는 Geirhos et al.의 shortcut learning 관점에 따라, sea fog classifier가 실제 scene-level visibility보다 더 쉬운 local edge-texture proxy에 의존할 수 있다고 가정한다.

### 원문 근거(핵심 표현)

- `"shortcut learning"`
- `"instead of learning the intended solution"`

---

## [R7] Geirhos et al. 2019 texture bias

### 기본 정보

- 추가 보강 논문
- 제목: *ImageNet-trained CNNs are biased towards texture; increasing shape bias improves accuracy and robustness*

### 넣을 파트

- Introduction
- Related Work
- Discussion

### 담당 논리

- CNN이 texture에 편향될 수 있다는 직접 근거를 제공한다.
- 해무 분류에서 local texture/edge bias를 설명하는 핵심 문헌이다.

### 본문에 넣을 문장

- Geirhos et al.은 ImageNet-trained CNN이 shape보다 texture에 더 강한 편향을 보일 수 있음을 보였으며, 이는 본 연구에서 관찰된 local edge-texture shortcut 현상을 해석하는 직접적 근거가 된다.

### 원문 근거(핵심 표현)

- `"texture-shape cue conflict"`
- `"strongly biased towards recognising textures rather than shapes"`

---

## [R8] Brendel and Bethge 2019

### 기본 정보

- 추가 보강 논문
- 제목: *Approximating CNNs with Bag-of-local-Features models works surprisingly well on ImageNet*

### 넣을 파트

- Introduction
- Related Work
- Discussion

### 담당 논리

- spatial ordering 없이 local patch evidence만으로도 높은 성능이 가능하다는 점을 보여준다.
- `성능이 좋다 = 올바른 cue를 본다`가 아니라는 논리를 강화한다.

### 본문에 넣을 문장

- Brendel and Bethge는 spatial ordering을 거의 고려하지 않는 local bag-of-features style model도 높은 분류 성능을 달성할 수 있음을 보였으며, 이는 sea fog classification에서도 좋은 accuracy가 곧 scene-level visibility reasoning을 의미하지 않을 수 있음을 시사한다.

### 원문 근거(핵심 표현)

- `"small local image features"`
- `"without taking into account their spatial ordering"`

---

## [R9] Luo et al. 2016

### 기본 정보

- 원래 목록 번호: 1
- 제목: *Understanding the Effective Receptive Field in Deep Convolutional Neural Networks*

### 넣을 파트

- Introduction
- Related Work

### 담당 논리

- actual ERF는 theoretical RF보다 훨씬 작고 중심 집중적이라는 점을 보여준다.
- small ERF가 local cue dominance로 이어질 수 있다는 가설의 이론적 기반이다.

### 본문에 넣을 문장

- Luo et al.은 deep CNN의 effective receptive field가 theoretical receptive field의 일부만 차지하고 중심부에 집중된다고 보고하였으며, 이는 small-kernel backbone이 장면 전체보다 국소 cue에 더 민감할 수 있음을 시사한다.

### 원문 근거(핵심 표현)

- `"Gaussian distribution"`
- `"only occupies a fraction"`

---

## [R10] Ding et al. 2022

### 기본 정보

- 원래 목록 번호: 2
- 제목: *Scaling Up Your Kernels to 31×31: Revisiting Large Kernel Design in CNNs*

### 넣을 파트

- Introduction
- Related Work
- Discussion

### 담당 논리

- larger kernel과 broader context 활용의 핵심 근거다.
- ERF 확장이 구조적 단서 활용에 유리할 수 있다는 가장 직접적인 modern CNN 근거다.

### 본문에 넣을 문장

- Recent large-kernel CNN research suggests that replacing stacks of small kernels with larger kernels can be a powerful design choice for broader structural context modeling, motivating our ERF redesign in sea fog classification.

### 원문 근거(핵심 표현)

- `"a few large convolutional kernels"`
- `"more powerful paradigm"`

---

## [R11] Huang et al. 2022

### 기본 정보

- 원래 목록 번호: 45
- 제목: *Correlation context-driven method for sea fog detection*

### 넣을 파트

- Introduction
- Related Work
- Discussion

### 담당 논리

- sea fog detection에서 context가 중요하다는 도메인 특화 근거다.
- local texture만으로는 부족하다는 본 연구의 주장과 가장 잘 맞는다.

### 본문에 넣을 문장

- Sea fog detection itself has been argued to require correlation context beyond isolated local patterns, which is consistent with our emphasis on scene-level structural visibility cues.

### 원문 근거(핵심 표현)

- `"context-driven"`
- `"sea fog detection"`

---

## [R12] Xu et al. 2022

### 기본 정보

- 원래 목록 번호: 38
- 제목: *Sea fog detection based on unsupervised domain adaptation*

### 넣을 파트

- Introduction
- Related Work
- Discussion

### 담당 논리

- sea fog annotation difficulty와 domain gap 문제를 직접 다룬다.
- 항만별 시점 차이와 라벨 품질 문제의 선행 근거다.

### 본문에 넣을 문장

- Xu et al. pointed out that sea fog detection suffers from severe annotation difficulty and domain gap, supporting our decision to treat inter-port camera shift and boundary-label instability as first-order issues.

### 원문 근거(핵심 표현)

- `"obtaining visibility information to help the annotations is difficult"`
- `"unsupervised domain adaptation"`

---

## [R13] VISOR-NET 2022

### 기본 정보

- 추가 보강 논문
- 제목: *VISOR-NET: Visibility Estimation Based on Deep Ordinal Relative Learning under Discrete-Level Labels*

### 넣을 파트

- Introduction
- Dataset limitation
- Discussion

### 담당 논리

- visibility level은 단순 hard class가 아니라 ordinal relation을 갖는다.
- lowvis-seafog boundary ambiguity를 정당화한다.

### 본문에 넣을 문장

- VISOR-NET treats visibility estimation as an ordinal problem rather than a purely categorical one, suggesting that the lowvis-seafog boundary in our dataset may lie on a continuous visibility transition rather than a clean hard split.

### 원문 근거(핵심 표현)

- `"ordinal information"`
- `"relative relation"`
- `"lack of real scenes or continuous labels"`

---

## [R14] Weakly supervised visibility 2023

### 기본 정보

- 추가 보강 논문
- 제목: *Visibility Estimation Based on Weakly Supervised Learning under Discrete Label Distribution*

### 넣을 파트

- Introduction
- Discussion

### 담당 논리

- visibility는 이미지 안에서 uneven하게 분포할 수 있다.
- foreground edge와 background degradation의 비대칭성을 설명한다.

### 본문에 넣을 문장

- Recent visibility estimation studies model visibility using discrete label distributions and explicitly exploit uneven fog distribution within an image, which aligns well with our observation that strong foreground edges may coexist with severe distant-structure degradation.

### 원문 근거(핵심 표현)

- `"discrete label distributions"`
- `"uneven fog distribution"`
- `"farthest visible region"`

---

## [R15] Multi-feature fusion under complex background 2025

### 기본 정보

- 추가 보강 논문
- 제목: *A Multi-Feature Fusion Approach for Sea Fog Detection Under Complex Background*

### 넣을 파트

- Introduction
- Related Work
- Discussion

### 담당 논리

- sea fog와 low cloud 혼동, occlusion, large-scale fog region에서 global context와 multi-scale information이 중요하다는 직접 도메인 근거다.

### 본문에 넣을 문장

- Recent sea fog studies under complex background explicitly emphasize long-range dependency and global context, supporting our claim that broader scene-level visibility structure is more informative than isolated local texture in ambiguous fog scenes.

### 원문 근거(핵심 표현)

- `"long-range dependencies"`
- `"global context information"`
- `"fusion of multi-scale information"`

---

## [R16] ConvNeXt 2022

### 기본 정보

- 원래 목록 번호: 3

### 넣을 파트

- Related Work
- Methods

### 담당 논리

- 7×7 depthwise kernel이 modern ConvNet에서 이미 중요한 설계 선택이라는 근거.

### 본문에 넣을 문장

- ConvNeXt demonstrates that relatively large depthwise kernels are already part of modern ConvNet design, making it a natural backbone family for ERF-oriented comparison.

### 원문 근거(핵심 표현)

- `"A ConvNet for the 2020s"`
- `"7×7 depthwise convolution"` 

---

## [R17] Xception 2017

### 기본 정보

- 원래 목록 번호: 17

### 넣을 파트

- Related Work
- Methods
- Discussion

### 담당 논리

- depthwise separable convolution backbone의 대표 사례.
- local shortcut 취약성을 보인 backbone 원형.

### 본문에 넣을 문장

- We include Xception as a representative depthwise-separable backbone because its spatial filtering relies heavily on small-kernel depthwise operations, making it suitable for testing local shortcut susceptibility.

### 원문 근거(핵심 표현)

- `"Depthwise Separable Convolutions"`

---

## [R18] MobileNetV3 2019

### 기본 정보

- 원래 목록 번호: 20

### 넣을 파트

- Related Work
- Methods

### 담당 논리

- practical small-kernel lightweight backbone.

### 본문에 넣을 문장

- MobileNetV3 serves as a practical small-kernel lightweight backbone for testing whether ERF redesign can improve cue utilization even under efficiency-oriented architectures.

### 원문 근거(핵심 표현)

- `"Searching for MobileNetV3"`

---

## [R19] EfficientNetV2 2021

### 기본 정보

- 원래 목록 번호: 22

### 넣을 파트

- Related Work
- Methods

### 담당 논리

- efficient modern CNN backbone.
- scaling만으로 해결되지 않고 ERF 설계도 중요함을 대비시킨다.

### 본문에 넣을 문장

- EfficientNetV2 provides an efficient modern CNN baseline, allowing us to examine whether ERF redesign matters beyond standard scaling improvements.

### 원문 근거(핵심 표현)

- `"Smaller Models and Faster Training"`

---

## [R20] Swin Transformer 2021

### 기본 정보

- 원래 목록 번호: 24

### 넣을 파트

- Related Work
- Methods

### 담당 논리

- broader context를 모델링하는 non-CNN 비교 baseline.

### 본문에 넣을 문장

- We additionally compare against a hierarchical transformer baseline, since Swin explicitly integrates context through shifted windows and thus provides a useful reference point for broader context modeling.

### 원문 근거(핵심 표현)

- `"Hierarchical Vision Transformer"`
- `"Shifted Windows"`

---

## [R21] Spatio-Temporal Network 2022

### 기본 정보

- 원래 목록 번호: 35

### 넣을 파트

- Related Work

### 담당 논리

- CCTV sea fog literature가 이미 forecasting으로 확장돼 있다는 점을 보여준다.
- 그러나 spatial ERF 문제는 아직 직접 다루지 않았다고 대비한다.

### 본문에 넣을 문장

- Prior CCTV-based sea fog research has extended toward spatio-temporal forecasting, but the effect of backbone receptive field design on shortcut behavior in single-frame classification remains underexplored.

### 원문 근거(핵심 표현)

- `"Spatio-Temporal Network for Sea Fog Forecasting"`

---

## [R22] Multi-satellite matching 2025

### 기본 정보

- 원래 목록 번호: 40

### 넣을 파트

- Related Work
- Discussion

### 담당 논리

- label/ground truth quality의 중요성.

### 본문에 넣을 문장

- Recent sea fog detection research has highlighted the importance of careful ground-truth construction, which supports our emphasis on label auditing before drawing conclusions about ERF effects.

### 원문 근거(핵심 표현)

- `"Image Matching"`
- `"Daytime Sea Fog Detection"`

---

## [R23] Sakaridis et al. 2018

### 기본 정보

- 원래 목록 번호: 55
- 제목: *Semantic Foggy Scene Understanding with Synthetic Data*

### 넣을 파트

- Related Work
- Discussion

### 담당 논리

- foggy image annotation difficulty.
- synthetic fog and supervision transfer.
- dehazing의 제한적 효과.

### 본문에 넣을 문장

- Sakaridis et al. showed that real foggy image annotation is difficult and that synthetic fog combined with supervision transfer can be more effective than relying on dehazing alone, reinforcing our emphasis on data quality and supervision design.

### 원문 근거(핵심 표현)

- `"difficulty of collecting and annotating foggy images"`
- `"synthetic fog on real images"`
- `"image dehazing marginally advances"`

---

## [R24] CMAda 2019

### 기본 정보

- 추가 보강 논문
- 제목: *Curriculum Model Adaptation with Synthetic and Real Data for Semantic Foggy Scene Understanding*

### 넣을 파트

- Related Work
- Discussion

### 담당 논리

- synthetic-to-real fog adaptation.
- dense real fog domain gap.

### 본문에 넣을 문장

- Curriculum adaptation studies suggest that foggy-scene recognition requires staged adaptation from synthetic light fog to dense real fog, underscoring how severe the domain gap can be in adverse-weather vision.

### 원문 근거(핵심 표현)

- `"gradually adapts"`
- `"light synthetic fog to dense real fog"`

---

## [R25] Grad-CAM 2017

### 기본 정보

- 원래 목록 번호: 13

### 넣을 파트

- Methods
- Results

### 담당 논리

- ERF 변화 전후 주의 영역을 시각적으로 비교하는 핵심 도구.

### 본문에 넣을 문장

- We use Grad-CAM to inspect whether ERF expansion shifts model attention away from isolated local edges toward broader structural visibility cues.

### 원문 근거(핵심 표현)

- `"Visual Explanations"`
- `"Gradient-based Localization"`

---

## [R26] CAM 2016

### 기본 정보

- 원래 목록 번호: 14

### 넣을 파트

- Methods

### 담당 논리

- localization-based interpretation 계보를 보강한다.

### 본문에 넣을 문장

- Our visualization analysis follows the broader CAM-style tradition of examining which image regions support class decisions.

### 원문 근거(핵심 표현)

- `"Discriminative Localization"`

---

## [R27] ImageNet challenge 2015

### 기본 정보

- 원래 목록 번호: 57

### 넣을 파트

- Methods

### 담당 논리

- 사전학습 기반 backbone initialization 정당화.

### 본문에 넣을 문장

- Because the sea fog dataset is relatively limited in scale, we adopt ImageNet-based pretraining as a standard initialization strategy.

### 원문 근거(핵심 표현)

- `"ImageNet Large Scale Visual Recognition Challenge"`

---

## [R28] Two-step transfer learning 2026

### 기본 정보

- 원래 목록 번호: 58

### 넣을 파트

- Methods

### 담당 논리

- 2-stage transfer setting 정당화.

### 본문에 넣을 문장

- Following recent two-step transfer learning practice, we first pretrain on a generic image dataset and then fine-tune on the port CCTV sea fog dataset.

### 원문 근거(핵심 표현)

- `"two-step transfer learning"`

---

## [R29] AdamW 2019

### 기본 정보

- 원래 목록 번호: 60

### 넣을 파트

- Methods

### 담당 논리

- optimizer 선택.

### 본문에 넣을 문장

- We optimize all models with AdamW, which decouples weight decay from the adaptive update and is widely used in modern vision training.

### 원문 근거(핵심 표현)

- `"Decoupled Weight Decay Regularization"`

---

## [R30] Sokolova and Lapalme 2009

### 기본 정보

- 원래 목록 번호: 66

### 넣을 파트

- Methods

### 담당 논리

- macro-F1 등 multi-metric evaluation의 필요성.

### 본문에 넣을 문장

- We prioritize macro-F1 and class-wise recall rather than overall accuracy, following standard guidance that different classification measures capture different error characteristics.

### 원문 근거(핵심 표현)

- `"performance measures for classification"`

---

## [R31] Powers 2011

### 기본 정보

- 원래 목록 번호: 67

### 넣을 파트

- Methods

### 담당 논리

- recall / F-measure / 다면 평가의 보조 근거.

### 본문에 넣을 문장

- In addition to macro-F1, we separately inspect class-wise recall and confusion patterns because a single metric cannot fully describe the behavior of a fog classifier.

### 원문 근거(핵심 표현)

- `"precision, recall and F-measure"`

---

## [R32] Zeiler and Fergus 2013

### 기본 정보

- 추가 보강 논문
- 제목: *Visualizing and Understanding Convolutional Networks*

### 넣을 파트

- Methods
- Appendix

### 담당 논리

- perturbation / ablation-style interpretation의 고전적 배경.

### 본문에 넣을 문장

- To complement Grad-CAM, we also use perturbation-style inspection, following the broader CNN visualization tradition that examines how model decisions change when informative regions are altered.

### 원문 근거(핵심 표현)

- `"visualization technique"`
- `"ablation study"`

---

## 4. 본문에 실제로 쓸 때의 최소 조합

### Introduction 최소 코어

- [R1], [R2], [R3], [R4], [R5]
- [R6], [R7], [R8]
- [R9], [R10]
- [R11], [R12], [R13], [R14], [R15]

### Related Work 최소 코어

- [R16], [R17], [R18], [R19], [R20]
- [R21], [R22], [R23], [R24]

### Methods 최소 코어

- [R25], [R27], [R28], [R29], [R30], [R31]

### Discussion 최소 코어

- [R7], [R8], [R10], [R11], [R13], [R14], [R15], [R23], [R24], [R32]

---

## 5. 최종 집필 원칙

- 본문 메인 줄기는 `ERF -> shortcut -> texture/local bias -> sea fog context -> label ambiguity -> visualization/evaluation` 순서로 유지한다.
- attention/general object detection literature는 메인 서사에서 뺀다.
- 핵심 claim은 문헌이 아니라 **실험 결과와 Grad-CAM/perturbation 분석이 최종 증명**한다.
- 문헌은 그 claim이 `왜 합리적인 가설인지`, `왜 이 문제를 연구할 가치가 있는지`, `왜 이 해석 프레임이 타당한지`를 받치는 역할에 집중시킨다.
