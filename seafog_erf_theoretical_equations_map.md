# 항만 CCTV 해무 ERF 논문용 이론 수식 맵

이 문서는 기존 [seafog_erf_70_references_curated_detailed.md](C:/Users/ust21/simulation/gradcam_all/data1/wj/seafog/results/seafog_erf_70_references_curated_detailed.md)을 바탕으로, `현상 설명용`이 아니라 `이론/방법론 수식`으로 가져올 가치가 있는 논문들만 다시 추린 정리본이다.

정리 원칙은 다음과 같다.

- 본문에 실제로 넣을 수식만 남긴다.
- 단순히 논문에 식이 있다는 이유로 넣지 않고, `우리 논문 주장`을 직접 받치는 식만 남긴다.
- 수식을 그대로 복사하기보다, `기호는 우리 논문 표기계에 맞게 재정의`하는 것을 기본으로 한다.
- Chrome에서 직접 읽은 논문은 별도로 표시한다.

## 1. 최종 선별

### A. 본문에 직접 넣을 가치가 큰 수식

- `[R18]` Understanding the Effective Receptive Field in Deep Convolutional Neural Networks
- `[R14]/[R13]/[R15]` CAM, Grad-CAM, Grad-CAM++
- `[R16]` Integrated Gradients
- `[R29]` MobileNets
- `[R27]` Multi-Scale Context Aggregation by Dilated Convolutions
- `[R38]` Deformable Convolutional Networks
- `[R55]` VISOR-NET
- `[R56]` Visibility Estimation Based on Weakly Supervised Learning under Discrete Label Distribution
- `[R65]` Decoupled Weight Decay Regularization
- `[R66]` Focal Loss for Dense Object Detection
- `[R70]` classification metrics paper

### B. 이론적이지만 식까지 본문에 넣을 필요는 낮은 논문

- `[R19]` RepLKNet: large kernel design rationale
- `[R21]` UniConvNet: ERF expansion with AGD
- `[R22]` LKA
- `[R24]` SAGR
- `[R25]` heterogeneous receptive field reparameterization
- `[R26]` numerical ERF investigation

이 그룹은 `식 인용`보다 `설계 철학 인용`이 더 적절하다.

## 2. 추천 본문 구성

### 꼭 넣을 만한 식

1. ERF 정의식
2. Grad-CAM 식
3. depthwise separable convolution 복잡도 식
4. dilated convolution 또는 deformable convolution 식
5. ordinal / label-distribution visibility 식 중 1개
6. AdamW 또는 focal loss 중 실제 사용 식
7. macro-F1 / precision / recall 식

### 넣어도 되지만 본문이 무거워질 수 있는 식

1. Grad-CAM++
2. Integrated Gradients
3. VISOR-NET의 pairwise ranking loss 전체
4. discrete label distribution의 KL + CE 결합식

## 3. 핵심 수식 정리

## [R18] ERF 정의

논문: `Understanding the Effective Receptive Field in Deep Convolutional Neural Networks`  
용도: `Related Work` 또는 `Theory`에서 ERF를 정의할 때 핵심.

추천 식:

```tex
R_{ij}^{(p,q)} = \left| \frac{\partial y_{p,q}}{\partial x_{ij}} \right|
```

의미:

- `x_{ij}`: 입력 이미지의 `(i,j)` 위치 픽셀 또는 feature
- `y_{p,q}`: 출력 feature map의 `(p,q)` 위치 활성값
- `R_{ij}^{(p,q)}`: 출력 위치 `(p,q)`에 대해 입력 위치 `(i,j)`가 미치는 실제 영향도

논문에서 가져갈 핵심 해석:

- theoretical receptive field와 actual effective receptive field는 다르다.
- 실제 ERF는 중심부에 강하게 집중된다.
- 실효 분포는 대체로 Gaussian-like하게 나타난다.

논문에 넣을 문장 예시:

`Following Luo et al., the effective receptive field can be characterized by the gradient magnitude of an output activation with respect to the input, indicating that the practically used spatial support is substantially smaller and more center-concentrated than the theoretical receptive field.`

실전 메모:

- 너 논문에서는 이 식 하나면 충분하다.
- Gaussian 유도식까지 길게 넣기보다 `center-concentrated` 해석만 문장으로 붙이는 편이 낫다.

## [R14] CAM

논문: `Learning Deep Features for Discriminative Localization`  
용도: Grad-CAM을 소개하기 전에 CAM 계보를 짧게 깔 때.

추천 식:

```tex
M_c(x,y) = \sum_k w_k^c f_k(x,y)
```

의미:

- `f_k(x,y)`: 마지막 convolution feature map의 `k`번째 채널 값
- `w_k^c`: class `c`에 대한 channel importance
- `M_c(x,y)`: class `c`의 class activation map

논문에 넣을 문장 예시:

`CAM expresses class evidence as a weighted sum of the final convolutional feature maps, thereby linking class prediction to spatial support regions.`

실전 메모:

- CAM은 본문에서 1문장 + 1식 정도면 충분하다.
- main visualization이 Grad-CAM이면 CAM은 계보 설명용으로만 둔다.

## [R13] Grad-CAM

논문: `Grad-CAM: Visual Explanations from Deep Networks via Gradient-based Localization`  
Chrome 확인: abstract 직접 확인  
용도: 네 논문 `Methods`와 `Results`에서 가장 직접적으로 쓸 식.

추천 식:

```tex
\alpha_k^c = \frac{1}{Z} \sum_i \sum_j \frac{\partial y^c}{\partial A_{ij}^k}
```

```tex
L_{\mathrm{Grad\text{-}CAM}}^c = \mathrm{ReLU}\left( \sum_k \alpha_k^c A^k \right)
```

의미:

- `A^k`: 선택된 convolution layer의 `k`번째 feature map
- `A_{ij}^k`: 그 spatial 위치값
- `y^c`: class `c`의 score
- `Z`: spatial normalization term
- `\alpha_k^c`: class `c`에 대한 channel importance
- `L_{\mathrm{Grad-CAM}}^c`: 최종 class-discriminative heatmap

논문에 넣을 문장 예시:

`We adopt Grad-CAM, where the channel importance weights are computed by global average pooling of the class-specific gradients and the final heatmap is obtained by a ReLU-weighted combination of the feature maps.`

실전 메모:

- 이 식은 거의 그대로 넣어도 된다.
- 너 논문에서는 `baseline vs ERF-expanded backbone`의 attention shift를 설명하는 핵심 식이다.

## [R15] Grad-CAM++

논문: `Grad-CAM++: Generalized Gradient-Based Visual Explanations for Deep Convolutional Networks`  
용도: appendix 또는 “more precise localization” 대안 설명.

핵심 식:

```tex
L_{\mathrm{Grad\text{-}CAM++}}^c = \mathrm{ReLU}\left( \sum_k w_k^c A^k \right)
```

```tex
w_k^c = \sum_i \sum_j \alpha_{ij}^{kc} \, \mathrm{ReLU}\left( \frac{\partial y^c}{\partial A_{ij}^k} \right)
```

의미:

- Grad-CAM과 달리 위치별 coefficient `\alpha_{ij}^{kc}`를 둬서 finer localization을 유도한다.

실전 메모:

- main paper엔 꼭 필요하지 않다.
- reviewer가 “Grad-CAM만으로 충분하냐”를 물을 때 appendix 후보로 좋다.

## [R16] Integrated Gradients

논문: `Axiomatic Attribution for Deep Networks`  
Chrome 확인: PMLR 본문/abstract 직접 확인  
용도: Discussion 또는 robustness check용.

추천 식:

```tex
\mathrm{IG}_i(x) = (x_i - x_i') \int_0^1 \frac{\partial F\!\left(x' + \alpha (x - x')\right)}{\partial x_i} \, d\alpha
```

의미:

- `x`: 실제 입력
- `x'`: baseline input
- `F(\cdot)`: 모델 출력 함수
- `\mathrm{IG}_i(x)`: 입력 `i`차원 feature의 attribution

논문에 넣을 문장 예시:

`Integrated Gradients accumulates gradients along the straight-line path from a baseline input to the actual input, thereby satisfying sensitivity and implementation invariance.`

실전 메모:

- 네 본문 main text엔 안 넣어도 된다.
- `future work`나 `robustness check` 설명에 유용하다.

## [R29] Depthwise Separable Convolution

논문: `MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications`  
용도: 왜 MobileNet/Xception 계열이 작은 spatial operator에 의존하는지 설명할 때.

표준 convolution 연산량:

```tex
\mathrm{Cost}_{\mathrm{std}} = D_K^2 \, M \, N \, D_F^2
```

depthwise separable convolution 연산량:

```tex
\mathrm{Cost}_{\mathrm{dwsep}} = D_K^2 \, M \, D_F^2 + M \, N \, D_F^2
```

의미:

- `D_K`: kernel size
- `D_F`: feature map spatial size
- `M`: input channels
- `N`: output channels

논문에 넣을 문장 예시:

`Depthwise separable convolution reduces computational cost by decomposing standard convolution into depthwise spatial filtering and pointwise channel mixing, which makes small-kernel backbones attractive but also tends to preserve highly local spatial processing.`

실전 메모:

- 이 식은 backbone 비교를 정당화할 때 꽤 좋다.
- MobileNet/Xception이 왜 local shortcut에 취약할 수 있는지 연결하기 쉽다.

## [R27] Dilated Convolution

논문: `Multi-Scale Context Aggregation by Dilated Convolutions`  
용도: receptive field expansion의 일반형 설명.

추천 식:

```tex
y[i] = \sum_k x[i + r \cdot k] \, w[k]
```

2D 표기:

```tex
y(\mathbf{p}) = \sum_{\mathbf{k}} x(\mathbf{p} + r \mathbf{k}) \, w(\mathbf{k})
```

의미:

- `r`: dilation rate
- `w`: convolution kernel
- dilation은 parameter 수를 크게 늘리지 않고 receptive field를 확장한다.

논문에 넣을 문장 예시:

`Dilated convolution enlarges the receptive field by inserting gaps between kernel samples, providing a parameter-efficient route for context aggregation without increasing kernel parameters proportionally.`

실전 메모:

- large kernel과의 대비 설명용으로 좋다.
- 네 paper에서는 “broader context aggregation”의 일반형으로 쓰면 된다.

## [R38] Deformable Convolution

논문: `Deformable Convolutional Networks`  
용도: adaptive receptive field alternative 설명.

추천 식:

```tex
y(\mathbf{p}_0) = \sum_{\mathbf{p}_n \in \mathcal{R}} w(\mathbf{p}_n)\, x(\mathbf{p}_0 + \mathbf{p}_n + \Delta \mathbf{p}_n)
```

의미:

- `\mathcal{R}`: regular convolution sampling grid
- `\Delta \mathbf{p}_n`: learned offset
- fixed grid 대신 learnable sampling location을 사용한다.

논문에 넣을 문장 예시:

`Deformable convolution replaces the fixed sampling grid with learnable offsets, making receptive field adaptation data-dependent rather than purely kernel-size-dependent.`

실전 메모:

- 네 paper가 adaptive RF까지 가는 건 아니므로 main text에서 1식 이상은 과하다.
- Discussion에서 “future adaptive ERF design”용으로 적절하다.

## [R55] VISOR-NET: ordinal relative learning

논문: `VISOR-NET: Visibility Estimation Based on Deep Ordinal Relative Learning under Discrete-Level Labels`  
Chrome 확인: MDPI 본문에서 pairwise relation, logistic-like loss, clustering loss 확인  
용도: `lowvis-seafog` 경계가 hard class가 아니라 ordinal structure를 가진다는 근거를 수식으로 보여줄 때.

권장 재구성식:

pairwise relative score:

```tex
r_{ij} = \sigma(\hat{v}_i - \hat{v}_j)
```

pairwise logistic loss:

```tex
L_{\mathrm{rank}} = - \frac{1}{N_p} \sum_{(i,j)} \left[ o_{ij} \log r_{ij} + (1-o_{ij}) \log (1-r_{ij}) \right]
```

optional clustering loss:

```tex
L_{\mathrm{clust}} = \sum_{k} \sum_{i \in \mathcal{C}_k} \lVert \hat{v}_i - c_k \rVert_2^2
```

total loss:

```tex
L = L_{\mathrm{rank}} + \lambda L_{\mathrm{clust}}
```

의미:

- `\hat{v}_i`: image `i`의 latent visibility score
- `o_{ij}`: ordinal relation label
- `c_k`: class `k`의 중심
- discrete class 사이에 latent ordering을 학습한다.

논문에 넣을 문장 예시:

`Ordinal-relative learning models visibility as an ordered latent variable rather than a purely nominal class, which is well aligned with the ambiguous transition between low-visibility and sea-fog samples.`

실전 메모:

- 네 논문 main text에 이 loss를 그대로 쓸 필요는 없다.
- 대신 `ordinal structure`를 설명하는 reference equation으로 1개만 넣는 것은 충분히 의미 있다.

## [R56] Discrete Label Distribution Learning

논문: `Visibility Estimation Based on Weakly Supervised Learning under Discrete Label Distribution`  
Chrome 확인: MDPI 본문에서 softmax, KL divergence loss, cross-entropy loss, total loss 구조 확인  
용도: ambiguous boundary label을 hard label 대신 soft distribution으로 볼 수 있다는 근거.

softmax:

```tex
p_k = \frac{\exp(z_k)}{\sum_j \exp(z_j)}
```

base branch KL loss:

```tex
L_{\mathrm{KL}} = \sum_k q_k \log \frac{q_k}{p_k}
```

attention branch CE loss:

```tex
L_{\mathrm{CE}} = - \sum_k y_k \log p_k^{(a)}
```

overall loss:

```tex
L = L_{\mathrm{KL}} + \lambda L_{\mathrm{CE}}
```

의미:

- `q_k`: discrete label distribution
- `p_k`: base branch predicted distribution
- `p_k^{(a)}`: attention branch prediction
- single hard label보다 인접 visibility level uncertainty를 더 잘 반영한다.

논문에 넣을 문장 예시:

`A label-distribution view is more suitable than a hard one-hot label when visibility changes gradually across adjacent fog levels, because it explicitly allows neighborhood uncertainty among ordinal classes.`

실전 메모:

- 이 식은 너 논문에서 매우 유용하다.
- 실제로 이 loss를 쓰지 않더라도 `label ambiguity`를 수식으로 설명하는 reference equation이 된다.

## [R60] Pixel-wise visibility regression

논문: `Deep Multi-head Regression Network for Pixel-wise Visibility Estimation from Hazy Images`  
용도: visibility가 scene-wide scalar가 아니라 spatial field일 수 있다는 점을 수식적으로 정당화.

권장 재구성식:

```tex
\hat{V} = f_\theta(I), \qquad \hat{V} \in \mathbb{R}^{H \times W}
```

pixel-wise regression loss:

```tex
L_{\mathrm{pix}} = \frac{1}{HW} \sum_{u=1}^{H} \sum_{v=1}^{W} \left\| \hat{V}_{uv} - V_{uv} \right\|_2^2
```

논문에 넣을 문장 예시:

`Pixel-wise visibility estimation formulations support the view that visibility degradation is spatially non-uniform, which is consistent with our observation that distant structures can be severely degraded even when local foreground edges remain sharp.`

실전 메모:

- main paper에 꼭 넣을 필요는 없다.
- Discussion에서 visibility의 spatial heterogeneity를 설명할 때 강하다.

## [R65] AdamW

논문: `Decoupled Weight Decay Regularization`  
Chrome 확인: arXiv abstract 직접 확인  
용도: optimizer 설명.

추천 식:

```tex
\theta_{t+1} = \theta_t - \eta_t \frac{\hat{m}_t}{\sqrt{\hat{v}_t} + \epsilon} - \eta_t \lambda \theta_t
```

의미:

- gradient step과 weight decay term이 분리되어 있다.
- adaptive optimizer에서 `L_2 penalty`와 `weight decay`를 동일시하지 않는다.

논문에 넣을 문장 예시:

`We employ AdamW, where weight decay is decoupled from the adaptive gradient update, following the recommendation of Loshchilov and Hutter.`

실전 메모:

- Methods에 한 줄 + 한 식이면 충분하다.

## [R66] Focal Loss

논문: `Focal Loss for Dense Object Detection`  
Chrome 확인: arXiv abstract 직접 확인  
용도: future work 또는 imbalance 대응 식.

추천 식:

```tex
\mathrm{FL}(p_t) = - \alpha_t (1-p_t)^\gamma \log(p_t)
```

의미:

- `p_t`: ground-truth class probability
- `\alpha_t`: class balancing factor
- `\gamma`: focusing parameter

논문에 넣을 문장 예시:

`Focal loss down-weights well-classified samples and concentrates the optimization on hard examples, making it a plausible extension for lowvis-seafog confusion under class imbalance.`

실전 메모:

- 네가 실제로 focal loss를 안 썼으면 main method에는 넣지 말고 discussion/future work로 보내는 게 낫다.

## [R70] 평가 지표

논문: `A systematic analysis of performance measures for classification tasks; Evaluation: from precision, recall and F-measure to ROC, informedness, markedness and correlation`  
Chrome 확인: arXiv abstract 직접 확인  
용도: 평가식 정당화.

precision:

```tex
\mathrm{Precision} = \frac{TP}{TP+FP}
```

recall:

```tex
\mathrm{Recall} = \frac{TP}{TP+FN}
```

F1:

```tex
F_1 = \frac{2 \cdot \mathrm{Precision} \cdot \mathrm{Recall}}{\mathrm{Precision} + \mathrm{Recall}}
```

macro-F1:

```tex
\mathrm{Macro}\text{-}F_1 = \frac{1}{C} \sum_{c=1}^{C} F_{1,c}
```

optional informedness:

```tex
J = \mathrm{TPR} + \mathrm{TNR} - 1
```

논문에 넣을 문장 예시:

`Because overall accuracy can mask class-specific failure modes, we prioritize class-wise recall and macro-F1 in addition to confusion analysis.`

실전 메모:

- 너 논문에서는 `precision/recall/F1/macro-F1`만 써도 충분하다.
- informedness까지 들어가면 다소 과할 수 있다.

## 4. 수식 포함 우선순위

### 본문 메인에 강하게 추천

1. `[R18]` ERF 정의식
2. `[R13]` Grad-CAM 식
3. `[R29]` depthwise separable convolution cost
4. `[R27]` dilated convolution 또는 `[R38]` deformable convolution 중 하나
5. `[R56]` label distribution 식 또는 `[R55]` ordinal loss 식 중 하나
6. `[R65]` AdamW
7. `[R70]` macro-F1

### appendix 또는 supplementary 추천

1. `[R15]` Grad-CAM++
2. `[R16]` Integrated Gradients
3. `[R55]` clustering-inclusive full ordinal objective
4. `[R60]` pixel-wise visibility regression

## 5. 네 논문용 최소 수식 세트

너 논문이 `ERF 확장 -> local shortcut 완화 -> scene-level visibility cue 활용`을 주장한다면, 가장 깔끔한 최소 세트는 다음이다.

### Theory

```tex
R_{ij}^{(p,q)} = \left| \frac{\partial y_{p,q}}{\partial x_{ij}} \right|
```

### Visualization

```tex
\alpha_k^c = \frac{1}{Z} \sum_i \sum_j \frac{\partial y^c}{\partial A_{ij}^k}, \qquad
L_{\mathrm{Grad\text{-}CAM}}^c = \mathrm{ReLU}\left( \sum_k \alpha_k^c A^k \right)
```

### Backbone rationale

```tex
\mathrm{Cost}_{\mathrm{dwsep}} = D_K^2 M D_F^2 + M N D_F^2
```

### Ambiguous visibility label rationale

```tex
L_{\mathrm{KL}} = \sum_k q_k \log \frac{q_k}{p_k}
```

### Optimization

```tex
\theta_{t+1} = \theta_t - \eta_t \frac{\hat{m}_t}{\sqrt{\hat{v}_t} + \epsilon} - \eta_t \lambda \theta_t
```

### Evaluation

```tex
F_1 = \frac{2PR}{P+R}, \qquad
\mathrm{Macro}\text{-}F_1 = \frac{1}{C} \sum_{c=1}^{C} F_{1,c}
```

이 6개만 있어도 논문 수식 축은 충분히 단단해진다.

## 6. 집필 주의점

- `RepLKNet`, `UniConvNet`, `LKA`류는 식을 길게 가져오면 오히려 산만해진다. 설계 rationale 인용 위주가 낫다.
- `Grad-CAM`은 반드시 네 실험 setting에 맞게 layer notation을 다시 정의해서 쓰는 게 좋다.
- `VISOR-NET`과 `label distribution learning`은 너의 실제 training loss가 아니라 `label ambiguity의 이론 근거`로 쓰는 편이 자연스럽다.
- 수식이 많아질수록 reviewer는 “실제로 다 썼나?”를 본다. 실제 사용하지 않은 식은 main method보다 theory/discussion 쪽에 배치하는 게 안전하다.

## 7. Chrome 확인 메모

이번 정리에서 Chrome으로 직접 확인한 핵심 논문은 다음과 같다.

- `Axiomatic Attribution for Deep Networks`
- `VISOR-NET`
- `Visibility Estimation Based on Weakly Supervised Learning under Discrete Label Distribution`
- `Decoupled Weight Decay Regularization`
- `Focal Loss for Dense Object Detection`

또한 기존 라운드에서 직접 확인한 논문과 합쳐서 아래 논문들의 맥락도 반영했다.

- `Grad-CAM`
- `Shortcut learning in deep neural networks`
- `ImageNet-trained CNNs are biased towards texture`
- `BagNet`
- `ERF expansion / large-kernel 계열`

