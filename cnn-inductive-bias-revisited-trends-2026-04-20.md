# 2026 CNN Inductive Bias 재조명 트렌드 리포트

기준일: 2026-04-20

## 조사 목적

이 문서는 `ViT 이후에도 CNN의 inductive bias가 왜 여전히 중요한가`를 연구 관점에서 정리한 리포트다.

여기서 말하는 핵심 inductive bias는 대체로 다음을 뜻한다.

- locality
- translation equivariance
- weight sharing
- multi-scale hierarchy
- spatially structured feature reuse

이 문서는 단순히 `CNN vs Transformer` 승패를 정리하려는 문서가 아니다.

- 어떤 문제에서 CNN bias가 아직도 유효한가
- Transformer가 실제로는 어떤 CNN priors를 다시 도입했는가
- foundation model 시대에도 도메인 특화 비전에서 CNN적 가정이 왜 다시 중요해지는가
- 앞으로 CNN 연구를 하려면 무엇을 “복원”하고 무엇을 “업데이트”해야 하는가

## 빠른 결론

1. `CNN bias는 사라진 것이 아니라, 많은 강한 비전 Transformer가 다시 부분적으로 도입한 설계 원리로 재등장했다.`
2. `대규모 사전학습과 강한 regularization이 있으면 ViT가 강하지만, 소규모 데이터·dense prediction·고해상도·실시간 제약에서는 CNN적 bias의 가치가 계속 드러난다.`
3. `ConvNeXt 이후의 흐름은 "CNN이 구식이다"가 아니라 "어떤 bias는 남기고 어떤 학습 관행은 Transformer 시대 방식으로 업데이트하자"에 가깝다.`
4. `앞으로의 좋은 CNN 연구는 순수 복고가 아니라, locality·hierarchy·equivariance를 현대적인 학습 스택과 결합하는 방향이어야 한다.`

---

## 1. 문제의식: 왜 지금 다시 CNN bias를 봐야 하나

ViT는 대규모 사전학습 환경에서 매우 강력한 결과를 보여 주었다. 원조 ViT 논문은 `충분히 큰 데이터셋으로 사전학습하면` pure transformer가 이미지 분류에서 매우 잘 동작함을 보였다.[1]

하지만 이 결과는 곧바로 `CNN bias가 필요 없다`는 뜻은 아니었다. 오히려 반대로, 비전 영역에서는 다음 조건들이 여전히 중요하다는 점이 반복적으로 드러났다.

- 데이터가 작을 때의 sample efficiency
- detection/segmentation 같은 dense prediction
- 고해상도 입력에서의 계산 비용
- 작은 객체와 다중 스케일 구조
- 배포 시 latency, memory locality, operator maturity

즉, ViT의 성공은 CNN bias의 완전한 폐기가 아니라 `bias와 scale의 교환관계`를 더 분명하게 드러낸 사건에 가깝다.

## 2. CNN이 원래 제공하던 것

CNN의 강점은 단순히 convolution 연산 그 자체가 아니다. 더 정확히는 아래 구조적 가정을 한 번에 제공했다는 데 있다.

### 2-1. locality

초기 층에서 가까운 공간 이웃을 우선적으로 결합한다. 이는 이미지의 국소적 상관구조와 잘 맞는다.

### 2-2. translation equivariance

같은 필터를 공간 전체에 공유하므로, 물체 위치가 변해도 같은 검출기가 작동한다. classification뿐 아니라 detection, tracking, segmentation에서 특히 중요하다.

### 2-3. hierarchical representation

깊어질수록 receptive field가 넓어지고 해상도는 줄며 의미 수준은 올라간다. 이 구조는 multi-scale vision task와 매우 잘 맞는다.

### 2-4. efficient sliding-window reuse

feature reuse와 operator 최적화가 잘 맞물린다. 실제 시스템에서는 FLOPs만이 아니라 memory traffic, kernel maturity, cache behavior가 중요하므로 이 이점은 아직도 작지 않다.

## 3. ViT가 가져온 변화는 무엇이었나

ViT는 patch token과 global self-attention으로 이미지를 처리하며, CNN이 강하게 고정하던 국소 priors를 훨씬 약하게 둔다.[1]

그 결과 얻는 장점은 명확하다.

- 더 유연한 장거리 상호작용
- architecture scaling의 단순성
- 대규모 pretraining과 결합했을 때의 높은 ceiling

하지만 비용도 있었다.

- 더 약한 지역 priors
- 작은 데이터에서의 학습 불안정성
- 고해상도에서 self-attention 비용 부담
- dense prediction으로 갈수록 추가 구조가 필요해짐

결국 비전에서 Transformer가 강해지는 과정은, NLP의 pure transformer를 그대로 가져오는 과정이 아니라 `비전용 priors를 다시 주입하는 과정`이었다.

## 4. 중요한 관찰 1: 강한 ViT들은 실제로 CNN적 priors를 다시 들여왔다

### 4-1. Swin Transformer는 hierarchy와 locality를 재도입했다

Swin Transformer는 shifted window를 통해 self-attention을 local window 안에서 계산하고, patch merging을 통해 계층적 representation을 만든다.[2]

중요한 함의는 이렇다.

- dense prediction에 강하려면 단일 해상도 token sequence만으로는 불리하다
- vision backbone으로 쓰려면 hierarchy가 다시 필요하다
- attention을 쓰더라도 locality 제약이 계산 효율과 일반화에 모두 도움이 된다

즉 Swin의 성공은 `CNN이 틀렸음`의 증거가 아니라, `Transformer도 결국 비전에서는 multi-scale local prior를 필요로 한다`는 증거에 가깝다.

### 4-2. CvT와 ConViT는 convolutional bias를 명시적으로 주입했다

CvT는 transformer 내부에 convolution을 도입해 shift/scale/distortion invariance를 강화하려 했다.[3] ConViT는 “soft convolutional inductive bias”라는 표현을 전면에 내세웠다.[4]

이 계열의 메시지는 명확하다.

- 비전 Transformer의 표현력이 높더라도, 초기 stage의 지역 bias는 여전히 유효하다
- hard bias와 soft bias 사이의 절충이 가능하다
- inductive bias는 성능 ceiling을 낮추는 족쇄가 아니라 sample efficiency를 사는 구조적 장치일 수 있다

### 4-3. early convolution stem은 “초반 bias”의 중요성을 다시 보여 줬다

`Early Convolutions Help Transformers See Better`는 patchify stem 앞단의 convolution이 optimization stability와 성능 모두에 유익할 수 있음을 보였다.[5]

연구적으로 보면 이건 매우 중요하다.

- bias는 전층에 균일하게 뿌릴 필요가 없을 수 있다
- 초반 representation formation 단계만 bias를 걸어도 충분할 수 있다
- “CNN vs ViT”보다 “어느 stage에서 어떤 bias가 필요한가”가 더 좋은 질문일 수 있다

## 5. 중요한 관찰 2: ConvNeXt는 CNN을 “현대적 대안”으로 되살렸다

ConvNeXt는 표준 ResNet을 Transformer 시대의 학습 관행과 설계 선택에 맞춰 modernize하면서, pure ConvNet만으로도 경쟁력 있는 결과를 얻을 수 있음을 보였다.[6]

이 논문이 던진 메시지는 매우 강하다.

- 일부 성능 격차는 attention의 본질적 우월성보다 training recipe와 macro design 차이에서 왔을 수 있다
- ConvNet은 더 이상 예전 ResNet 설정으로만 평가하면 안 된다
- hierarchical backbone, large kernel, inverted bottleneck, layer norm 등 현대화된 설계로 순수 CNN도 충분히 강하다

특히 ConvNeXt가 detection과 segmentation에서도 강한 결과를 보였다는 점은 중요하다.[6]

## 6. 중요한 관찰 3: data regime에 따라 inductive bias의 가치가 달라진다

### 6-1. 작은 데이터에서는 bias가 곧 regularization이다

`When Vision Transformers Outperform ResNets without Pre-training or Strong Data Augmentations`는 ViT가 적절한 regularization과 optimization을 갖추면 scratch에서도 강해질 수 있음을 보였지만, 동시에 그 과정 자체가 더 민감하고 recipe 의존적임을 드러냈다.[7]

즉, 해석은 이렇게 해야 한다.

- ViT가 작은 데이터에서 절대 안 된다는 것은 아니다
- 그러나 scratch regime에서 ViT는 CNN보다 더 많은 recipe engineering을 요구하는 경우가 많다
- CNN bias는 이런 설정에서 구조적 regularizer로 작동한다

DeiT 역시 외부 데이터 없이도 ViT를 강하게 훈련할 수 있음을 보였지만, 그 핵심은 distilled training과 강한 augmentation/regularization이었다.[8]

### 6-2. bias를 줄이면 데이터 요구량은 대체로 올라간다

ConViT는 hard inductive bias가 sample-efficient learning에 유리하지만 성능 ceiling을 제한할 수 있다고 정리했다.[4]

- 강한 bias: 적은 데이터, 빠른 수렴, 구조적 안정성
- 약한 bias: 더 큰 표현 유연성, 큰 데이터에서 높은 ceiling

앞으로의 핵심 질문은 `bias를 없앨 것인가`가 아니라 `bias의 강도를 어떻게 제어할 것인가`다.

## 7. 중요한 관찰 4: robustness와 shape-texture 문제는 아직 끝나지 않았다

Geirhos 등은 ImageNet-trained CNN이 shape보다 texture에 더 치우치는 경향을 보였고, shape bias를 높이면 정확도와 robustness가 함께 좋아질 수 있음을 보였다.[9]

한편 `Intriguing Properties of Vision Transformers`와 `Understanding The Robustness in Vision Transformers`는 ViT가 occlusion, corruption, shape 관련 평가에서 흥미로운 강점을 보인다고 분석했다.[10][11]

해석을 잘해야 한다.

- 일부 robustness 축에서는 ViT가 더 낫다
- 그러나 이것이 곧 모든 실제 배포 조건에서 ViT가 더 믿을 만하다는 뜻은 아니다
- 어떤 robustness가 architecture에서 오고, 어떤 robustness가 pretraining/data scale에서 오는지 분리해야 한다

## 8. 중요한 관찰 5: dense prediction은 CNN bias의 가치를 더 오래 남긴다

classification과 달리 detection/segmentation은 아래 요구가 강하다.

- 정확한 spatial localization
- multi-scale object handling
- 고해상도 feature map 유지
- local detail과 global context의 동시 활용

그래서 많은 strong detector/segmenter는 결국 다음 중 하나를 택했다.

- CNN backbone 유지
- hierarchical transformer 사용
- multi-scale feature pyramid 재도입
- local attention, deformable attention, hybrid stem 사용

`Benchmarking Detection Transfer Learning with Vision Transformers`도 detection transfer를 공정하게 평가하려면 backbone 호환성, feature pyramid, pretraining 방식, memory cost 등을 함께 봐야 함을 강조했다.[12]

## 9. 연구적으로 지금 정말 유용한 질문들

### 9-1. bias placement question

CNN bias를 어디에 넣는 것이 가장 효율적인가.

- stem만 convolution으로 둘 것인가
- early stage만 local prior를 둘 것인가
- decoder 쪽에만 multi-scale prior를 둘 것인가

### 9-2. bias strength question

hard equivariance가 필요한가, 아니면 soft locality bias면 충분한가.

- conv
- local attention
- relative position bias
- deformable sampling

을 동일 budget에서 비교할 가치가 크다.

### 9-3. regime-dependent comparison

아래 조건별 ranking이 달라지는지 보는 것이 중요하다.

- small data
- long-tail
- noisy labels
- OOD shift
- dense prediction
- edge deployment

### 9-4. representation geometry

CNN과 ViT의 feature geometry 차이가 실제 transfer와 robustness 차이로 이어지는지 분석할 필요가 있다.

## 10. 연구 설계 제안

이 주제를 실제 연구 프로젝트로 밀어 붙이려면 다음 비교축이 좋다.

- backbone family: ResNet-50, ConvNeXt-T, ViT-B/16, Swin-T, ConViT 또는 conv-stem ViT
- task family: ImageNet-1K classification, COCO detection, ADE20K segmentation, domain-specific dataset 1개
- regime split: full data, 10% labels, long-tail subset, synthetic corruption, natural shift
- measurement: clean accuracy, corruption robustness, calibration, convergence speed, GPU memory, latency, small-object AP 또는 boundary quality

핵심은 `단일 top-1`로 결론 내리지 않는 것이다.

## 11. 앞으로의 해석: CNN 연구는 어디로 가야 하나

지금 CNN 연구가 생산적이려면 아래 방향이 좋다.

- pure nostalgia가 아니라 `현대적 학습 스택과 결합된 CNN bias 연구`
- architecture 자체보다 `bias placement`와 `regime dependence`를 보는 연구
- classification만이 아니라 dense prediction, robustness, efficiency를 함께 보는 연구
- domain-specific vision에서 필요한 priors를 명시적으로 설계하는 연구

특히 의료영상, 산업검사, 위성, 로봇 비전, edge vision처럼 데이터가 작고 shift가 크며 latency가 중요한 분야에서는 CNN적 관점이 여전히 매우 강한 연구 출발점이 된다.

---

## 최종 정리

CNN이 끝났다는 표현은 과장에 가깝다.

- `순수하고 강한 CNN bias만으로 모든 문제를 해결하던 시대는 끝났다.`
- `하지만 비전 문제의 구조와 시스템 제약 때문에, locality·hierarchy·equivariance는 여전히 중요하다.`
- `실제로 많은 성공적인 Transformer는 이 priors를 다른 형태로 다시 도입했다.`

즉 2026년 기준으로 CNN 연구자에게 가장 좋은 태도는 `방어`가 아니라 `재정식화`다. 질문은 더 이상 `CNN이 아직 살아 있나`가 아니라 `어떤 bias가 어떤 조건에서 얼마나 필요한가`여야 한다.

---

## 참고문헌

[1] Dosovitskiy et al., *An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale* (ICLR 2021)  
[OpenReview](https://openreview.net/forum?id=YicbFdNTTy)

[2] Liu et al., *Swin Transformer: Hierarchical Vision Transformer Using Shifted Windows* (ICCV 2021)  
[CVF Open Access](https://openaccess.thecvf.com/content/ICCV2021/html/Liu_Swin_Transformer_Hierarchical_Vision_Transformer_Using_Shifted_Windows_ICCV_2021_paper.html)

[3] Wu et al., *CvT: Introducing Convolutions to Vision Transformers* (ICCV 2021)  
[CVF Open Access](https://openaccess.thecvf.com/content/ICCV2021/html/Wu_CvT_Introducing_Convolutions_to_Vision_Transformers_ICCV_2021_paper.html)

[4] D'Ascoli et al., *ConViT: Improving Vision Transformers with Soft Convolutional Inductive Biases* (ICML 2021)  
[PMLR](https://proceedings.mlr.press/v139/d-ascoli21a.html)

[5] Xiao et al., *Early Convolutions Help Transformers See Better* (CoRR 2021)  
[OpenReview PDF](https://openreview.net/pdf?id=Lpfh1Bpqfk)

[6] Liu et al., *A ConvNet for the 2020s* (CVPR 2022)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2022/html/Liu_A_ConvNet_for_the_2020s_CVPR_2022_paper.html)

[7] Chen et al., *When Vision Transformers Outperform ResNets without Pre-training or Strong Data Augmentations* (ICLR 2022)  
[OpenReview PDF](https://openreview.net/pdf/97b8505eb7034c4bfaee9c7d480a9f605be6fea8.pdf)

[8] Touvron et al., *Training data-efficient image transformers & distillation through attention* (ICML 2021)  
[PMLR](https://proceedings.mlr.press/v139/touvron21a.html)

[9] Geirhos et al., *ImageNet-trained CNNs are biased towards texture; increasing shape bias improves accuracy and robustness* (ICLR 2019)  
[OpenReview](https://openreview.net/forum?id=Bygh9j09KX)

[10] Naseer et al., *Intriguing Properties of Vision Transformers* (NeurIPS 2021)  
[OpenReview PDF](https://openreview.net/pdf?id=o2mbl-Hmfgd)

[11] Zhou et al., *Understanding The Robustness in Vision Transformers* (ICML 2022)  
[PMLR](https://proceedings.mlr.press/v162/zhou22m.html)

[12] Li et al., *Benchmarking Detection Transfer Learning with Vision Transformers* (CoRR 2021)  
[arXiv](https://arxiv.org/abs/2111.11429)
