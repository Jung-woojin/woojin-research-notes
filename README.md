# Woojin Research Notes

논문 기반 기술 트렌드 리포트, 연구노트, 시스템 기술서를 함께 모아 두는 개인 저장소입니다.

이 저장소는 단순 요약 아카이브보다 아래에 더 가깝습니다.

- 논문과 공식 문서를 바탕으로 흐름 정리
- 최신 모델 소개보다 `문제 구조`, `평가 기준`, `실험 질문`을 우선
- 컴퓨터비전, 멀티모달, 엣지 AI, 감시시스템, 데이터셋/실험 프로토콜을 연결해서 보기
- 나중에 실제 실험 설계와 시스템 구현으로 이어지게 문서화

## Repository Structure

문서는 현재 크게 네 그룹으로 나뉩니다.

1. `트렌드 리포트`
연구/산업 흐름을 정리한 문서

2. `연구노트`
트렌드 문서에서 바로 실험 질문과 비교축을 뽑아낸 문서

3. `도메인/주제 설계 문서`
특정 문제를 연구 프레임으로 재구성한 문서

4. `실험/시스템 기술서`
실제로 재현 가능한 프로토콜, 데이터 운영, 시스템 스택을 정리한 문서

## Document Index

### 트렌드 리포트

| 문서 | 주제 | 메모 |
| --- | --- | --- |
| [computer-vision-trends-2026-04-20.md](./computer-vision-trends-2026-04-20.md) | Computer Vision 전반 | 비전 연구 흐름의 큰 그림 |
| [cnn-inductive-bias-revisited-trends-2026-04-20.md](./cnn-inductive-bias-revisited-trends-2026-04-20.md) | CNN inductive bias | ViT 이후에도 남는 CNN적 가정 |
| [small-data-domain-shift-robust-vision-trends-2026-04-20.md](./small-data-domain-shift-robust-vision-trends-2026-04-20.md) | Small-data / Domain Shift / Robust Vision | 현실 배포 환경의 robustness 문제 |
| [efficient-dense-vision-systems-trends-2026-04-20.md](./efficient-dense-vision-systems-trends-2026-04-20.md) | Efficient Dense Vision Systems | detection/segmentation 시스템 효율 |
| [multimodal-ai-trends-2026-04-20.md](./multimodal-ai-trends-2026-04-20.md) | Multimodal AI | multimodal model 흐름과 방향 전환 |
| [on-device-edge-ai-trends-2026-04-20.md](./on-device-edge-ai-trends-2026-04-20.md) | On-device / Edge AI | 엣지 배포, 하이브리드 추론, 하드웨어 트렌드 |
| [data-centric-vision-and-synthetic-data-trends-2026.md](./data-centric-vision-and-synthetic-data-trends-2026.md) | Data-centric Vision / Synthetic Data | 데이터 파이프라인, synthetic data, distillation |
| [camera-based-surveillance-systems-trends-2026-04-24.md](./camera-based-surveillance-systems-trends-2026-04-24.md) | Camera-based Surveillance Systems | Jetson, ONVIF/RTSP, ROS 2, 감시 제품, 센서, 학술 트렌드 |
| [ai-tools-trend-report-2026-04-20.md](./ai-tools-trend-report-2026-04-20.md) | AI 툴 트렌드 | 연구 외부의 서비스/도구 스냅샷 |

### 연구노트

| 문서 | 주제 | 메모 |
| --- | --- | --- |
| [efficient-vision-backbone-notes-2026-04-21.md](./efficient-vision-backbone-notes-2026-04-21.md) | Vision backbone 비교 | CNN, ViT, Swin, InternImage, Mamba 계보 정리 |
| [vision-training-recipes-and-scaling-laws-notes-2026.md](./vision-training-recipes-and-scaling-laws-notes-2026.md) | Training recipe / scaling law | backbone 비교를 recipe와 scaling 관점에서 재정리 |
| [research-questions-from-trends-2026-04-21.md](./research-questions-from-trends-2026-04-21.md) | 트렌드 기반 연구질문 | 실제 논문/프로젝트로 이어질 질문 목록 |

### 도메인/주제 설계 문서

| 문서 | 주제 | 메모 |
| --- | --- | --- |
| [fog-visibility-paper-topic-candidates-2026-04-22.md](./fog-visibility-paper-topic-candidates-2026-04-22.md) | 항만 시정 분류 주제 후보 | 항별 독립 학습, 전이, uncertainty, deployment 프레임 |

### 실험/시스템 기술서

| 문서 | 주제 | 메모 |
| --- | --- | --- |
| [harbor-fog-dataset-protocol-and-experiment-design-2026-04-23.md](./harbor-fog-dataset-protocol-and-experiment-design-2026-04-23.md) | 데이터셋 실험 프로토콜 | split, leakage 방지, baseline, metric, H200 운영 기준 |

### PDF 버전

주요 문서 중 일부는 PDF 버전도 같이 포함되어 있습니다.

- [computer-vision-trends-2026-04-20.pdf](./computer-vision-trends-2026-04-20.pdf)
- [cnn-inductive-bias-revisited-trends-2026-04-20.pdf](./cnn-inductive-bias-revisited-trends-2026-04-20.pdf)
- [efficient-dense-vision-systems-trends-2026-04-20.pdf](./efficient-dense-vision-systems-trends-2026-04-20.pdf)
- [efficient-vision-backbone-notes-2026-04-21.pdf](./efficient-vision-backbone-notes-2026-04-21.pdf)
- [multimodal-ai-trends-2026-04-20.pdf](./multimodal-ai-trends-2026-04-20.pdf)
- [on-device-edge-ai-trends-2026-04-20.pdf](./on-device-edge-ai-trends-2026-04-20.pdf)
- [small-data-domain-shift-robust-vision-trends-2026-04-20.pdf](./small-data-domain-shift-robust-vision-trends-2026-04-20.pdf)
- [data-centric-vision-and-synthetic-data-trends-2026.pdf](./data-centric-vision-and-synthetic-data-trends-2026.pdf)
- [vision-training-recipes-and-scaling-laws-notes-2026.pdf](./vision-training-recipes-and-scaling-laws-notes-2026.pdf)
- [research-questions-from-trends-2026-04-21.pdf](./research-questions-from-trends-2026-04-21.pdf)
- [fog-visibility-paper-topic-candidates-2026-04-22.pdf](./fog-visibility-paper-topic-candidates-2026-04-22.pdf)
- [camera-based-surveillance-systems-trends-2026-04-24.pdf](./camera-based-surveillance-systems-trends-2026-04-24.pdf)

## Recommended Reading Paths

### 1. 비전 연구 흐름부터 잡고 싶다면

1. [computer-vision-trends-2026-04-20.md](./computer-vision-trends-2026-04-20.md)
2. [cnn-inductive-bias-revisited-trends-2026-04-20.md](./cnn-inductive-bias-revisited-trends-2026-04-20.md)
3. [efficient-vision-backbone-notes-2026-04-21.md](./efficient-vision-backbone-notes-2026-04-21.md)
4. [vision-training-recipes-and-scaling-laws-notes-2026.md](./vision-training-recipes-and-scaling-laws-notes-2026.md)

### 2. robustness와 실제 배포까지 보고 싶다면

1. [small-data-domain-shift-robust-vision-trends-2026-04-20.md](./small-data-domain-shift-robust-vision-trends-2026-04-20.md)
2. [data-centric-vision-and-synthetic-data-trends-2026.md](./data-centric-vision-and-synthetic-data-trends-2026.md)
3. [on-device-edge-ai-trends-2026-04-20.md](./on-device-edge-ai-trends-2026-04-20.md)
4. [research-questions-from-trends-2026-04-21.md](./research-questions-from-trends-2026-04-21.md)

### 3. 감시시스템 / 엣지 비전 스택이 궁금하다면

1. [camera-based-surveillance-systems-trends-2026-04-24.md](./camera-based-surveillance-systems-trends-2026-04-24.md)
2. [on-device-edge-ai-trends-2026-04-20.md](./on-device-edge-ai-trends-2026-04-20.md)
3. [harbor-fog-dataset-protocol-and-experiment-design-2026-04-23.md](./harbor-fog-dataset-protocol-and-experiment-design-2026-04-23.md)

### 4. 특정 도메인을 연구 문제로 바꾸고 싶다면

1. [research-questions-from-trends-2026-04-21.md](./research-questions-from-trends-2026-04-21.md)
2. [fog-visibility-paper-topic-candidates-2026-04-22.md](./fog-visibility-paper-topic-candidates-2026-04-22.md)
3. [harbor-fog-dataset-protocol-and-experiment-design-2026-04-23.md](./harbor-fog-dataset-protocol-and-experiment-design-2026-04-23.md)

## Core Questions Running Through This Repo

이 저장소 문서들은 각각 따로 노는 문서가 아니라, 아래 질문들을 반복해서 파고듭니다.

- 어떤 inductive bias가 아직도 유효한가
- 어떤 backbone이 어떤 데이터 regime에서 강한가
- 모델 차이보다 data pipeline과 training recipe가 더 중요한 순간은 언제인가
- synthetic benchmark 성능이 실제 배포 robustness로 이어지는가
- edge / on-device 환경에서 좋은 모델과 시스템의 조건은 무엇인가
- 감시시스템은 어떤 센서/통신/메타데이터 구조로 진화하고 있는가

즉 중심은 `최신 모델 소개`보다 `무엇을 어떻게 비교하고 평가할 것인가`에 있습니다.

## Writing Style

대부분의 문서는 아래 형식으로 정리되어 있습니다.

- 빠른 결론
- 핵심 트렌드 또는 설계 원칙
- 왜 연구적/실무적으로 중요한지
- 바로 실험이나 시스템 설계로 바꿀 수 있는 질문
- 참고문헌 / 공식 링크

그래서 블로그 글보다는 `리서치 브리프 + 실험 노트 + 기술서`에 더 가깝습니다.

## Suggested Next Additions

이 저장소에서 다음으로 확장하기 좋은 주제는 아래와 같습니다.

- vision evaluation protocol standard
- baseline selection and comparison standard
- dataset quality audit checklist
- surveillance edge reference architecture
- multimodal retrieval for video systems

## Why This Repo Exists

논문을 읽다 보면 자주 생기는 문제가 있습니다.

- 읽은 것은 많은데 연결이 안 된다
- 최신 모델 이름은 아는데 왜 중요한지는 흐릿하다
- 흥미로운 주제는 보이는데 실험 질문이나 시스템 설계로 떨어지지 않는다

이 저장소는 그 간극을 줄이기 위해 만들었습니다.

한 줄로 요약하면:

> 논문과 기술 트렌드를 연구 질문, 실험 프로토콜, 시스템 설계로 바꾸기 위한 개인 연구노트 저장소
