# 항만 CCTV 해무 분류 ERF 논문용 70편 인용 포인트 정리

이 문서는 [참고문헌_70편_정리.md](C:/Users/ust21/연구노트/참고문헌_70편_정리.md)와 [seafog_erf_research_summary.md](C:/Users/ust21/Downloads/seafog_erf_research_summary.md)를 바탕으로, 각 참고문헌에서 **무엇을 인용할지**와 **본 논문에서 어떻게 연결할지**를 바로 쓸 수 있는 형태로 정리한 것이다.

정리 원칙은 다음과 같다.

- `인용 포인트`: 해당 논문에서 가져올 핵심 논리
- `본 논문 연결`: 우리 논문에서 그 논리를 어떻게 사용할지
- `권장 위치`: Introduction / Related Work / Methods / Discussion 중 추천 위치
- 일부 핵심 논문은 Chrome에서 초록/개요 페이지를 직접 확인했다. 나머지는 제목, 초록 수준 정보, 기존 역할 정리를 기준으로 논문 서사에 맞게 압축했다.

---

## 카테고리 1. ERF 이론 및 Large Kernel 설계

### 1. Understanding the Effective Receptive Field in Deep CNNs
- 인용 포인트: CNN의 실제 ERF는 이론적 receptive field보다 훨씬 작고, 중심부에 집중된 Gaussian-like 분포를 보인다.
- 본 논문 연결: 단순히 네트워크가 깊거나 theoretical RF가 크다고 해서 장면 수준 시정 단서를 충분히 활용하는 것은 아니며, 실제로는 small ERF 때문에 local edge-texture에 끌릴 수 있다고 연결한다.
- 권장 위치: Introduction, Related Work
- 예시 문장: Luo et al. [1]은 deep CNN의 effective receptive field가 theoretical receptive field보다 훨씬 작고 중심부에 집중된다고 보고했으며, 이는 small-kernel backbone이 장면 전체보다 국소 edge-texture 단서에 더 민감할 수 있음을 시사한다.

### 2. Scaling Up Your Kernels to 31×31: Revisiting Large Kernel Design in CNNs
- 인용 포인트: large kernel depthwise convolution은 CNN의 문맥 수용 범위를 넓히고, shape/structure 중심 표현을 강화할 수 있다.
- 본 논문 연결: 해무 분류에서도 ERF 확장이 local shortcut 완화에 기여할 수 있다는 가장 직접적인 근거로 사용한다. 다만 본 연구는 "무조건 클수록 좋다"가 아니라 "적절한 ERF가 중요하다"는 쪽으로 확장한다.
- 권장 위치: Introduction, Related Work, Discussion
- 예시 문장: Ding et al. [2]의 large-kernel CNN 설계 논의에 따르면 broader spatial context는 구조적 단서 활용에 유리하며, 본 연구 역시 ERF 확장이 local shortcut 완화에 기여할 수 있음을 확인하였다.

### 3. A ConvNet for the 2020s
- 인용 포인트: 현대 ConvNet 설계는 7×7 depthwise convolution 같은 비교적 큰 kernel을 다시 적극적으로 채택한다.
- 본 논문 연결: ConvNeXt를 비교 backbone에 포함한 이유와, large-kernel 설계가 이미 현대 CNN의 주요 흐름이라는 점을 설명하는 데 사용한다.
- 권장 위치: Related Work, Methods
- 예시 문장: ConvNeXt [3]는 modern ConvNet design에서 7×7 depthwise convolution을 핵심 구성으로 채택함으로써, larger kernel design이 이미 실용적 backbone 설계의 중요한 흐름임을 보여준다.

### 4. UniConvNet
- 인용 포인트: 최근 ERF 연구는 ERF를 단순 확대하는 것이 아니라, 안정적인 분포 특성을 유지하면서 확장하는 방향을 탐색한다.
- 본 논문 연결: 우리 결과에서 7×7 수준의 moderate expansion이 유리하고 과도한 확대가 항상 이득은 아니라는 해석과 연결한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: 최근 UniConvNet 계열 연구 [4]는 ERF의 무조건적 확대보다 안정적인 분포를 유지하는 확장이 중요함을 시사하며, 이는 본 연구에서 moderate ERF expansion이 가장 효과적이었던 결과와도 일치한다.

### 5. Large Kernel Adapter for Enhanced Medical Image Classification
- 인용 포인트: large-kernel adaptation은 성능 개선 가능성이 있지만, task-specific tuning이 필요하며 과도한 확장은 비효율 또는 성능 저하를 낳을 수 있다.
- 본 논문 연결: Xception/MobileNet/EfficientNet에서 15×15가 항상 최선이 아니고 7×7 또는 branch형 설계가 더 안정적일 수 있다는 논리로 연결한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: Large-kernel adaptation 연구 [5]가 보여주듯이 receptive field 확장은 task-specific tuning이 필요하며, 본 실험에서도 과도한 15×15 확장보다 7×7 또는 branch-style 설계가 더 안정적이었다.

### 6. Exploiting Gaussian Based Effective Receptive Fields for Object Detection
- 인용 포인트: ERF는 분석 대상이 아니라 설계 변수로도 활용될 수 있으며, Gaussian 기반 제어가 가능하다는 관점을 제시한다.
- 본 논문 연결: 향후 연구에서 해무 분류용 동적 ERF 또는 adaptive ERF 설계로 확장할 수 있다는 future work 근거로 사용한다.
- 권장 위치: Discussion
- 예시 문장: Gaussian-based ERF 설계 연구 [6]는 receptive field를 제어 가능한 설계 변수로 다루며, 이는 향후 해무 분류에서도 adaptive ERF 구조로 확장될 가능성을 제시한다.

### 7. Spatially-Adaptive Gradient Re-parameterization for 3D Large Kernel
- 인용 포인트: large-kernel 계열에서도 ERF의 공간적 편향과 gradient 재분배가 성능에 중요하다.
- 본 논문 연결: 해무 분류에서도 중요한 것은 "커널 숫자" 자체보다, 어떤 공간 단서에 주의를 분배하느냐라는 해석으로 연결한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: Spatially-adaptive large-kernel 연구 [7]는 kernel size 자체보다 spatial emphasis distribution이 중요함을 보여주며, 본 연구 역시 어떤 영역을 보게 하느냐가 핵심이라고 해석한다.

### 8. Expert-Like Reparameterization of Heterogeneous Pyramid Receptive Fields
- 인용 포인트: 이질적 receptive field를 병렬/피라미드 구조로 조합하는 설계가 유효할 수 있다.
- 본 논문 연결: 우리 Type B branch-style depthwise convolution이 단일 크기 확장보다 나은 이유를 설명할 때, heterogeneous receptive field 조합과 유사한 아이디어로 연결한다.
- 권장 위치: Related Work, Methods
- 예시 문장: Heterogeneous receptive field 조합 연구 [8]와 유사하게, 본 연구의 Type B 구조는 서로 다른 spatial range를 병렬적으로 활용해 local cue와 broader context의 균형을 맞추고자 했다.

### 9. Numerical Investigation of ERF and Its Relationship with Kernels
- 인용 포인트: kernel size와 실제 ERF 변화의 관계는 단순 선형이 아니며, 구조적 조건에 따라 달라진다.
- 본 논문 연결: 같은 7×7 또는 15×15라도 backbone마다 효과가 다르게 나타난 이유를 설명하는 정량적 배경으로 사용한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: ERF와 kernel size의 관계를 정량적으로 다룬 연구 [9]는 동일한 kernel enlargement라도 architecture에 따라 실제 ERF 변화가 다를 수 있음을 보여주며, 이는 backbone-dependent 결과를 설명한다.

### 10. Multi-Scale Context Aggregation by Dilated Convolutions
- 인용 포인트: receptive field 확장은 large kernel 외에도 dilation을 통해 달성될 수 있으며, 목적은 broader context aggregation이다.
- 본 논문 연결: 본 연구는 dilation 대신 depthwise kernel redesign을 택했지만, 핵심 목적은 동일하게 broader context 확보였다고 설명한다.
- 권장 위치: Related Work
- 예시 문장: Yu and Koltun [10]은 dilated convolution을 통해 broader context aggregation의 중요성을 보여주었으며, 본 연구는 같은 목적을 depthwise kernel redesign으로 달성하고자 했다.

---

## 카테고리 2. Shortcut Learning 및 모델 해석

### 11. Shortcut Learning in Deep Neural Networks
- 인용 포인트: 딥러닝 모델은 목표 개념 자체보다 더 쉽고 우연한 proxy signal에 의존하는 shortcut learning 경향이 있다.
- 본 논문 연결: strong-edge shortcut과 weak/degraded-texture shortcut을 해무 분류의 도메인 특화 shortcut 사례로 정의하는 핵심 이론 근거로 사용한다.
- 권장 위치: Introduction, Related Work
- 예시 문장: Geirhos et al. [11]의 shortcut learning 관점에 따르면, 모델은 종종 목표 개념보다 더 쉬운 proxy cue에 의존하며, 본 연구에서는 strong edge와 local degradation을 그러한 shortcut cue로 해석한다.

### 12. On Measuring Localization of Shortcuts in Deep Networks
- 인용 포인트: shortcut은 모델 내부 특정 layer/region에 국소화될 수 있으며, 이를 측정하는 관점이 중요하다.
- 본 논문 연결: 우리 연구가 Grad-CAM과 ERF 비교를 통해 shortcut의 공간적 위치를 추적하려는 시도라는 점을 정당화한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: Tsoy et al. [12]이 제안한 shortcut localization 관점은 shortcut을 공간적으로 추적할 필요성을 강조하며, 본 연구의 Grad-CAM 분석 역시 이러한 문제의식을 따른다.

### 13. Grad-CAM: Visual Explanations from Deep Networks via Gradient-based Localization
- 인용 포인트: 분류 결정에 기여한 시각 영역을 heatmap 형태로 해석할 수 있는 대표적 방법이다.
- 본 논문 연결: ERF 변화 전후에 모델이 local edge에서 horizon/sea-sky boundary/원거리 구조물 열화로 attention을 이동하는지 확인하는 핵심 도구로 제시한다.
- 권장 위치: Methods
- 예시 문장: 우리는 Selvaraju et al. [13]의 Grad-CAM을 사용해 ERF 조정 전후 모델의 주의 영역이 local edge에서 scene-level visibility structure로 이동하는지를 시각적으로 확인하였다.

### 14. Learning Deep Features for Discriminative Localization (CAM)
- 인용 포인트: 클래스 판별에 유효한 공간 영역을 localization 관점에서 이해하는 초기 프레임을 제공한다.
- 본 논문 연결: Grad-CAM 계열 시각화가 단순한 예시 그림이 아니라, 분류 단서가 어디에 있는지 논의하는 정당한 분석 축이라는 점을 보강한다.
- 권장 위치: Related Work
- 예시 문장: CAM [14]의 기본 아이디어는 분류 성능 해석에서 판별적 공간 영역의 위치를 함께 보는 것이 중요하다는 점을 보여주며, 이는 본 연구의 시각화 분석 방향과 맞닿아 있다.

### 15. Grad-CAM++
- 인용 포인트: Grad-CAM보다 세밀한 localization이 가능한 확장 기법으로 알려져 있다.
- 본 논문 연결: 필요하면 부록 또는 추가 검증에서 local edge hotspot이 single-point artifact인지 아닌지 확인하는 대안적 해석 기법으로 언급한다.
- 권장 위치: Methods, Discussion
- 예시 문장: 보다 세밀한 localization이 필요한 경우 Grad-CAM++ [15]는 local hotspot이 단일 artifact인지 구조적 주의 패턴인지를 추가 검증하는 대안이 될 수 있다.

### 16. Axiomatic Attribution for Deep Networks
- 인용 포인트: attribution의 일관성과 공리적 정당성을 강조하는 대표적 해석 방법이다.
- 본 논문 연결: 향후 Grad-CAM 외 attribution 기법을 추가해 shortcut 해석의 강건성을 확인할 수 있다는 보조 근거로 사용한다.
- 권장 위치: Discussion
- 예시 문장: Sundararajan et al. [16]의 attribution framework는 해석 결과의 일관성을 중시하므로, 향후 본 연구의 shortcut 분석을 다른 attribution 기법으로 교차 검증하는 데 활용될 수 있다.

---

## 카테고리 3. CNN Backbone 아키텍처

### 17. Xception
- 인용 포인트: depthwise separable convolution을 극단적으로 활용한 backbone이며, spatial filtering이 depthwise conv에 강하게 의존한다.
- 본 논문 연결: Xception이 small-kernel depthwise 구조에서 local edge shortcut에 특히 취약하게 나타난 이유를 논의하는 backbone 설명에 사용한다.
- 권장 위치: Methods, Discussion
- 예시 문장: Xception [17]은 depthwise separable convolution에 강하게 의존하는 구조이므로, 본 연구에서는 small-kernel depthwise filtering이 local shortcut을 얼마나 강화하는지 살피는 핵심 backbone으로 사용하였다.

### 18. MobileNets
- 인용 포인트: depthwise separable convolution 기반 경량 backbone의 출발점이다.
- 본 논문 연결: 경량 CNN에서 receptive field 재설계가 왜 중요한 연구 주제인지 설명하는 아키텍처 계보로 사용한다.
- 권장 위치: Related Work
- 예시 문장: MobileNets [18] 이후 depthwise separable convolution은 경량 비전 모델의 표준 요소가 되었으며, 이에 따라 이러한 구조에서 receptive field를 어떻게 설계할지가 중요한 문제가 되었다.

### 19. MobileNetV2
- 인용 포인트: inverted residual과 linear bottleneck을 통해 경량성과 표현력을 개선한 구조다.
- 본 논문 연결: 작은 연산량 backbone에서 ERF 확장이 성능에 미치는 영향을 탐색하는 흐름의 중간 단계 backbone 계보로 언급한다.
- 권장 위치: Related Work
- 예시 문장: MobileNetV2 [19]는 경량 backbone의 표현력을 높였지만 기본적인 small-kernel 설계는 유지하므로, ERF redesign 효과를 논의하는 맥락에서 여전히 중요한 비교 축이 된다.

### 20. Searching for MobileNetV3
- 인용 포인트: NAS와 경량 설계를 결합한 실용적 backbone으로, small-kernel 중심의 모바일 설계를 대표한다.
- 본 논문 연결: MobileNetV3-L을 본 실험 backbone으로 채택한 이유와, small ERF 경향을 가진 실용 backbone에서 ERF 조정 효과를 비교하려는 목적을 설명한다.
- 권장 위치: Methods
- 예시 문장: MobileNetV3 [20]는 실용적 경량 backbone을 대표하므로, 본 연구에서는 이러한 small-kernel backbone에서 ERF 조정이 실제로 얼마나 유의미한지를 평가하였다.

### 21. EfficientNet
- 인용 포인트: CNN scaling을 depth/width/resolution의 compound scaling으로 정리한 대표 backbone이다.
- 본 논문 연결: 해무 분류 성능은 단순 스케일링만으로 해결되지 않고 receptive field 설계 자체도 중요하다는 논리를 제시할 때 사용한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: EfficientNet [21]은 model scaling의 중요성을 보여주지만, 본 연구 결과는 해무 분류에서 성능 향상이 단순 scaling뿐 아니라 receptive field design에도 크게 의존함을 시사한다.

### 22. EfficientNetV2
- 인용 포인트: 효율적인 학습과 실용 성능을 강조한 modern CNN backbone이다.
- 본 논문 연결: EfficientNetV2-M을 비교 backbone으로 둔 이유와, 작은 기본 kernel에서도 ERF redesign이 얼마나 이득을 주는지 평가한다는 실험 목적을 설명한다.
- 권장 위치: Methods
- 예시 문장: EfficientNetV2 [22]는 학습 효율과 성능을 모두 고려한 backbone이기 때문에, 본 연구에서는 기본 kernel은 작더라도 ERF redesign이 추가 이득을 주는지 검증하는 데 사용하였다.

### 23. Deep Residual Learning for Image Recognition
- 인용 포인트: residual connection은 더 깊은 네트워크 학습을 가능하게 하지만, depth 증가가 곧바로 적절한 ERF 활용을 보장하지는 않는다.
- 본 논문 연결: 네트워크를 깊게 만드는 접근과 ERF를 직접 조정하는 접근은 다른 문제라는 점을 구분하는 데 사용한다.
- 권장 위치: Related Work
- 예시 문장: ResNet [23]이 깊은 네트워크 최적화를 가능하게 했더라도, depth 자체가 적절한 scene-level context utilization을 보장하는 것은 아니므로 ERF 설계는 별도의 문제로 다루어져야 한다.

### 24. Swin Transformer
- 인용 포인트: hierarchical vision transformer는 local window와 shifted window를 통해 장면 구조를 점진적으로 통합한다.
- 본 논문 연결: CNN의 ERF 재설계가 transformer 수준의 broader context 활용에 얼마나 근접할 수 있는지 비교하는 baseline의 의미를 설명한다.
- 권장 위치: Methods, Discussion
- 예시 문장: Swin Transformer [24]는 hierarchical context integration의 강한 baseline을 제공하므로, 본 연구는 ERF-augmented CNN이 이러한 broader context modeling에 어느 정도 근접할 수 있는지 비교하였다.

### 25. An Image is Worth 16x16 Words
- 인용 포인트: ViT는 전역적 token interaction을 통해 long-range context를 직접 다룬다.
- 본 논문 연결: 본 연구가 ViT 전체로 가지 않고 CNN 내부 ERF redesign을 택한 이유, 즉 데이터 규모와 도메인 제약 하에서 CNN을 더 잘 설계하는 실용 노선을 설명할 때 사용한다.
- 권장 위치: Related Work
- 예시 문장: ViT [25]가 global context modeling의 장점을 보여주었지만, 본 연구는 제한된 도메인 데이터 환경에서 CNN backbone 내부의 ERF redesign이 더 실용적인 대안이라고 판단하였다.

### 26. ImageNet Classification with Deep CNNs
- 인용 포인트: 현대 이미지 분류 CNN 계열의 출발점으로, convolutional hierarchical feature learning의 고전적 기반이다.
- 본 논문 연결: 역사적 배경 또는 CNN 기반 시정/해무 분류 논의의 장기 흐름을 짧게 정리할 때 사용한다.
- 권장 위치: Introduction, Related Work
- 예시 문장: AlexNet [26] 이후 CNN 기반 hierarchical feature learning은 시각 분류의 표준이 되었고, 해무 분류 역시 이러한 계보 위에서 발전해 왔다.

---

## 카테고리 4. Attention Mechanism

### 27. CBAM
- 인용 포인트: channel attention과 spatial attention을 결합해 특징 강조를 유도한다.
- 본 논문 연결: attention 모듈은 feature reweighting에는 도움을 주지만, local shortcut 자체를 구조적으로 완화하는 것은 ERF 설계와 다른 문제라는 대비 논리로 사용한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: CBAM [27]과 같은 attention module은 중요한 특징을 재강조할 수 있지만, 본 연구는 local shortcut 완화에는 feature reweighting보다 receptive field redesign이 더 직접적이라고 본다.

### 28. Squeeze-and-Excitation Networks
- 인용 포인트: channel-wise recalibration을 통해 중요한 특징 채널을 강조한다.
- 본 논문 연결: 본 연구가 attention보다 receptive field redesign에 더 초점을 둔 이유를 설명할 때 보조 비교로 사용한다.
- 권장 위치: Related Work
- 예시 문장: SE-Net [28]은 channel recalibration의 효과를 보여주지만, 본 연구는 해무 분류에서 문제가 되는 것이 channel importance보다 spatial cue range라고 보아 ERF 설계에 초점을 맞추었다.

### 29. ECA-Net
- 인용 포인트: 적은 비용으로 채널 attention을 적용하는 효율적 설계다.
- 본 논문 연결: 경량 backbone에서 attention 추가와 ERF redesign 사이의 비용-효과 차이를 논의할 때 사용할 수 있다.
- 권장 위치: Related Work, Discussion
- 예시 문장: ECA-Net [29]은 저비용 attention 설계를 제시하지만, 본 연구는 경량 backbone에서조차 ERF redesign이 더 구조적인 개선을 제공할 수 있는지에 주목한다.

### 30. BAM
- 인용 포인트: bottleneck 수준에서 attention을 삽입해 공간/채널 정보를 조절한다.
- 본 논문 연결: receptive field를 키우지 않고도 feature importance를 재배분하는 대안 계열로 언급한다.
- 권장 위치: Related Work
- 예시 문장: BAM [30]과 같은 bottleneck attention은 feature emphasis를 조절하는 대안이지만, 본 연구는 spatial context 자체를 넓히는 접근과 구분해 논의한다.

### 31. Attention Is All You Need
- 인용 포인트: attention은 멀리 떨어진 의존성을 직접 모델링할 수 있는 일반 원리다.
- 본 논문 연결: 해무 판별에 필요한 scene-level visibility cue는 본질적으로 broader context 문제이며, 우리의 large-kernel CNN 설계는 이를 convolutional 방식으로 근사하려는 시도라고 연결한다.
- 권장 위치: Introduction, Related Work
- 예시 문장: Vaswani et al. [31]가 보여준 long-range dependency modeling의 중요성은, 해무 판별 역시 broader scene context를 필요로 한다는 본 연구의 문제의식과 맞닿아 있다.

### 32. Deformable Convolutional Networks
- 인용 포인트: sampling 위치를 적응적으로 바꾸어 receptive field를 유연하게 조정한다.
- 본 논문 연결: future work에서 고정 large kernel 대신 adaptive receptive field를 도입할 수 있다는 대안적 방향으로 언급한다.
- 권장 위치: Discussion
- 예시 문장: Deformable convolution [32]은 receptive field를 적응적으로 조절하는 대표적 예로, 향후 해무 분류에서도 fixed kernel enlargement를 넘어 adaptive ERF 설계로 확장될 수 있다.

---

## 카테고리 5. 해무 탐지 - CCTV 기반

### 33. Automatic Sea Fog Detection and Estimation of Visibility on CCTV
- 인용 포인트: CCTV 기반 해무/시정 추정이 실용적으로 중요하며, 영상 기반 관측의 가능성을 조기에 제시한 연구다.
- 본 논문 연결: 항만 CCTV 기반 해무 분류라는 문제 설정 자체의 타당성과 현장 활용성을 설명하는 선행 연구로 사용한다.
- 권장 위치: Introduction
- 예시 문장: 초기 CCTV 기반 sea fog detection 연구 [33]는 영상 기반 시정 판단의 실용 가능성을 제시하였으며, 이는 본 연구의 문제 설정을 직접적으로 뒷받침한다.

### 34. Sea Fog Dissipation Prediction in Incheon Port and Haeundae Beach
- 인용 포인트: 한국 항만/해안 환경에서 해무 예측이 실제 운영 문제이며, 지역별 특성과 데이터 불균형이 크다.
- 본 논문 연결: 같은 연구 맥락과 데이터 출처 계열을 공유하는 자기 인용으로, 본 연구가 예측 문제가 아니라 시각 분류/ERF 설계 문제로 확장된 후속 연구임을 설명한다.
- 권장 위치: Introduction, Related Work
- 예시 문장: Han et al. [34]은 한국 항만 환경에서 sea fog prediction의 운영적 중요성과 지역별 차이를 보여주었으며, 본 연구는 이를 영상 분류와 ERF 설계 문제로 확장한다.

### 35. Spatio-Temporal Network for Sea Fog Forecasting
- 인용 포인트: Daesan port CCTV 기반 해무 forecasting에서는 시간 정보가 중요하며, 단일 프레임보다 spatio-temporal modeling이 유리할 수 있다.
- 본 논문 연결: 기존 CCTV 해무 연구가 temporal forecasting 쪽으로 확장되고 있지만, spatial backbone의 receptive field 문제 자체는 충분히 분석되지 않았다고 지적한다.
- 권장 위치: Related Work
- 예시 문장: Park et al. [35]은 CCTV sea fog forecasting에서 temporal modeling의 중요성을 보였지만, spatial backbone 내부의 receptive field design 문제는 여전히 충분히 다뤄지지 않았다.

### 36. RDCP: A Real Time Sea Fog Intensity and Visibility Estimation Algorithm
- 인용 포인트: 해무 강도와 시정 추정을 실시간으로 수행하려는 응용 지향 연구다.
- 본 논문 연결: 본 연구의 분류 모델도 궁극적으로는 실시간 항만 안전 운영과 연결된다는 응용 맥락을 강화하는 데 사용한다.
- 권장 위치: Introduction, Discussion
- 예시 문장: RDCP [36]와 같은 실시간 visibility estimation 연구는 해무 인식 모델이 항만 안전 운영에 직접 연결될 수 있음을 보여주며, 본 연구의 응용 가치도 같은 맥락에 있다.

### 37. Enhancement of Marine Lantern's Visibility under High Haze Using AI Camera
- 인용 포인트: 저시정/해무 환경은 항만 표지 식별과 안전에 직접 영향을 준다.
- 본 논문 연결: 해무 분류가 단순 academic task가 아니라 실제 시정 저하 대응 체계의 upstream component라는 운영적 중요성을 설명한다.
- 권장 위치: Introduction
- 예시 문장: 해무와 저시정이 해상 표지 식별에 직접적인 영향을 준다는 연구 [37]는, 본 연구의 sea fog classification이 실제 안전 지원 시스템의 선행 단계가 될 수 있음을 보여준다.

### 38. Sea Fog Detection Based on Unsupervised Domain Adaptation
- 인용 포인트: sea fog annotation은 어렵고, 도메인 차이 때문에 일반화 문제가 심각하며, 이를 해결하기 위한 domain adaptation이 필요하다.
- 본 논문 연결: 항만별 CCTV 시점 차이, 군산항 카메라 위치 변경으로 인한 domain gap, lowvis/seafog 경계 라벨 불안정 문제를 설명하는 강한 선행 근거로 사용한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: Xu et al. [38]은 sea fog detection에서 annotation difficulty와 domain gap 문제를 지적하였으며, 이는 본 연구에서 항만별 카메라 차이와 라벨 불안정성을 중시한 이유와 직결된다.

### 39. Sea Fog Identification from GOCI Images Using CNN Transfer Learning
- 인용 포인트: 해무 탐지에서도 transfer learning 기반 CNN 접근이 유효하다.
- 본 논문 연결: 본 연구의 ImageNet-100 pretrain 및 finetune 전략이 해무 도메인에서도 자연스러운 선택이라는 점을 보강한다.
- 권장 위치: Related Work, Methods
- 예시 문장: GOCI 기반 해무 식별 연구 [39]가 transfer learning의 유효성을 보여준 바와 같이, 본 연구의 사전학습 후 미세조정 전략도 해무 도메인에서 합리적인 선택이다.

### 40. Multi-Satellite Image Matching for Daytime Sea Fog Detection
- 인용 포인트: 해무 레이블 구축에는 멀티소스 정합과 정교한 ground truth 생성 과정이 중요하다.
- 본 논문 연결: lowvis/seafog 경계 샘플 정리와 uncertain 분리 같은 label audit이 왜 중요한지 설명하는 데이터 품질 근거로 사용한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: Multi-source matching 기반 sea fog labeling 연구 [40]는 ground truth 품질의 중요성을 보여주며, 본 연구의 label audit과 uncertain sample 제외 전략도 같은 문제의식에서 출발한다.

### 41. A scSE-LinkNet Deep Learning Model for Daytime Sea Fog Detection
- 인용 포인트: attention-enhanced segmentation 모델이 daytime sea fog detection 성능 향상에 기여할 수 있다.
- 본 논문 연결: 기존 해무 연구가 segmentation/attention 위주였으며, 본 연구는 classification setting에서 receptive field 설계를 직접 문제화했다는 차별점으로 연결한다.
- 권장 위치: Related Work
- 예시 문장: scSE-LinkNet [41]과 같은 attention-enhanced segmentation 연구와 달리, 본 연구는 image-level sea fog classification에서 receptive field design 자체를 핵심 변수로 다룬다.

### 42. Daytime Sea Fog Identification Based on ECA-TransUnet
- 인용 포인트: transformer 또는 hybrid attention 구조가 해무 탐지에 적용되고 있으며, broader context 활용이 중요하다.
- 본 논문 연결: 해무 탐지에서 장면 수준 문맥이 중요하다는 점은 이미 여러 구조에서 암시되어 있으며, 본 연구는 이를 ERF 관점으로 재해석한다고 연결한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: ECA-TransUnet 기반 해무 식별 연구 [42]는 broader context utilization의 중요성을 보여주며, 본 연구는 이를 CNN의 ERF 관점에서 다시 해석한다.

---

## 카테고리 6. 해무 예측 및 분석

### 43. Short-Term Sea Fog Area Forecast: A New Dataset and Deep Learning Approach
- 인용 포인트: 해무 예측 연구에서도 데이터셋 구축과 딥러닝 기반 nowcasting이 중요한 축으로 부상하고 있다.
- 본 논문 연결: 본 연구가 분류 문제에 집중하지만, 향후 time-aware ERF 설계나 예측 모델로 확장될 수 있다는 broader literature 맥락을 제공한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: 최근 sea fog nowcasting 연구 [43]는 데이터셋 구축과 딥러닝 예측의 중요성을 보여주며, 본 연구 역시 향후 time-aware classification 또는 forecasting으로 확장될 수 있다.

### 44. FogNet: Multiscale 3D CNN with Double-Branch Dense Block
- 인용 포인트: fog 예측에서 multi-scale, multi-branch 구조가 유효하며, 단일 스케일보다 다양한 수용 범위가 필요하다.
- 본 논문 연결: Type B branch-style depthwise convolution의 설계 철학과 유사하게, 여러 receptive field를 병렬적으로 활용하는 것이 도움이 된다는 배경으로 사용한다.
- 권장 위치: Related Work, Methods
- 예시 문장: FogNet [44]이 multi-branch and multi-scale design의 효과를 보인 것처럼, 본 연구의 Type B 구조도 복수의 spatial range를 병렬적으로 활용한다는 점에서 유사한 철학을 가진다.

### 45. Correlation Context-Driven Method for Sea Fog Detection
- 인용 포인트: sea fog detection은 주변 문맥과 상관 구조를 함께 봐야 하며, 국소 텍스처만으로는 한계가 있다.
- 본 논문 연결: 우리 핵심 주장인 "scene-level structural visibility cue가 필요하다"는 논리를 가장 자연스럽게 지지하는 도메인 논문으로 사용한다.
- 권장 위치: Introduction, Related Work
- 예시 문장: Huang et al. [45]은 sea fog detection에서 correlation context의 중요성을 강조하였으며, 이는 본 연구가 scene-level structural visibility cue를 중시하는 이유를 직접 뒷받침한다.

### 46. Machine Learning Analysis and Nowcasting of Marine Fog Visibility
- 인용 포인트: marine fog visibility는 예측과 분석 모두에서 중요한 연속적 대상이며, 다양한 환경 변수와 함께 다뤄진다.
- 본 논문 연결: 우리 3-class 분류가 실제로는 visibility continuum를 근사하는 문제라는 점을 설명할 때 사용한다.
- 권장 위치: Introduction, Discussion
- 예시 문장: Marine fog visibility를 연속적 현상으로 다룬 연구 [46]를 고려하면, 본 연구의 normal-lowvis-seafog 분류 역시 본질적으로 visibility continuum를 근사하는 문제로 볼 수 있다.

### 47. Data-to-Data Translation-Based Nowcasting of Specific Sea Fog
- 인용 포인트: 해무는 정적 객체가 아니라 시간에 따라 변하는 현상이며, 생성/변환 기반 접근도 가능하다.
- 본 논문 연결: 본 연구는 single-frame classification에 집중하지만, 향후 영상 기반 변화 모델로 확장될 수 있다는 서사에 사용한다.
- 권장 위치: Discussion
- 예시 문장: Data-to-data translation 기반 nowcasting 연구 [47]는 sea fog를 시간적으로 변화하는 현상으로 다루며, 이는 본 연구가 향후 temporal modeling으로 확장될 수 있음을 시사한다.

### 48. Sea Fog Detection Using Transformer-Based Deep Learning
- 인용 포인트: transformer 기반 구조는 해무 탐지에서도 유효하며, 전역 문맥 활용이 핵심이다.
- 본 논문 연결: global context가 중요한 문제라는 점은 transformer 기반 성과와도 일치하며, 우리는 이를 CNN 내부 ERF 설계로 달성하려 했다고 연결한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: Transformer-based sea fog detection 연구 [48]의 성과는 global context의 중요성을 다시 보여주며, 본 연구는 이를 CNN의 ERF redesign을 통해 달성하고자 했다.

---

## 카테고리 7. 시정 및 날씨 분류

### 49. Deep Learning Models for Visibility Forecasting Using Climatological Data
- 인용 포인트: visibility forecasting은 독립적인 연구 분야이며, 시정 예측 자체가 중요한 운영 과제다.
- 본 논문 연결: 해무 분류를 visibility-aware classification 문제로 프레이밍할 때 사용한다.
- 권장 위치: Introduction
- 예시 문장: Visibility forecasting literature [49]는 시정 자체가 중요한 예측 대상임을 보여주며, 본 연구의 sea fog classification 역시 visibility-aware recognition problem으로 이해될 수 있다.

### 50. Visibility Estimation Based on Weakly Supervised Learning
- 인용 포인트: visibility label은 획득 비용이 높아 weak supervision이 유용할 수 있다.
- 본 논문 연결: lowvis/seafog 경계가 애매한 상황에서 향후 weak label 또는 uncertain-aware learning을 고려할 수 있다는 점을 뒷받침한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: Weakly supervised visibility estimation 연구 [50]는 고품질 라벨 확보의 어려움을 보여주며, 이는 본 연구에서 ambiguous lowvis/seafog samples를 uncertain으로 분리한 판단과 연결된다.

### 51. Highway Visibility Estimation in Foggy Weather via Multi-Scale Fusion
- 인용 포인트: foggy visibility estimation에는 multi-scale feature fusion이 중요하다.
- 본 논문 연결: local edge와 global visibility structure를 함께 봐야 한다는 우리 주장과 잘 맞으며, Type B 구조의 정당화에 사용한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: Multi-scale visibility estimation 연구 [51]가 보여주듯이 foggy scenes에서는 단일 scale보다 복수 scale의 정보 결합이 중요하며, 이는 본 연구의 Type B 설계와도 부합한다.

### 52. Multi Visual Feature Fusion Based Fog Visibility Estimation
- 인용 포인트: 시정 추정은 단일 특징보다 다중 시각 특징 융합이 유리하다.
- 본 논문 연결: ERF 확장이 단순한 문맥 확대가 아니라 다양한 spatial cue를 함께 읽게 만드는 효과라고 해석할 수 있다.
- 권장 위치: Related Work
- 예시 문장: Fog visibility estimation에서 multi-feature fusion의 효과를 보인 연구 [52]는, 본 연구의 ERF expansion 또한 다양한 spatial cue를 함께 읽도록 만드는 역할을 할 수 있음을 시사한다.

### 53. CNN-Enabled Visibility Enhancement Framework for Vessel Detection under Haze
- 인용 포인트: haze/visibility degradation은 downstream marine vision task 성능에도 직접 영향을 준다.
- 본 논문 연결: 해무 분류는 자체 목적뿐 아니라 이후 선박 감시/탐지 성능 안정화의 전처리 또는 상태 인식 모듈이라는 응용적 의미를 강조할 때 사용한다.
- 권장 위치: Introduction, Discussion
- 예시 문장: Haze-aware vessel detection 연구 [53]는 visibility degradation이 downstream marine vision task에 직접 영향을 준다는 점을 보여주며, 이는 sea fog classification의 응용 가치를 강화한다.

### 54. Deep Multi-Head Regression Network for Pixel-Wise Visibility
- 인용 포인트: visibility는 장면 전체 하나의 수치가 아니라 공간적으로 변하는 field처럼 다룰 수 있다.
- 본 논문 연결: 해무 판별에서도 중요한 것은 장면 내 특정 object edge 하나보다 공간적으로 퍼진 visibility degradation 패턴이라는 해석으로 연결한다.
- 권장 위치: Discussion
- 예시 문장: Pixel-wise visibility estimation 연구 [54]는 visibility가 spatial field의 성격을 가진다는 점을 보여주며, 이는 본 연구가 local object edge보다 distributed degradation pattern에 주목한 이유와 맞닿아 있다.

### 55. Semantic Foggy Scene Understanding with Synthetic Data
- 인용 포인트: foggy scene understanding에서는 synthetic data와 domain gap 문제가 중요하다.
- 본 논문 연결: 데이터 증강, synthetic fog 생성, domain generalization을 future work로 제시할 때 활용한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: Synthetic fog data를 활용한 scene understanding 연구 [55]는 domain generalization의 가능성을 보여주며, 이는 향후 본 연구의 데이터 확장 전략으로 연결될 수 있다.

### 56. Surveillance Camera-Based Deep Learning Framework for Precipitation Type
- 인용 포인트: surveillance camera 기반 기상 현상 분류는 카메라 고유 노이즈, 조명, 시점 변화에 크게 영향을 받는다.
- 본 논문 연결: CCTV 영상 기반 해무 분류도 동일하게 camera-specific artifact와 domain bias의 영향을 받는다고 설명한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: Surveillance camera 기반 기상 분류 연구 [56]가 보여주듯이 카메라별 시점과 노이즈 차이는 모델 일반화에 큰 영향을 주며, 본 연구의 항만별 domain bias 문제도 같은 범주에 속한다.

---

## 카테고리 8. Transfer Learning 및 학습 전략

### 57. ImageNet Large Scale Visual Recognition Challenge
- 인용 포인트: ImageNet pretraining은 시각 backbone 초기화의 표준이며, 범용 feature 학습 기반이다.
- 본 논문 연결: 해무 데이터가 상대적으로 작기 때문에 ImageNet 계열 사전학습을 활용한 것은 자연스럽고 필요한 선택이라고 설명한다.
- 권장 위치: Methods
- 예시 문장: ImageNet pretraining [57]은 범용 시각 특징 학습의 표준이므로, 제한된 해무 데이터셋에서 이를 초기화 기반으로 활용한 것은 자연스러운 선택이다.

### 58. ImageNet Pre-Training and Two-Step Transfer Learning
- 인용 포인트: 두 단계 전이학습은 특수 도메인 적응을 안정화하는 유효한 학습 전략이 될 수 있다.
- 본 논문 연결: ImageNet-100 pretraining 후 해무 데이터셋 finetune이라는 본 연구의 2-stage 학습 전략을 직접 정당화하는 근거로 사용한다.
- 권장 위치: Methods
- 예시 문장: Two-step transfer learning 연구 [58]를 참고하여, 본 연구는 generic visual pretraining 이후 sea fog domain finetuning을 수행하는 2-stage 학습 전략을 채택하였다.

### 59. How Transferable Are Features in Deep Neural Networks?
- 인용 포인트: 전이 가능한 특징은 layer에 따라 다르며, 상위 특징일수록 task specificity가 커진다.
- 본 논문 연결: backbone 하위/상위 계층의 receptive field 변화가 해무 도메인 적응에 다르게 작동할 수 있다는 해석으로 연결한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: Yosinski et al. [59]이 보여준 layer-wise transferability 차이를 고려하면, ERF redesign의 효과 역시 backbone의 어느 계층에서 작동하느냐에 따라 달라질 수 있다.

### 60. Decoupled Weight Decay Regularization (AdamW)
- 인용 포인트: AdamW는 weight decay를 decouple하여 일반화 성능과 최적화 안정성을 개선한다.
- 본 논문 연결: 본 실험의 optimizer 선택을 설명하는 표준 근거로 사용한다.
- 권장 위치: Methods
- 예시 문장: 우리는 일반화와 최적화 안정성을 고려해 AdamW [60]를 사용하였으며, 이는 recent vision finetuning practice와도 일치한다.

---

## 카테고리 9. 객체 탐지 및 응용 CV

### 61. Focal Loss for Dense Object Detection
- 인용 포인트: 클래스 불균형 상황에서는 어려운 샘플에 더 집중하는 손실 설계가 중요하다.
- 본 논문 연결: lowvis/seafog 경계 샘플이나 소수 클래스 성능 문제를 논의할 때, 향후 imbalance-aware loss를 고려할 수 있다는 근거로 사용한다.
- 권장 위치: Discussion
- 예시 문장: Focal Loss [61]가 class imbalance에 효과적이었던 것처럼, 향후 본 연구에서도 lowvis/seafog confusion 완화를 위해 imbalance-aware objective를 도입할 수 있다.

### 62. YOLOv3: An Incremental Improvement
- 인용 포인트: 실시간 시각 인지 모델 설계에서는 정확도와 속도의 균형이 중요하다.
- 본 논문 연결: 해무 분류 모델도 항만 운영 환경에서는 eventually real-time deployment를 염두에 둔다는 점을 말할 때 보조적으로 사용한다.
- 권장 위치: Discussion
- 예시 문장: YOLOv3 [62]가 보여준 것처럼 실용 비전 시스템에서는 정확도와 속도의 균형이 중요하며, sea fog classification 역시 향후 실시간 운용을 고려해야 한다.

### 63. YOLOv8
- 인용 포인트: 최근 실용 CV 시스템은 효율적 backbone과 경량 추론을 중시한다.
- 본 논문 연결: 본인 선행 연구 및 실용 지향 모델링 경험과 현재 해무 분류 연구를 연결하는 bridging citation으로 활용할 수 있다.
- 권장 위치: Related Work
- 예시 문장: 최근 실용 CV 시스템에서 활용되는 YOLOv8 [63]의 경향처럼, 본 연구 역시 정확도뿐 아니라 항만 환경에서의 실용적 추론 가능성을 함께 고려한다.

### 64. Enhanced YOLOv8 Based Drone Detection for Improving Operational Stability
- 인용 포인트: 저자의 기존 연구는 운영 안정성을 위한 실용형 비전 모델 개선 경험을 보여준다.
- 본 논문 연결: 본 연구도 항만 CCTV 환경에서 운영 안정성을 높이기 위한 시정 상태 인식이라는 연속선상에 있다고 자기 인용한다.
- 권장 위치: Introduction, Related Work
- 예시 문장: 우리의 이전 연구 [64]가 운영 안정성을 위한 vision model refinement에 초점을 두었던 것처럼, 본 연구 역시 항만 환경의 operational stability 향상을 목표로 한다.

### 65. Feature Pyramid Networks for Object Detection
- 인용 포인트: 다양한 스케일의 특징을 함께 다루는 것이 시각 인식에서 중요하다.
- 본 논문 연결: 해무 판별도 단일 scale보다 local edge와 broader scene cue를 함께 봐야 한다는 점을 설명할 때 사용한다.
- 권장 위치: Related Work, Discussion
- 예시 문장: FPN [65]이 multi-scale representation의 중요성을 보여준 것처럼, 해무 분류에서도 local edge와 broader scene structure를 함께 다루는 것이 중요하다.

---

## 카테고리 10. 평가 지표 및 검증

### 66. A Systematic Analysis of Performance Measures for Classification
- 인용 포인트: accuracy만으로는 불균형 분류 문제를 충분히 설명할 수 없으며, precision/recall/F1의 역할이 다르다.
- 본 논문 연결: 본 연구에서 macro-F1과 class-wise recall을 주요 지표로 사용하는 이유를 설명하는 핵심 근거로 사용한다.
- 권장 위치: Methods
- 예시 문장: Sokolova and Lapalme [66]의 논의에 따라, 우리는 class imbalance와 class-wise error를 함께 반영하기 위해 overall accuracy보다 macro-F1과 recall을 중점적으로 보고하였다.

### 67. Evaluation: From Precision, Recall and F-Measure to ROC, Informedness, Markedness and Correlation
- 인용 포인트: 분류 평가에서는 다양한 지표가 서로 다른 오류 특성을 반영하므로, 단일 수치보다 다면 평가가 중요하다.
- 본 논문 연결: 특히 seafog recall과 lowvis/seafog confusion을 별도로 보는 이유를 설명하는 보조 지표 근거로 사용한다.
- 권장 위치: Methods
- 예시 문장: Powers [67]가 지적했듯이 단일 지표만으로는 오류 특성을 충분히 설명할 수 없기 때문에, 본 연구는 seafog recall과 lowvis/seafog confusion을 별도로 분석하였다.

### 68. Meteorological Grade Standards for Fog and Haze
- 인용 포인트: fog/haze/visibility의 기상학적 등급과 판단 기준은 제도적으로 정의되어 있다.
- 본 논문 연결: normal, lowvis, seafog의 운영상 구분과 라벨 설계 원칙을 설명할 때 인용한다.
- 권장 위치: Introduction, Dataset/Methods
- 예시 문장: 본 연구의 시정 등급 구분은 KMA 기준 [68]에서 제시하는 fog and haze visibility standard를 참고하여 설계하였다.

### 69. ICAO Visibility Definitions
- 인용 포인트: visibility는 안전 운항 관점에서 표준화된 operational definition을 가진다.
- 본 논문 연결: 해무 분류가 단순 시각적 미학 문제가 아니라 안전 운항/항만 운영과 연결된 상태 인식이라는 점을 강조한다.
- 권장 위치: Introduction
- 예시 문장: ICAO visibility definition [69]이 보여주듯이 visibility는 운영 안전과 직결되는 표준 지표이며, sea fog classification은 이러한 operational visibility assessment와 밀접하게 연결된다.

### 70. KHOA Sea Fog Observation Data
- 인용 포인트: 본 연구 데이터는 공공 해양 관측 체계 및 해무 서비스 맥락에 기반한다.
- 본 논문 연결: 데이터 출처, 사회적 필요성, 향후 서비스 적용 가능성을 설명하는 직접 근거로 사용한다.
- 권장 위치: Dataset, Introduction
- 예시 문장: 본 연구는 KHOA sea fog observation framework [70]와 연계된 데이터를 기반으로 하며, 이는 연구 결과의 실서비스 연계 가능성을 높여준다.

---

## 바로 가져다 쓸 수 있는 문장 템플릿

### 1. ERF 핵심 주장
- Luo et al. [1] showed that the effective receptive field of deep CNNs occupies only a fraction of the theoretical receptive field and is concentrated around the center.
- Building on this view, we hypothesize that small-ERF backbones in port CCTV sea-fog classification can over-rely on localized edge-texture evidence rather than scene-level visibility structure.

### 2. Shortcut framing
- Following the shortcut learning perspective of Geirhos et al. [11], we interpret strong local edges and local degradation artifacts as potential shortcut cues that can bias sea-fog classification.
- Our Grad-CAM analysis suggests that ERF expansion reduces this shortcut tendency and encourages the model to attend to broader structural visibility cues.

### 3. Large-kernel motivation
- Recent large-kernel CNN studies, including RepLKNet [2] and ConvNeXt [3], suggest that enlarging convolutional context can improve the use of broader spatial structure.
- In our task, moderate ERF expansion was more effective than excessive kernel enlargement, indicating that the optimal receptive field is task- and backbone-dependent.

### 4. 도메인 차별성
- Prior sea-fog studies have focused on forecasting, segmentation, or domain adaptation [35, 38, 41, 42], whereas the effect of backbone ERF on shortcut behavior in port CCTV image classification remains underexplored.
- We therefore analyze sea-fog classification from the viewpoint of receptive field design and shortcut mitigation.

### 5. 데이터/평가 정당화
- Because low-visibility and sea-fog samples can form an ambiguous continuum, label quality control is critical in this task.
- We therefore report macro-F1 and class-wise recall following standard classification evaluation guidance [66, 67], rather than relying solely on overall accuracy.

---

## 우선순위가 가장 높은 필수 인용 후보

아래 논문들은 본 논문의 중심 서사와 직접 연결되므로 본문에서 빠지면 안 된다.

1. Luo et al. [1]
2. Ding et al. / RepLKNet [2]
3. Geirhos et al. [11]
4. Selvaraju et al. [13]
5. Xception [17]
6. MobileNetV3 [20]
7. EfficientNetV2 [22]
8. Sea Fog Dissipation Prediction in Incheon Port and Haeundae Beach [34]
9. Sea Fog Detection Based on Unsupervised Domain Adaptation [38]
10. Sokolova and Lapalme [66]

---

## 추천 사용법

- Introduction에는 11, 1, 2, 33, 34, 38, 68, 69, 70을 우선 배치
- Related Work에는 3, 4, 5, 10, 13, 17, 20, 22, 24, 27, 38, 41, 42, 45, 48을 중심으로 배치
- Methods에는 13, 17, 20, 22, 24, 57, 58, 60, 66, 67을 배치
- Discussion에는 2, 4, 5, 8, 9, 12, 27, 32, 40, 45, 50, 51, 55, 61을 배치
