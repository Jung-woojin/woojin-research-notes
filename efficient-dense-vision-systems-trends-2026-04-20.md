# 2026 Efficient Dense Vision Systems 트렌드 리포트

기준일: 2026-04-20

## 조사 목적

이 문서는 detection, segmentation, tracking처럼 `dense prediction` 성격이 강한 비전 시스템에서 왜 여전히 `정확도만이 아니라 시스템 효율`이 핵심인지 정리한 리포트다.

다루는 범위는 다음과 같다.

- object detection
- instance / semantic / panoptic segmentation
- multi-scale feature design
- decoder efficiency
- convergence speed
- real-time deployment
- edge/mobile dense vision
- promptable segmentation 모델의 시스템 함의

핵심 질문은 간단하다.

- 왜 dense vision에서는 아직도 multi-scale과 locality가 중요한가
- 왜 FLOPs만 보고 효율을 말하면 자주 틀리는가
- 왜 end-to-end 모델이 강해져도 현실 시스템에서는 backbone, neck, decoder, memory를 따로 봐야 하는가

## 빠른 결론

1. `dense vision의 병목은 여전히 multi-scale representation과 localization 품질이다.`
2. `DETR 계열이 판을 바꿨지만, 그 성공 과정 역시 locality, multi-scale, query design, matching 안정성 같은 구조적 보강의 역사였다.`
3. `실제 효율은 FLOPs보다 memory bandwidth, feature resolution, decoder cost, training convergence에 더 크게 좌우되는 경우가 많다.`
4. `segmentation foundation model이 강해져도, 실전에서는 encoder 경량화와 memory system 설계가 다시 핵심이 된다.`
5. `dense vision 연구는 이제 모델 아키텍처만이 아니라 시스템 전체를 설계하는 문제다.`

---

## 1. 왜 dense vision은 classification과 다른가

classification은 대체로 이미지 하나를 전역 의미 벡터로 요약해도 된다. 하지만 dense vision은 다르다.

- 어디에 있는가
- 경계가 어디인가
- 작은 물체가 몇 개인가
- 서로 겹친 물체를 어떻게 나누는가
- 시간이 흐를 때 identity를 어떻게 유지하는가

즉 dense task는 `고해상도 공간 정보`와 `전역 문맥`을 동시에 요구한다. 이 때문에 다음 문제가 항상 다시 등장한다.

- multi-scale feature fusion
- localization vs classification trade-off
- decoder complexity
- latency vs resolution trade-off

## 2. FPN 이후에도 multi-scale이 계속 남는 이유

`Feature Pyramid Networks`는 ConvNet의 피라미드 계층을 활용해 semantically strong한 multi-scale feature를 `marginal extra cost`로 만들 수 있음을 보여 준 고전이다.[1]

왜 이 개념이 아직도 강한가.

- 작은 객체는 고해상도 feature가 필요하다
- 큰 객체는 넓은 receptive field가 필요하다
- 한 장면 안에 object scale 분포가 넓다

이건 단순 구현 습관이 아니라 문제 구조에 가깝다. 그래서 Transformer 시대에도 다양한 방식으로 multi-scale이 재도입되었다.

- hierarchical backbone
- deformable attention
- multi-scale decoder
- feature pyramid neck

즉 2026년 시점에도 질문은 `multi-scale이 필요한가`가 아니라 `어떤 방식의 multi-scale이 가장 값어치가 큰가`다.

## 3. EfficientDet가 남긴 교훈: 효율은 backbone 하나로 결정되지 않는다

`EfficientDet`는 efficient backbone만이 아니라 `feature fusion`과 `compound scaling`을 함께 설계해야 detection 효율이 좋아진다는 점을 매우 선명하게 보여 줬다.[2]

이 논문이 중요한 이유는 다음과 같다.

- detection 효율은 backbone만의 문제가 아니다
- BiFPN 같은 neck 설계가 성능과 비용 모두에 큰 영향을 준다
- input resolution, feature depth, head width를 따로 키우는 것보다 같이 조정하는 편이 낫다

즉 효율을 보려면 최소한 아래 네 층을 분리해서 봐야 한다.

- backbone
- multi-scale fusion neck
- prediction head / decoder
- input resolution policy

## 4. DETR이 바꾼 것: 수작업 detection pipeline의 대체 가능성

`DETR`는 object detection을 set prediction으로 재정식화해 anchor generation과 NMS 같은 hand-crafted component를 대폭 줄였다.[3]

이건 개념적으로 매우 큰 전환이었다.

- detection을 bipartite matching 문제로 본다
- end-to-end optimization을 밀어 붙인다
- region proposal 중심 사고를 약화시킨다

하지만 동시에 큰 약점도 드러났다.

- 수렴이 느리다
- 작은 객체에 약하다
- 고해상도 feature 처리에 불리하다

즉 DETR은 새 패러다임은 맞았지만, dense vision의 오래된 현실 제약을 완전히 없애지는 못했다.

## 5. Deformable DETR 이후의 메시지: dense vision은 locality를 다시 부른다

`Deformable DETR`는 attention이 소수의 key sampling point에만 집중하도록 하면서, 특히 small object와 convergence 문제를 크게 개선했다. 논문은 DETR보다 `10배 적은 training epochs`로 더 나은 성능을 낼 수 있다고 보고했다.[4]

이 변화의 의미는 매우 크다.

- global attention만으로는 dense prediction이 비효율적일 수 있다
- sparse/local sampling은 단순 근사가 아니라 문제 구조에 맞는 inductive bias일 수 있다
- end-to-end detector도 결국 locality와 multi-scale을 다시 도입하게 된다

## 6. DINO, RT-DETR, DEIM: 이제 병목은 “수렴 속도”와 “실시간성”이다

### 6-1. DINO는 DETR 계열의 training efficiency를 크게 끌어올렸다

`DINO`는 denoising training, query initialization, box prediction 개선을 통해 DETR류의 성능과 효율을 함께 끌어올렸다.[5]

핵심 메시지는 다음과 같다.

- dense vision에서 좋은 architecture만으로는 부족하다
- matching, query formulation, denoising supervision이 학습 효율에 직접 영향을 준다
- detector 성능은 추론 구조와 학습 신호 설계가 함께 결정한다

### 6-2. RT-DETR는 real-time end-to-end detector를 정면으로 겨냥했다

`DETRs Beat YOLOs on Real-time Object Detection`는 RT-DETR를 제안하며 real-time에서도 end-to-end DETR이 가능하다는 점을 밀어붙였다.[6]

이건 중요한 전환점이다.

- DETR은 느리다는 인식을 깨려 했다
- real-time detector에서도 query-based end-to-end 설계가 실용 가능함을 보였다
- accuracy와 latency를 동시에 겨냥하는 연구가 본류로 올라왔다

### 6-3. 최근 연구는 이제 convergence cost 자체를 줄이려 한다

`DEIM`은 real-time DETR 계열에서 더 빠른 convergence를 목표로 했고, RT-DETR 및 D-FINE과 결합했을 때 `training time 50% 감소`를 보고했다.[7]

이건 매우 중요한 추세다.

- 재학습 주기
- 도메인 업데이트 속도
- 실험 반복 비용
- 하이퍼파라미터 탐색 비용

즉 앞으로 efficient dense vision은 `inference efficiency`뿐 아니라 `training efficiency`까지 포함해야 한다.

## 7. segmentation에서 드러난 사실: 좋은 decoder가 생각보다 훨씬 중요하다

`Mask2Former`는 semantic, instance, panoptic segmentation을 하나의 masked-attention 기반 프레임워크로 묶으며 강한 성능을 보였다.[8]

이 논문이 던진 메시지는 명확하다.

- segmentation은 task별 head를 따로 만들지 않아도 될 수 있다
- mask representation이 bounding box보다 더 통합적인 인터페이스가 될 수 있다
- decoder 설계가 곧 task unification의 핵심이다

하지만 여기서 중요한 건 transformer가 다 이겼다가 아니다. `MaskConver`는 pure convolutional panoptic 모델을 다시 보며, decoder 설계가 충분히 강하면 convolution 기반 모델도 transformer 계열과 격차를 크게 줄이거나 특정 설정에서 앞설 수 있음을 보였다.[9]

이 결과는 dense vision 연구자에게 중요한 메시지를 준다.

- backbone 논쟁보다 decoder/context aggregation이 더 중요할 수 있다
- dense task에서 locality와 global context의 균형은 여전히 핵심이다
- 적절한 decoder가 있으면 pure conv도 여전히 강하다

## 8. promptable segmentation의 부상과 시스템적 역설

`Segment Anything`은 promptable segmentation이라는 새로운 인터페이스를 대중화했고, SA-1B라는 대규모 segmentation dataset을 공개했다.[10]

이후 `SAM 2`는 이미지뿐 아니라 비디오까지 확장하며, 이미지 segmentation에서는 SAM보다 더 정확하고 `6배 빠르다`, 비디오 segmentation에서는 더 적은 interaction으로 더 좋은 성능을 보인다고 보고했다.[11]

하지만 이 흐름은 한 가지 역설을 낳는다.

- foundation segmentation 모델은 강하다
- 그러나 그 자체는 무겁다
- 실제 현장에서는 mobile/edge/real-time 제약이 다시 등장한다

그래서 최근 연구의 초점이 곧바로 아래로 이동했다.

- encoder 경량화
- memory module 경량화
- prompt 처리 비용 절감
- video streaming memory 최적화

예를 들어 `EfficientViT-SAM`은 SAM 가속을 목표로 했고, SAM 2류 tracking-anything 시스템도 경량화 연구가 빠르게 붙고 있다.[12]

## 9. Dense vision 효율에서 FLOPs만 보면 자주 틀리는 이유

Dense vision은 classification보다 FLOPs와 latency의 괴리가 더 크다.

그 이유는 다음과 같다.

### 9-1. high-resolution feature map 유지 비용

작은 객체 성능을 위해 높은 spatial resolution을 유지하면 activation memory와 bandwidth 비용이 빠르게 커진다.

### 9-2. neck / decoder cost

backbone FLOPs가 작아도 neck과 decoder가 무거우면 실시간성이 쉽게 무너진다.

### 9-3. operator maturity

conv, depthwise conv, resize, fuse 연산은 하드웨어 최적화가 잘 되어 있는 반면, 일부 attention, grid sampling, dynamic operator는 배포 환경에서 비효율적일 수 있다.

### 9-4. post-processing and data movement

NMS, mask upsampling, memory copy, video buffer management 같은 요소도 실제 latency에 영향을 준다.

따라서 efficient dense vision에서는 적어도 아래를 같이 적어야 한다.

- AP / PQ / mIoU
- FPS 또는 ms/image
- resolution
- memory footprint
- training epochs
- hardware target

## 10. 앞으로 중요한 연구 질문

### 10-1. multi-scale is structural or incidental?

multi-scale hierarchy는 단지 현재 모델이 못 배워서 필요한 것인가, 아니면 dense vision 문제 자체가 본질적으로 multi-scale 구조를 요구하는가.

### 10-2. where is the real bottleneck?

정확도 개선 병목이 backbone에 있는가, 아니면 decoder/matching/training objective에 있는가. 최근 흐름은 후자 쪽에 무게가 크다.

### 10-3. can one universal dense interface win?

box, mask, point, prompt, track token 중 무엇이 dense perception의 가장 좋은 인터페이스인가.

### 10-4. what does efficiency really mean?

효율을 무엇으로 정의할 것인가.

- 추론 FPS
- 모바일 latency
- GPU memory
- annotation interaction 수
- training wall-clock

이 중 어느 것을 최적화하느냐에 따라 좋은 모델이 달라진다.

## 11. 연구 설계 제안

이 주제를 연구 프로젝트로 다루려면 다음 비교가 좋다.

- task split: COCO detection, COCO panoptic, ADE20K semantic segmentation, domain-specific dense task
- architecture split: FPN + CNN baseline, EfficientDet류 detector, DETR / Deformable DETR / DINO, RT-DETR 계열, Mask2Former / MaskConver, SAM 또는 SAM 2 기반 시스템
- efficiency split: accuracy only, latency constrained, memory constrained, training-time constrained, mobile / edge constrained
- reporting split: clean metric, small-object metric, boundary metric, convergence epochs, actual wall-clock, deployment hardware

## 12. 연구 방향 요약

앞으로의 efficient dense vision은 세 방향으로 더 갈 가능성이 크다.

1. `multi-scale와 locality를 버리는 방향`이 아니라 `더 싸게 구현하는 방향`
2. `better architecture`만이 아니라 `better matching / better supervision / faster convergence` 방향
3. `foundation segmentation model`을 실제 장치에 태우기 위한 경량화와 memory engineering 방향

즉 이 분야는 어떤 모델이 가장 정확한가보다 `어떤 시스템이 실제 조건에서 가장 덜 무너지는가`로 중심이 이동하고 있다.

---

## 최종 정리

dense vision은 아직도 구조와 시스템이 만나는 전장이다.

- DETR는 detection을 단순화했다
- Deformable DETR와 DINO는 dense reality를 다시 받아들였다
- RT-DETR는 real-time성을 정면으로 문제 삼았다
- Mask2Former와 SAM 계열은 인터페이스를 바꿨다
- 그리고 다시 decoder, memory, deployment가 핵심으로 돌아왔다

따라서 이 주제에서 가장 좋은 연구 질문은 아래와 같다.

- 무엇이 성능을 올리나
- 무엇이 실제 제약 아래에서도 유지되나

---

## 참고문헌

[1] Lin et al., *Feature Pyramid Networks for Object Detection* (CVPR 2017)  
[CVF Open Access](https://openaccess.thecvf.com/content_cvpr_2017/html/Lin_Feature_Pyramid_Networks_CVPR_2017_paper.html)

[2] Tan et al., *EfficientDet: Scalable and Efficient Object Detection* (CVPR 2020)  
[CVF Open Access](https://openaccess.thecvf.com/content_CVPR_2020/html/Tan_EfficientDet_Scalable_and_Efficient_Object_Detection_CVPR_2020_paper.html)

[3] Carion et al., *End-to-End Object Detection with Transformers* (ECCV 2020)  
[OpenReview](https://openreview.net/forum?id=ygD59QgnY4a)

[4] Zhu et al., *Deformable DETR: Deformable Transformers for End-to-End Object Detection* (ICLR 2021)  
[OpenReview PDF](https://openreview.net/pdf/758d4b5c0d63033d526ff8744d872a03543bb674.pdf)

[5] Zhang et al., *DINO: DETR with Improved DeNoising Anchor Boxes for End-to-End Object Detection* (ICLR 2023)  
[OpenReview](https://openreview.net/forum?id=3mRwyG5one)

[6] Zhao et al., *DETRs Beat YOLOs on Real-time Object Detection* (CVPR 2024)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2024/html/Zhao_DETRs_Beat_YOLOs_on_Real-time_Object_Detection_CVPR_2024_paper.html)

[7] Huang et al., *DEIM: DETR with Improved Matching for Fast Convergence* (CVPR 2025)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2025/html/Huang_DEIM_DETR_with_Improved_Matching_for_Fast_Convergence_CVPR_2025_paper.html)

[8] Cheng et al., *Masked-attention Mask Transformer for Universal Image Segmentation (Mask2Former)* (CVPR 2022)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2022/html/Cheng_Masked-Attention_Mask_Transformer_for_Universal_Image_Segmentation_CVPR_2022_paper.html)

[9] Rashwan et al., *MaskConver: Revisiting Pure Convolution Model for Panoptic Segmentation* (WACV 2024)  
[CVF Open Access](https://openaccess.thecvf.com/content/WACV2024/html/Rashwan_MaskConver_Revisiting_Pure_Convolution_Model_for_Panoptic_Segmentation_WACV_2024_paper.html)

[10] Kirillov et al., *Segment Anything* (2023)  
[arXiv](https://arxiv.org/abs/2304.02643)

[11] Ravi et al., *SAM 2: Segment Anything in Images and Videos* (ICLR 2025)  
[OpenReview](https://openreview.net/forum?id=Ha6RTeWMd0)

[12] Zhang et al., *EfficientViT-SAM: Accelerated Segment Anything Model Without Performance Loss* (CVPR 2024 workshop)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2024W/ELVM/html/Zhang_EfficientViT-SAM_Accelerated_Segment_Anything_Model_Without_Performance_Loss_CVPRW_2024_paper.html)
