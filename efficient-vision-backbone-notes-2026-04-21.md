# 2026 Efficient Vision Backbone 연구노트

기준일: 2026-04-21

## 목적

이 문서는 `지금 비전 백본(backbone)을 어떻게 봐야 하는가`를 연구 관점에서 정리한 노트다.

여기서 말하는 백본은 단순 분류 모델을 뜻하지 않는다.  
오히려 다음 태스크에서 공통 표현 추출기로 쓰이는 구조를 뜻한다.

- image classification
- object detection
- segmentation
- tracking / video understanding
- multimodal vision encoder
- on-device / edge deployment

핵심 질문은 아래다.

- 2026년 기준으로 강한 비전 백본 계보는 어떻게 나뉘는가
- CNN, ViT, hierarchical transformer, deformable conv, SSM 계열을 어떤 기준으로 비교해야 하는가
- “좋은 백본”은 정확도만 높은 모델인가, 아니면 데이터 조건과 시스템 제약에 따라 달라지는가

## 빠른 결론

1. `단일 백본이 모든 조건에서 최선인 시대는 아니다.`
2. `분류 중심의 최고 성능과 dense task/배포 효율 중심의 최고 성능은 종종 다르다.`
3. `좋은 백본을 고르려면 accuracy보다 먼저 inductive bias, scaling behavior, resolution cost, downstream compatibility를 봐야 한다.`
4. `ConvNeXt 계열은 CNN을 현대화한 강한 기준선이고, Swin 계열은 vision transformer를 dense task 친화적으로 만든 기준선이며, InternImage는 대규모 비전용 convolution의 재부상을 보여 준다.`
5. `최근 SSM/Mamba 계열은 long-range modeling과 linear-time scaling의 가능성을 보여 주지만, 아직은 “모든 곳에 바로 대체 가능한 지배적 백본”이라기보다 탐색 가치가 큰 신흥 축으로 보는 편이 정확하다.`

---

## 1. 좋은 백본을 평가할 때 무엇을 봐야 하나

비전 백본을 비교할 때 흔히 top-1 accuracy만 본다.  
하지만 실제 연구와 시스템 설계에서는 그 기준이 너무 약하다.

적어도 아래 6가지는 함께 봐야 한다.

### 1-1. inductive bias

- locality가 강한가
- translation equivariance를 얼마나 유지하는가
- multi-scale hierarchy를 구조적으로 제공하는가
- 전역 문맥을 early stage부터 잘 섞는가

### 1-2. scaling behavior

- 데이터가 늘 때 얼마나 잘 올라가는가
- 해상도가 커질 때 계산량이 어떻게 증가하는가
- 모델 크기를 키웠을 때 학습 안정성이 유지되는가

### 1-3. downstream compatibility

- detection/segmentation neck과 잘 붙는가
- feature pyramid를 만들기 쉬운가
- dense prediction에서 고해상도 특성을 잘 보존하는가

### 1-4. optimization recipe sensitivity

- 강한 augmentation과 regularization이 꼭 필요한가
- scratch 학습에 민감한가
- pretraining 의존도가 큰가

### 1-5. deployment efficiency

- FLOPs 대비 실제 latency가 좋은가
- operator가 하드웨어 친화적인가
- memory footprint가 작은가

### 1-6. transfer robustness

- small-data fine-tuning에 안정적인가
- natural distribution shift에서 성능이 덜 무너지는가
- calibration이나 uncertainty behavior가 덜 나쁜가

이 프레임으로 봐야 백본 논쟁이 “유행 모델 소개”에서 “연구 설계”로 넘어간다.

## 2. 큰 계보: 비전 백본은 대략 다섯 축으로 정리된다

2026년 시점의 대표 계보는 아래처럼 정리하는 편이 가장 실용적이다.

1. `고전 CNN 효율 계보`
2. `현대화된 ConvNet 계보`
3. `pure / hierarchical transformer 계보`
4. `대규모 비전 특화 convolution 계보`
5. `SSM / hybrid long-range modeling 계보`

이 다섯 축을 순서대로 보는 것이 좋다.

---

## 3. 고전 CNN 효율 계보: MobileNetV2와 EfficientNet이 남긴 것

### 3-1. MobileNetV2의 핵심

MobileNetV2는 inverted residual과 linear bottleneck으로 mobile setting에서 표현력과 비용 사이 균형을 잡았다.[1]

왜 아직도 중요한가.

- depthwise separable convolution은 여전히 모바일 연산 최적화의 기준점이다
- 채널 확장 후 projection하는 inverted residual은 경량 네트워크 설계의 기본 문법이 되었다
- on-device vision에서 operator maturity가 중요하다는 사실을 일찍 보여 줬다

즉 MobileNetV2는 “옛날 모바일 모델”이 아니라, 지금도 `효율성은 연산식만이 아니라 하드웨어 친화적 분해 전략에서 나온다`는 교훈을 준다.

### 3-2. EfficientNet의 핵심

EfficientNet은 depth, width, resolution을 compound scaling으로 함께 조정해야 효율적이라는 점을 보여 줬다.[2]

연구적으로 중요한 포인트는 아래다.

- 스케일링은 한 축만 키우는 문제가 아니다
- 좋은 백본은 base architecture뿐 아니라 scaling law가 좋아야 한다
- downstream에 붙을 모델이라면 backbone 자체 성능보다 “어떻게 커지는가”가 더 중요할 수 있다

오늘 기준으로 EfficientNet 계열이 SOTA의 중심은 아니지만, `효율 연구의 사고방식`에는 여전히 남아 있다.

## 4. 현대화된 ConvNet 계보: ConvNeXt와 ConvNeXt V2

### 4-1. ConvNeXt가 바꾼 것

ConvNeXt는 ResNet을 Transformer 시대의 학습 관행에 맞게 업데이트하면 pure ConvNet도 매우 강력한 backbone이 될 수 있음을 보여 줬다.[3]

중요한 메시지는 이렇다.

- CNN의 한계로 보였던 일부는 아키텍처 본질보다 recipe 문제였을 수 있다
- large kernel, patchify stem, inverted bottleneck, LayerNorm 같은 요소는 CNN에도 효과적이다
- dense prediction에서는 여전히 계층형 ConvNet이 강력한 기본 선택지다

즉 ConvNeXt는 `CNN이 끝나지 않았다`는 주장보다, `현대적 기준선으로 재평가해야 한다`는 주장에 가깝다.

### 4-2. ConvNeXt V2의 의미

ConvNeXt V2는 masked autoencoder류 자기지도 학습과 ConvNet co-design을 더 밀어붙인 작업이다.[4]

이 논문이 중요한 이유는 다음과 같다.

- self-supervised pretraining은 Transformer만의 영역이 아님을 보여 줬다
- ConvNet도 representation learning regime을 바꾸면 추가로 좋아질 수 있음을 보여 줬다
- backbone 비교는 supervised-only 세팅으로 끝내면 안 된다는 점을 강조한다

연구자 관점에서는 이 지점이 중요하다.

- ConvNet이 약해서 진 것이 아니라 pretraining culture에서 뒤처졌을 수 있다
- 따라서 “Conv vs ViT” 비교는 pretraining protocol까지 맞춰야 공정하다

## 5. Transformer 백본 계보: ViT, DeiT, Swin

### 5-1. ViT의 의미

ViT는 patch token과 self-attention만으로 large-scale pretraining 하에서 매우 강한 이미지 인식을 달성했다.[5]

이 모델이 남긴 가장 큰 교훈은 다음이다.

- 충분한 데이터와 compute가 있으면 약한 inductive bias도 높은 ceiling을 가질 수 있다
- 비전 백본도 NLP처럼 token sequence view로 다룰 수 있다
- scale이 model design의 일부가 되었다

하지만 곧바로 현실 문제가 드러났다.

- small-data에서 불안정
- dense task와의 결합 비용
- 고해상도 attention 비용

### 5-2. DeiT의 의미

DeiT는 외부 거대 데이터 없이도 data-efficient training recipe로 ViT를 강하게 훈련할 수 있음을 보였다.[6]

연구적으로 보면 중요한 메시지는 이렇다.

- 약한 구조적 bias의 부족분을 학습 절차가 메워 줄 수 있다
- knowledge distillation과 augmentation은 backbone 비교에서 매우 큰 교란변수다
- “어떤 백본이 더 낫나”를 말할 때 recipe를 빼면 결론이 흔들린다

### 5-3. Swin의 의미

Swin Transformer는 shifted window와 patch merging으로 hierarchical representation을 복원했다.[7]

왜 중요한가.

- dense vision에서는 hierarchy가 구조적으로 중요하다는 점을 다시 확인시켰다
- local attention은 효율성 타협이 아니라 비전용 inductive bias로 볼 수 있다
- transformer backbone도 vision downstream task에 맞추려면 결국 multi-scale을 다시 도입하게 된다는 점을 보여 줬다

즉 Swin은 “pure transformer의 승리”라기보다 “vision transformer의 현실적 수렴점”에 가깝다.

## 6. 대규모 비전 특화 convolution: InternImage

InternImage는 deformable convolution을 대규모 비전 foundation model 관점으로 확장하며, large-scale vision backbone으로서의 ConvNet 잠재력을 다시 부각시켰다.[8]

이 모델이 중요한 이유는 아래다.

- convolution도 단순 local fixed kernel에 머무르지 않는다
- deformable sampling은 locality와 adaptivity를 함께 제공한다
- large model regime에서도 비전 특화 spatial inductive bias가 여전히 강력할 수 있다

특히 dense prediction 관점에서 deformable mechanism은 매우 매력적이다.

- 객체 크기와 형태 변화에 더 유연하다
- feature sampling을 태스크 구조에 맞게 조정할 수 있다
- detection/segmentation과 자연스럽게 잘 맞는다

즉 InternImage는 `대규모 백본 = transformer`라는 단순 도식을 깨는 대표 사례다.

## 7. SSM / Mamba 계열: 새로운 백본 축인가, 아직은 탐색 축인가

### 7-1. Vision Mamba 계열의 약속

Visual state space model 계열은 self-attention의 quadratic cost를 피하면서 장거리 의존성을 효율적으로 다룰 수 있다는 기대를 받는다.  
Vision Mamba는 bidirectional state space model로 효율적 시각 표현 학습 가능성을 제시했다.[9]

### 7-2. VMamba의 의미

VMamba는 2D selective scan을 도입해 vision backbone으로서 SSM을 좀 더 직접적으로 제시했다.[10]

이 계열이 흥미로운 이유는 다음과 같다.

- attention 없는 long-range mixing 대안이 생긴다
- 해상도 증가에 따른 scaling이 더 유리할 가능성이 있다
- token mixer 설계의 새로운 축을 연다

하지만 아직 조심해야 한다.

- operator maturity가 conv만큼 높지 않다
- 실제 latency가 이론 FLOPs 개선과 일치하지 않을 수 있다
- dense task와의 결합에서 검증이 더 필요하다

### 7-3. MambaVision의 의미

MambaVision은 hybrid Mamba-transformer backbone으로, pure replacement보다 결합형 설계가 현실적일 수 있음을 보여 준다.[11]

이건 중요한 신호다.

- 신흥 백본이 기존 계보를 단숨에 대체하기보다 hybrid로 스며드는 경우가 많다
- 연구 질문은 “누가 최종 승자인가”보다 “어떤 mixing mechanism을 어디에 둘 것인가”가 더 생산적이다

## 8. 태스크별로 어떤 백본이 유리한가

### 8-1. classification 중심

대규모 pretraining 환경에서는 ViT류가 여전히 매우 강하다.[5]  
하지만 strong ConvNet도 충분히 경쟁력 있고, 특히 moderate-scale setting에서는 ConvNeXt류가 여전히 강한 기준선이다.[3][4]

### 8-2. detection / segmentation 중심

계층형 구조와 multi-scale 표현이 중요하므로 Swin, ConvNeXt, InternImage처럼 dense task 친화성이 높은 백본이 더 실용적이다.[3][7][8]

### 8-3. mobile / edge 중심

여전히 MobileNet류와 EfficientNet류 사고방식이 중요하다.[1][2]  
이 환경에서는 operator availability와 memory access pattern이 accuracy 몇 점보다 더 중요할 수 있다.

### 8-4. small-data / domain-specific 중심

강한 구조적 bias가 있는 CNN/ConvNet류가 더 안정적인 출발점이 되는 경우가 많다.  
Transformer류는 pretraining 혜택이 크지만, fine-tuning sensitivity와 data requirement를 함께 봐야 한다.[3][5][6]

### 8-5. long-range video / high-resolution 중심

SSM/Mamba 계열과 hierarchical transformer 계열 모두 연구 가치가 있다.  
다만 2026년 현재는 “이론적으로 유망”과 “실전적으로 검증 완료”를 구분해서 봐야 한다.[9][10][11]

## 9. 실제로 백본을 고를 때 쓸 수 있는 의사결정 프레임

실험을 시작하기 전 아래 질문을 먼저 던지면 좋다.

1. 내 태스크는 classification 중심인가, dense prediction 중심인가
2. 데이터는 큰가, 작은가
3. inference target은 서버인가, edge인가
4. pretraining을 쓸 수 있는가
5. 고해상도 입력이 핵심인가
6. 학습 속도와 재현성이 얼마나 중요한가

이 질문에 대한 대략적 선택 가이드는 아래와 같다.

- `강한 기준선 + 안정성`이 필요하면 ConvNeXt
- `대규모 pretraining ceiling`을 보고 싶으면 ViT/DeiT 계열
- `dense task와 multi-scale 친화성`이 중요하면 Swin
- `비전 특화 적응형 spatial modeling`을 보고 싶으면 InternImage
- `새 mixing mechanism 실험`이 목적이면 VMamba / MambaVision
- `모바일 배포`가 핵심이면 MobileNetV2 / EfficientNet 계보를 시작점으로

## 10. 지금 시점에서 연구적으로 가장 중요한 질문

### 10-1. backbone quality는 representation 문제인가, training culture 문제인가

ConvNeXt와 DeiT가 보여 준 가장 중요한 사실 중 하나는, architecture 우열의 일부가 recipe와 pretraining setup에 묻혀 있다는 점이다.[3][4][6]

### 10-2. dense prediction에서 multi-scale은 본질인가

Swin과 InternImage, 그리고 detection/segmentation 계보를 보면 multi-scale hierarchy는 단순 전통이 아니라 문제 구조일 가능성이 크다.[7][8]

### 10-3. long-range modeling은 attention만의 영역인가

SSM 계열은 여기에 새로운 반론을 제시한다.[9][10][11]

### 10-4. 실제 효율은 왜 이론 효율과 다르게 보이는가

모바일과 edge에서는 operator maturity, memory traffic, framework support가 큰 차이를 만든다.[1][2]

## 11. 내가 바로 해볼 만한 비교 실험

### 실험 A: modern ConvNet vs hierarchical transformer

- backbone: ConvNeXt-T vs Swin-T
- task: COCO detection 또는 ADE20K segmentation
- 측정: AP/mIoU, latency, GPU memory, convergence speed

### 실험 B: large-scale pretraining benefit

- backbone: ConvNeXt V2 vs ViT/DeiT
- 설정: supervised pretraining vs self-supervised pretraining
- 질문: backbone 차이보다 pretraining 차이가 더 큰가

### 실험 C: domain-specific small-data transfer

- backbone: ConvNeXt, Swin, InternImage
- 데이터: 의료/산업/원격탐사 중 하나
- 질문: 적은 데이터와 shift 환경에서 어떤 구조가 가장 안정적인가

### 실험 D: new mixer evaluation

- backbone: Swin vs VMamba vs MambaVision
- 입력: 고해상도 또는 긴 시퀀스
- 질문: 이론 scaling 이점이 실제 wall-clock과 downstream metric으로 이어지는가

## 12. 읽기 우선순위

이 주제를 빠르게 잡으려면 아래 순서가 좋다.

1. MobileNetV2, EfficientNet  
효율 백본의 기본 문법

2. ViT, DeiT  
Transformer 백본이 왜 강해졌는지

3. Swin  
dense task 친화적 transformer의 핵심

4. ConvNeXt, ConvNeXt V2  
현대적 ConvNet의 재정립

5. InternImage  
대규모 비전 특화 convolution

6. Vision Mamba, VMamba, MambaVision  
차세대 mixer 탐색

---

## 최종 정리

2026년의 비전 백본 연구는 `CNN vs Transformer` 같은 이분법으로 보면 거의 반드시 놓친다.

더 정확한 프레임은 아래다.

- 어떤 태스크인가
- 어떤 데이터 regime인가
- 어떤 pretraining을 쓸 것인가
- 어떤 해상도와 latency 제약이 있는가
- 어떤 spatial prior와 global mixing이 필요한가

즉 좋은 백본은 절대적 승자가 아니라 `문제 구조와 시스템 제약에 가장 잘 맞는 표현 추출기`다.

---

## 참고문헌

[1] Sandler et al., *MobileNetV2: Inverted Residuals and Linear Bottlenecks* (CVPR 2018)  
[CVF Open Access](https://openaccess.thecvf.com/content_cvpr_2018/html/Sandler_MobileNetV2_Inverted_Residuals_CVPR_2018_paper.html)

[2] Tan and Le, *EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks* (ICML 2019)  
[PMLR](https://proceedings.mlr.press/v97/tan19a.html)

[3] Liu et al., *A ConvNet for the 2020s* (CVPR 2022)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2022/html/Liu_A_ConvNet_for_the_2020s_CVPR_2022_paper.html)

[4] Woo et al., *ConvNeXt V2: Co-designing and Scaling ConvNets with Masked Autoencoders* (CVPR 2023)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2023/html/Woo_ConvNeXt_V2_Co-Designing_and_Scaling_ConvNets_With_Masked_Autoencoders_CVPR_2023_paper.html)

[5] Dosovitskiy et al., *An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale* (ICLR 2021)  
[OpenReview](https://openreview.net/forum?id=YicbFdNTTy)

[6] Touvron et al., *Training data-efficient image transformers & distillation through attention* (ICML 2021)  
[PMLR](https://proceedings.mlr.press/v139/touvron21a.html)

[7] Liu et al., *Swin Transformer: Hierarchical Vision Transformer Using Shifted Windows* (ICCV 2021)  
[CVF Open Access](https://openaccess.thecvf.com/content/ICCV2021/html/Liu_Swin_Transformer_Hierarchical_Vision_Transformer_Using_Shifted_Windows_ICCV_2021_paper.html)

[8] Wang et al., *InternImage: Exploring Large-Scale Vision Foundation Models with Deformable Convolutions* (CVPR 2023)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2023/html/Wang_InternImage_Exploring_Large-Scale_Vision_Foundation_Models_With_Deformable_Convolutions_CVPR_2023_paper.html)

[9] Zhu et al., *Vision Mamba: Efficient Visual Representation Learning with Bidirectional State Space Model* (ICML 2024)  
[PMLR](https://proceedings.mlr.press/v235/zhu24f.html)

[10] Liu et al., *VMamba: Visual State Space Model* (NeurIPS 2024)  
[OpenReview](https://openreview.net/forum?id=5cZoCHnHFG)

[11] Hatamizadeh et al., *MambaVision: A Hybrid Mamba-Transformer Vision Backbone* (CVPR 2025)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2025/html/Hatamizadeh_MambaVision_A_Hybrid_Mamba-Transformer_Vision_Backbone_CVPR_2025_paper.html)
