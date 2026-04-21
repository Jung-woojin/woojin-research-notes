# Woojin Research Notes

논문 기반 기술 트렌드 리포트와 연구노트를 모아 두는 개인 저장소입니다.

이 저장소는 단순 요약 모음보다 아래에 더 가깝습니다.

- 논문과 공식 자료를 바탕으로 흐름 정리
- 최신 모델 소개보다 연구 질문과 문제 구조를 우선
- 비전 백본, robustness, dense vision, multimodality, edge AI를 서로 연결해서 보기
- 나중에 실험 설계와 프로젝트 노트로 이어지게 문서화

## Repository Map

문서는 크게 두 그룹으로 나뉩니다.

1. `트렌드 리포트`
기술 흐름을 정리한 문서

2. `연구노트`
트렌드 문서에서 바로 실험 질문과 비교축을 뽑아낸 문서

## Document Index

### 트렌드 리포트

| 문서 | 주제 | 메모 |
| --- | --- | --- |
| [computer-vision-trends-2026-04-20.md](./computer-vision-trends-2026-04-20.md) | Computer Vision 전반 | 비전 연구 흐름의 큰 그림 |
| [cnn-inductive-bias-revisited-trends-2026-04-20.md](./cnn-inductive-bias-revisited-trends-2026-04-20.md) | CNN inductive bias | ViT 이후에도 남는 CNN적 가정 |
| [small-data-domain-shift-robust-vision-trends-2026-04-20.md](./small-data-domain-shift-robust-vision-trends-2026-04-20.md) | Small-data / Domain Shift / Robust Vision | 현실 배포 환경의 robustness 문제 |
| [efficient-dense-vision-systems-trends-2026-04-20.md](./efficient-dense-vision-systems-trends-2026-04-20.md) | Efficient Dense Vision Systems | detection/segmentation 시스템 효율 |
| [multimodal-ai-trends-2026-04-20.pdf](./multimodal-ai-trends-2026-04-20.pdf) | Multimodal AI | multimodal model 흐름 요약 PDF |
| [on-device-edge-ai-trends-2026-04-20.pdf](./on-device-edge-ai-trends-2026-04-20.pdf) | On-device / Edge AI | edge deployment와 추론 제약 요약 PDF |
| [ai-tools-trend-report-2026-04-20.md](./ai-tools-trend-report-2026-04-20.md) | AI 툴 트렌드 | 연구 외부의 서비스/도구 스냅샷 |

### 연구노트

| 문서 | 주제 | 메모 |
| --- | --- | --- |
| [efficient-vision-backbone-notes-2026-04-21.md](./efficient-vision-backbone-notes-2026-04-21.md) | Vision backbone 비교 | CNN, ViT, Swin, InternImage, Mamba 계보 정리 |
| [research-questions-from-trends-2026-04-21.md](./research-questions-from-trends-2026-04-21.md) | 트렌드 기반 연구질문 | 실제 논문/프로젝트로 이어질 질문 목록 |

### 보조 PDF

| 문서 | 용도 |
| --- | --- |
| [cnn-inductive-bias-revisited-trends-2026-04-20.pdf](./cnn-inductive-bias-revisited-trends-2026-04-20.pdf) | CNN inductive bias 리포트 PDF 버전 |

## Recommended Reading Paths

### 1. 비전 연구 흐름부터 잡고 싶다면

1. [computer-vision-trends-2026-04-20.md](./computer-vision-trends-2026-04-20.md)
2. [cnn-inductive-bias-revisited-trends-2026-04-20.md](./cnn-inductive-bias-revisited-trends-2026-04-20.md)
3. [efficient-vision-backbone-notes-2026-04-21.md](./efficient-vision-backbone-notes-2026-04-21.md)

### 2. 실제 배포와 robustness까지 보고 싶다면

1. [small-data-domain-shift-robust-vision-trends-2026-04-20.md](./small-data-domain-shift-robust-vision-trends-2026-04-20.md)
2. [efficient-dense-vision-systems-trends-2026-04-20.md](./efficient-dense-vision-systems-trends-2026-04-20.md)
3. [research-questions-from-trends-2026-04-21.md](./research-questions-from-trends-2026-04-21.md)

### 3. Multimodal / Edge 쪽까지 넓히고 싶다면

1. [multimodal-ai-trends-2026-04-20.pdf](./multimodal-ai-trends-2026-04-20.pdf)
2. [on-device-edge-ai-trends-2026-04-20.pdf](./on-device-edge-ai-trends-2026-04-20.pdf)
3. [research-questions-from-trends-2026-04-21.md](./research-questions-from-trends-2026-04-21.md)

## Core Questions Running Through This Repo

이 저장소 문서들은 각각 따로 노는 문서가 아니라, 아래 질문들을 반복해서 파고듭니다.

- 어떤 inductive bias가 아직도 유효한가
- 어떤 backbone이 어떤 데이터 regime에서 강한가
- dense prediction에서 multi-scale hierarchy는 왜 계속 살아남는가
- synthetic benchmark 성능이 실제 배포 robustness로 이어지는가
- edge / on-device 환경에서 좋은 모델의 조건은 무엇인가
- multimodal model이 진짜 공통 표현을 학습하는가

즉 중심은 `최신 모델 소개`보다 `무엇이 아직 설명되지 않았는가`에 있습니다.

## Writing Style

대부분의 문서는 아래 형식으로 정리되어 있습니다.

- 빠른 결론
- 핵심 트렌드 정리
- 왜 연구적으로 중요한지
- 바로 실험으로 바꿀 수 있는 질문
- 참고문헌 / 공식 링크

그래서 블로그 글보다는 `리서치 브리프 + 실험 노트`에 더 가깝습니다.

## Suggested Next Additions

이 저장소에서 다음으로 확장하기 좋은 주제는 아래와 같습니다.

- world model / video understanding
- medical vision under domain shift
- calibration-aware test-time adaptation
- efficient foundation segmentation
- high-resolution backbone benchmarking

## Why This Repo Exists

논문을 읽다 보면 자주 생기는 문제가 있습니다.

- 읽은 것은 많은데 연결이 안 된다
- 최신 모델 이름은 아는데 왜 중요한지는 흐릿하다
- 흥미로운 주제는 보이는데 실험 질문으로 떨어지지 않는다

이 저장소는 그 간극을 줄이기 위해 만들었습니다.

한 줄로 요약하면:

> 논문과 기술 트렌드를 연구 질문으로 바꾸기 위한 개인 연구노트 저장소
