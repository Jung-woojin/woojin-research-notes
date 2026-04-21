# 2026 트렌드 문서 기반 연구질문 노트

기준일: 2026-04-21

## 목적

이 문서는 지금까지 정리한 트렌드 문서들에서 `바로 논문 주제나 프로젝트로 밀어붙일 수 있는 연구 질문`만 뽑아낸 메타 노트다.

이 노트는 아래 문서들을 배경으로 한다.

- [computer-vision-trends-2026-04-20.md](/C:/Users/ust21/연구노트/computer-vision-trends-2026-04-20.md)
- [cnn-inductive-bias-revisited-trends-2026-04-20.md](/C:/Users/ust21/연구노트/cnn-inductive-bias-revisited-trends-2026-04-20.md)
- [small-data-domain-shift-robust-vision-trends-2026-04-20.md](/C:/Users/ust21/연구노트/small-data-domain-shift-robust-vision-trends-2026-04-20.md)
- [efficient-dense-vision-systems-trends-2026-04-20.md](/C:/Users/ust21/연구노트/efficient-dense-vision-systems-trends-2026-04-20.md)
- [multimodal-ai-trends-2026-04-20.md](/C:/Users/ust21/연구노트/multimodal-ai-trends-2026-04-20.md)
- [on-device-edge-ai-trends-2026-04-20.md](/C:/Users/ust21/연구노트/on-device-edge-ai-trends-2026-04-20.md)

핵심 목표는 리뷰가 아니다.  
오히려 아래에 가깝다.

- 무엇이 아직 안 풀렸는가
- 어디가 과대평가되었는가
- 실험으로 검증 가능한 가설은 무엇인가
- 연구비/시간 대비 가장 생산적인 질문은 무엇인가

## 빠른 결론

지금 시점에서 가장 값어치가 큰 질문은 대체로 아래 세 축에 모인다.

1. `inductive bias와 scale의 교환관계`
2. `realistic robustness와 deployment gap`
3. `dense / multimodal / on-device 시스템에서의 실제 효율`

즉 “새 모델 하나 더 만들기”보다 아래 질문들이 더 강하다.

- 어떤 bias가 어떤 데이터 regime에서 유효한가
- 어떤 성능 개선이 실제 shift와 latency 제약 아래에서도 유지되는가
- 어떤 표현이 여러 태스크에 공통으로 남는가

---

## 1. A급 질문: 바로 대표 주제로 밀 만한 것

아래 질문들은 실제로 논문 단위 주제가 될 만한 밀도를 가진다.

### Q1. small-data + domain shift 환경에서 CNN bias는 실제로 얼마나 남는가

왜 중요한가:
- ViT와 large-scale pretraining이 강해졌지만, 작은 데이터와 도메인 이동 환경에서는 구조적 bias가 다시 중요해질 가능성이 높다.[1][2][3]

검증 가능한 가설:
- `same pretraining budget`와 `same fine-tuning budget` 아래에서는 ConvNeXt류가 ViT류보다 더 안정적일 수 있다.

최소 실험:
- backbone: ConvNeXt-T, Swin-T, ViT-B
- 데이터: source-target domain split가 있는 의료 또는 산업 데이터
- 측정: accuracy, calibration, target drift sensitivity

성공했을 때의 확장:
- backbone-level prior와 uncertainty quality의 관계
- parameter-efficient tuning 환경에서의 비교

### Q2. dense prediction에서 multi-scale hierarchy는 학습 편의인가, 문제 구조인가

왜 중요한가:
- FPN 이후 거의 모든 강한 detector/segmenter가 어떤 형태로든 multi-scale을 다시 도입했다.[4][5][6]

검증 가능한 가설:
- 동일 compute budget에서는 pure flat representation보다 hierarchy가 small object와 boundary quality에서 일관되게 유리하다.

최소 실험:
- flat ViT backbone vs Swin vs ConvNeXt
- task: COCO detection, ADE20K segmentation
- metric: small-object AP, boundary F-score, latency

성공했을 때의 확장:
- hierarchy가 필요한 정확한 stage를 찾는 ablation

### Q3. robust vision에서 가장 부족한 것은 모델이 아니라 평가 프로토콜인가

왜 중요한가:
- WILDS와 natural shift benchmark는 synthetic corruption 개선이 현실 성능 보장으로 바로 이어지지 않음을 보여 줬다.[2][7][8]

검증 가능한 가설:
- 많은 robust method의 ranking은 shift 종류가 바뀌면 역전된다.

최소 실험:
- corruption, natural shift, temporal shift, device shift를 분리 평가
- baseline: ERM, SSL-pretrained backbone, DG method, TTA method

성공했을 때의 확장:
- 논문 단위 benchmark / protocol paper로 연결 가능

### Q4. DETR 이후의 dense vision에서 진짜 병목은 backbone이 아니라 training objective인가

왜 중요한가:
- Deformable DETR, DINO, RT-DETR, DEIM의 흐름은 dense vision 성능 향상이 attention 자체보다 convergence engineering과 matching design에서 많이 나왔음을 시사한다.[5][6][9][10]

검증 가능한 가설:
- 같은 backbone에서도 training objective와 query design이 성능 차이 대부분을 설명한다.

최소 실험:
- backbone 고정
- DETR / Deformable DETR / DINO-style training 비교
- convergence speed와 final AP 동시 측정

성공했을 때의 확장:
- backbone 논쟁을 objective design 쪽으로 재배치 가능

### Q5. on-device AI에서 좋은 비전 모델의 조건은 accuracy가 아니라 updateability인가

왜 중요한가:
- edge 환경에서는 한 번 배포하고 끝이 아니라, 경량 fine-tuning, distillation, quantization, cache policy까지 포함한 lifecycle이 중요하다.[11][12]

검증 가능한 가설:
- 조금 덜 정확해도 calibration과 update cost가 낮은 백본이 실제 운영 ROI는 더 좋다.

최소 실험:
- MobileNetV2 / EfficientNet / 소형 ConvNeXt / 모바일 ViT 계열 비교
- metric: on-device latency, memory, incremental update cost, post-quantization degradation

성공했을 때의 확장:
- “deployment-aware backbone selection” 프레임 제안 가능

---

## 2. A-/B+급 질문: 깊게 파면 논문이 되는 것

### Q6. representation quality와 calibration quality는 왜 같이 가지 않는가

근거:
- shift 상황에서 높은 정확도가 좋은 uncertainty를 보장하지 않는다.[8][13]

가설:
- backbone의 feature geometry와 logit calibration은 다른 방향으로 최적화된다.

실험 힌트:
- 동일 backbone에 supervised, SSL, multimodal pretraining을 걸고 calibration 비교

### Q7. foundation segmentation model의 성능은 어디서 실제로 깨지는가

근거:
- SAM과 SAM 2는 강력하지만, 도메인 shift, interaction budget, mobile constraint에서는 다른 문제가 생긴다.[14][15]

가설:
- foundation segmentation model의 한계는 zero-shot mask quality보다 memory and interaction efficiency에서 먼저 드러난다.

실험 힌트:
- prompt 수 제한
- memory budget 제한
- domain shift 포함

### Q8. multimodal model에서 진짜 공통 표현은 early fusion에서 생기나, late alignment에서 생기나

근거:
- 최근 모델들은 native / omni multimodality를 강조하지만, 실제로 어느 stage에서 공통 latent가 가치 있는지는 아직 불분명하다.[16][17]

가설:
- 모든 모달리티를 초반에 섞는 것보다 task-specific stage를 남기는 hybrid fusion이 더 효율적일 수 있다.

실험 힌트:
- image-text-audio 중 2~3모달 toy benchmark 설계
- early, mid, late fusion 비교

### Q9. 비전 백본에서 SSM/Mamba 계열의 장점은 이론 계산량인가, 실제 고해상도 처리인가

근거:
- Vision Mamba, VMamba, MambaVision은 효율적 long-range modeling을 주장하지만, 실제 downstream과 wall-clock 이득은 더 검증이 필요하다.[18][19][20]

가설:
- SSM backbone의 장점은 standard ImageNet보다 high-resolution dense task에서 더 크게 드러난다.

실험 힌트:
- 1024px 이상 입력
- segmentation 또는 aerial imagery

### Q10. domain adaptation은 accuracy를 올릴 때 신뢰도도 함께 올리는가

근거:
- Tent류 TTA는 유용하지만 brittle하고 calibration 개선이 자동으로 따라오지 않는다.[3][13]

가설:
- entropy-minimization 기반 TTA는 accuracy gain 대비 calibration loss를 유발할 수 있다.

실험 힌트:
- source-free TTA
- ECE, NLL, selective risk 동시 측정

---

## 3. B급 질문: 짧은 프로젝트나 탐색 연구로 좋은 것

### Q11. ConvNeXt와 Swin의 차이는 dense task에서 실제로 backbone 차이인가, neck 적합성 차이인가

### Q12. deformable convolution과 deformable attention은 어떤 조건에서 서로 대체 가능한가

### Q13. edge vision에서 quantization 친화적인 백본의 구조적 특징은 무엇인가

### Q14. synthetic data augmentation이 natural shift robustness로 이어지는 조건은 무엇인가

### Q15. multimodal encoder를 써도 domain-specific visual prior가 필요한가

### Q16. segmentation에서 mask decoder를 무겁게 하는 것이 backbone을 키우는 것보다 더 나은가

### Q17. long-context video understanding에서 frame redundancy를 backbone이 얼마나 흡수할 수 있는가

### Q18. 의료/산업 도메인에서 open-vocabulary perception은 closed-set detector보다 언제 유리한가

이 질문들은 개별적으로는 조금 작아 보이지만, 제대로 묶으면 충분히 논문급이 될 수 있다.

---

## 4. 지금 가장 추천하는 실제 프로젝트 5개

### 프로젝트 1. CNN bias under realistic shift

질문:
- 작은 데이터와 병원/장비 이동 환경에서 ConvNeXt와 Swin 중 누가 더 안정적인가

장점:
- 당신 배경과 가장 잘 맞는다
- 실험 설계가 명확하다
- 논문 스토리가 선명하다

리스크:
- 데이터 확보와 평가 protocol 설계가 중요하다

### 프로젝트 2. Hierarchy is not optional

질문:
- dense prediction에서 hierarchy 제거가 실제로 어느 지표를 가장 먼저 무너뜨리는가

장점:
- backbone 논쟁을 문제 구조 논쟁으로 바꿀 수 있다

리스크:
- 공정 비교를 위해 decoder와 training recipe를 잘 고정해야 한다

### 프로젝트 3. Calibration-aware TTA

질문:
- test-time adaptation이 accuracy gain 대비 calibration loss를 일으키는가

장점:
- robust vision 쪽에서 실용 가치가 높다

리스크:
- baseline 구현과 평가가 생각보다 번거롭다

### 프로젝트 4. Efficient foundation segmentation

질문:
- SAM 2 계열의 병목은 encoder인가 memory인가 interaction인가

장점:
- 최신성 높다
- 실용성과 연구성을 동시에 가진다

리스크:
- 실험 환경이 무거울 수 있다

### 프로젝트 5. High-resolution SSM backbone reality check

질문:
- VMamba / MambaVision이 실제 high-resolution dense task에서 wall-clock 이득을 주는가

장점:
- 최신 백본 연구와 효율 연구를 동시에 건드린다

리스크:
- 구현 성숙도 차이 때문에 공정 비교가 어려울 수 있다

---

## 5. 연구질문을 고를 때 버려야 할 함정

### 함정 1. top-1 0.x% 개선만 보는 것

이건 연구 질문이 아니라 leaderboard 대응일 때가 많다.

### 함정 2. pretraining budget이 다른 모델을 그대로 비교하는 것

이 경우 backbone 우열이 아니라 자원 우열을 비교하게 된다.

### 함정 3. synthetic corruption만 보고 robustness를 주장하는 것

natural shift와 deployment shift는 별개 문제다.[7][8]

### 함정 4. FLOPs만 보고 효율을 주장하는 것

dense vision과 edge vision에서는 latency, memory, operator maturity가 훨씬 중요할 수 있다.[4][11]

### 함정 5. “요즘 뜨는 모델”을 그대로 따라가는 것

좋은 연구 질문은 유행 모델 추종보다 `무엇이 아직 설명되지 않았는가`에서 나온다.

---

## 6. 읽기 순서

이 노트를 실제 프로젝트로 연결하려면 아래 순서가 좋다.

1. ConvNeXt, Swin, ViT  
백본 축 정리

2. WILDS, natural shift, Tent  
robustness 축 정리

3. Deformable DETR, DINO, RT-DETR, SAM 2  
dense/system 축 정리

4. Vision Mamba, VMamba, MambaVision  
차세대 backbone 축 정리

그 다음에 본인 도메인 데이터 하나를 정해 `작은 실험`으로 바로 들어가는 것이 좋다.

---

## 최종 정리

지금 트렌드 문서들에서 가장 생산적인 연구 방향은 “최신 모델 요약”이 아니다.  
오히려 아래 질문들이다.

- 어떤 bias가 실제로 남는가
- 어떤 개선이 현실 제약 아래에서도 유지되는가
- 어떤 평가지표가 진짜 현상을 포착하는가

즉 좋은 다음 문서는 또 하나의 트렌드 리포트가 아니라, `위 질문들 중 하나를 실제 실험 계획으로 바꾼 프로젝트 노트`일 가능성이 높다.

---

## 참고문헌

[1] Liu et al., *A ConvNet for the 2020s* (CVPR 2022)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2022/html/Liu_A_ConvNet_for_the_2020s_CVPR_2022_paper.html)

[2] Koh et al., *WILDS: A Benchmark of in-the-Wild Distribution Shifts* (ICML 2021)  
[PMLR](https://proceedings.mlr.press/v139/koh21a.html)

[3] Wang et al., *Tent: Fully Test-Time Adaptation by Entropy Minimization* (ICLR 2021)  
[OpenReview](https://openreview.net/forum?id=uXl3bZLkr3c)

[4] Lin et al., *Feature Pyramid Networks for Object Detection* (CVPR 2017)  
[CVF Open Access](https://openaccess.thecvf.com/content_cvpr_2017/html/Lin_Feature_Pyramid_Networks_CVPR_2017_paper.html)

[5] Zhu et al., *Deformable DETR: Deformable Transformers for End-to-End Object Detection* (ICLR 2021)  
[OpenReview PDF](https://openreview.net/pdf/758d4b5c0d63033d526ff8744d872a03543bb674.pdf)

[6] Zhang et al., *DINO: DETR with Improved DeNoising Anchor Boxes for End-to-End Object Detection* (ICLR 2023)  
[OpenReview](https://openreview.net/forum?id=3mRwyG5one)

[7] Hendrycks and Dietterich, *Benchmarking Neural Network Robustness to Common Corruptions and Perturbations* (ICLR 2019)  
[OpenReview](https://openreview.net/forum?id=HJz6tiCqYm)

[8] Taori et al., *Measuring Robustness to Natural Distribution Shifts in Image Classification* (NeurIPS 2020)  
[NeurIPS PDF](https://proceedings.neurips.cc/paper/2020/file/d8330f857a17c53d217014ee776bfd50-Paper.pdf)

[9] Zhao et al., *DETRs Beat YOLOs on Real-time Object Detection* (CVPR 2024)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2024/html/Zhao_DETRs_Beat_YOLOs_on_Real-time_Object_Detection_CVPR_2024_paper.html)

[10] Huang et al., *DEIM: DETR with Improved Matching for Fast Convergence* (CVPR 2025)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2025/html/Huang_DEIM_DETR_with_Improved_Matching_for_Fast_Convergence_CVPR_2025_paper.html)

[11] Sandler et al., *MobileNetV2: Inverted Residuals and Linear Bottlenecks* (CVPR 2018)  
[CVF Open Access](https://openaccess.thecvf.com/content_cvpr_2018/html/Sandler_MobileNetV2_Inverted_Residuals_CVPR_2018_paper.html)

[12] Tan and Le, *EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks* (ICML 2019)  
[PMLR](https://proceedings.mlr.press/v97/tan19a.html)

[13] Ovadia et al., *Can You Trust Your Model's Uncertainty? Evaluating Predictive Uncertainty under Dataset Shift* (NeurIPS 2019)  
[NeurIPS](https://proceedings.neurips.cc/paper/2019/hash/8558cb408c1d76621371888657d2eb1d-Abstract.html)

[14] Kirillov et al., *Segment Anything* (2023)  
[arXiv](https://arxiv.org/abs/2304.02643)

[15] Ravi et al., *SAM 2: Segment Anything in Images and Videos* (ICLR 2025)  
[OpenReview](https://openreview.net/forum?id=Ha6RTeWMd0)

[16] Alayrac et al., *Flamingo: a Visual Language Model for Few-Shot Learning* (NeurIPS 2022)  
[NeurIPS](https://proceedings.neurips.cc/paper_files/paper/2022/hash/960a172bc7fbf0177ccccbb411a7d800-Abstract-Conference.html)

[17] Radford et al., *Learning Transferable Visual Models From Natural Language Supervision* (ICML 2021)  
[PMLR](https://proceedings.mlr.press/v139/radford21a.html)

[18] Zhu et al., *Vision Mamba: Efficient Visual Representation Learning with Bidirectional State Space Model* (ICML 2024)  
[PMLR](https://proceedings.mlr.press/v235/zhu24f.html)

[19] Liu et al., *VMamba: Visual State Space Model* (NeurIPS 2024)  
[OpenReview](https://openreview.net/forum?id=5cZoCHnHFG)

[20] Hatamizadeh et al., *MambaVision: A Hybrid Mamba-Transformer Vision Backbone* (CVPR 2025)  
[CVF Open Access](https://openaccess.thecvf.com/content/CVPR2025/html/Hatamizadeh_MambaVision_A_Hybrid_Mamba-Transformer_Vision_Backbone_CVPR_2025_paper.html)
