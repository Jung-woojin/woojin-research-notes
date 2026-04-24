# 2026 카메라 기반 감시시스템 기술 트렌드 조사서

기준일: 2026-04-24

## 목적

이 문서는 `카메라 기반 감시시스템`을 산업 제품, 엣지 AI, 로보틱스/ROS, 학술 트렌드까지 포함해 한 번에 훑기 위한 기술 조사서다.

이번 조사의 질문은 아래와 같다.

- 요즘 감시시스템은 어떤 하드웨어 위에 올라가나
- Jetson Nano 같은 보드가 아직 기준선인가, 아니면 Orin 계열이 사실상 기준선인가
- 카메라와 서버, 엣지 장치, 클라우드, 로봇은 어떤 통신 방식으로 묶나
- 실제 현장에서 어떤 모델들이 많이 쓰이나
- 대표 기업들은 어떤 제품 방향으로 가고 있나
- 카메라 입력은 RGB만 쓰는가, IR/thermal/radar/depth까지 어디서 쓰나
- 학술적으로는 어디로 가고 있나

## 빠른 결론

1. `고정형 CCTV/보안 감시시스템의 기본 축은 여전히 IP 카메라 + ONVIF/RTSP + VMS/NVR + 엣지/서버 분석이다.`
2. `Jetson Nano는 여전히 존재하지만, 2026년 실전 기준선은 사실상 Jetson Orin Nano/Orin NX 쪽으로 넘어갔다.`
3. `ROS 2는 고정형 CCTV 업계의 기본 표준은 아니고, 로봇/이동형 센서/액티브 PTZ/멀티센서 자율 시스템에서 강하다.`
4. `산업 제품은 “탐지”에서 “메타데이터 중심 검색·추적·요약”으로 이동 중이며, 자연어 검색과 멀티카메라 추적이 빠르게 올라오고 있다.`
5. `센서는 RGB 단일이 기본이지만, 야간·안개·경계감시·광역 감시에서는 IR, thermal, radar-video fusion, bispectral 구성이 빠르게 늘고 있다.`
6. `학술 트렌드는 open-vocabulary anomaly, 멀티카메라 3D 추적, 온라인 비디오 이해, RGB-thermal 같은 멀티모달 쪽으로 이동 중이다.`

---

## 1. 시스템을 어떻게 나눠 봐야 하나

카메라 기반 감시시스템은 크게 세 층으로 나누어 보는 게 가장 실용적이다.

### 1-1. 고정형 보안/산업 감시 스택

가장 전통적이고 여전히 주류인 구조다.

- IP camera
- RTSP/ONVIF
- VMS/NVR
- 서버 또는 엣지 AI 분석
- 알림/검색/저장/관제 UI

이 구조에서 핵심은 `영상 스트림`과 `분석 메타데이터`를 어떻게 표준적으로 다루는가다.

### 1-2. 엣지 AI 스택

최근 가장 빠르게 커지는 구조다.

- 카메라
- Jetson Orin 계열 같은 엣지 AI 보드
- DeepStream 같은 스트리밍 AI 파이프라인
- 로컬 저장, 메타데이터 버스, API gateway
- 선택적으로 클라우드 연동

이 구조는 `여러 스트림을 저지연으로 받아 실시간 탐지/추적/검색`하는 데 강하다.

### 1-3. 로보틱스/액티브 센서 스택

고정 CCTV와는 결이 다르다.

- 이동형 로봇/드론/AGV/무인 순찰 시스템
- RGB-D, LiDAR, IMU, thermal, event camera
- ROS 2 / Isaac ROS
- DDS 기반 통신
- 맵핑, 내비게이션, 액티브 센서 제어

즉 `카메라 기반 감시`라고 해도, `고정식 영상 관제`와 `로봇 기반 순찰/탐지`는 스택이 다르다.

---

## 2. 하드웨어 트렌드: Jetson Nano에서 Orin 계열로

### 2-1. Jetson Nano는 아직 있긴 하지만 기준선은 아니다

NVIDIA Jetson Product Lifecycle 페이지 기준으로 `Jetson Nano` 상용 모듈은 `2027년 1월`까지 available로 표시되어 있다. 반면 `Jetson Orin Nano 4GB/8GB`는 `2032년 1월`까지로 잡혀 있다.[1]

또 NVIDIA FAQ는 `JetPack 4 is EoL and supports Jetson Xavier, Jetson TX2, and Jetson Nano`라고 명시한다.[2]

즉 실무 해석은 아래가 맞다.

- Nano는 완전히 사라진 것은 아니다
- 하지만 소프트웨어 기준선은 오래된 JetPack 4.x 계열이다
- 새 프로젝트 기준으로는 Nano를 주력 기준으로 잡기 어렵다

### 2-2. 2026 실전 기준선은 Orin Nano/Orin NX

NVIDIA 공식 가이드에 따르면 Jetson Orin Nano Developer Kit는 JetPack 6.x를 전제로 하고, 공장 출하 펌웨어가 JetPack 6.x와 호환되지 않을 수 있어 먼저 펌웨어 업데이트가 필요하다고 안내한다.[3]

JetPack 6.1은 NVIDIA의 `latest production release of JetPack 6`이며, Linux 5.15, Ubuntu 22.04 기반 rootfs, CUDA 12.6, TensorRT 10.3 등을 포함한다.[4]

실무적으로는 아래처럼 보면 된다.

- `Nano`: 구형, 유지보수형, 저비용 프로토타입
- `Orin Nano`: 2026 entry edge AI 기준선
- `Orin NX / AGX Orin`: 다채널, 고성능, 멀티센서 감시/로봇 쪽

### 2-3. Jetson에서 요즘 권장 소프트웨어 스택

NVIDIA는 이제 Jetson을 단순 보드로만 팔지 않고, `JetPack + DeepStream + Jetson Platform Services + Metropolis` 축으로 묶는다.

특히 Jetson Platform Services는 `production-grade systems on Jetson`을 위한 공통 서비스 계층으로 소개되며, VST, Redis, Ingress, Monitoring, Firewall 같은 구성요소를 제공한다.[5]

---

## 3. 통신과 시스템 묶는 방식

### 3-1. 고정형 감시시스템의 표준: ONVIF + RTSP

고정형 IP 감시에서 가장 중요한 표준은 여전히 ONVIF다.

ONVIF Profile S는 IP 기반 영상 시스템용 프로파일이며, 카메라/비디오 인코더가 영상을 IP 네트워크로 전송하고, 클라이언트가 이를 설정·요청·제어할 수 있게 한다.[6]

실전적으로는 아래처럼 해석하면 된다.

- `RTSP`: 실제 영상 스트리밍
- `ONVIF S/T`: 카메라 discovery, 제어, 고급 스트리밍
- `ONVIF M`: 분석 메타데이터 표준화

Axis는 자사 ONVIF 페이지에서 `Profile M`이 분석 데이터, 이벤트, PTZ 위치용 표준 메타데이터 스트림을 지원하며, conformant VMS가 이를 이용해 자동 응답과 저장/검색을 할 수 있다고 설명한다.[7]

이건 꽤 중요하다.

- 예전: 비디오는 영상, 분석은 벤더 종속
- 지금: 영상은 RTSP/ONVIF, 분석도 메타데이터 표준화 방향

### 3-2. Jetson/엣지 AI에서 묶는 방식

NVIDIA Jetson Platform Services의 `VST(Video Storage Toolkit)`는 카메라 discovery, 저장, 하드웨어 가속 디코드, 스트리밍을 담당하며, ONVIF-S compliant IP camera auto-discovery, RTSP URL 수동 등록, WebRTC, REST API를 지원한다.[8]

또 foundation service로 `Redis`를 공용 message bus/storage로 둔다.[5]

즉 Jetson 기반 감시 스택의 요즘 모습은 아래에 가깝다.

- camera ingress: ONVIF/RTSP
- stream pipeline: GStreamer/DeepStream
- internal message bus: Redis
- external API: REST
- remote live view: WebRTC
- deployment: Docker Compose

이 구조는 `카메라 스트림 처리 + 메타데이터 이벤트 처리 + 대시보드/서비스 연동`에 적합하다.

### 3-3. ROS 2는 어디서 쓰나

ROS 2 문서 기준으로 ROS 2의 최신 릴리스는 `Kilted`, `Jazzy`는 아직 지원되는 이전 버전이다.[9] ROS 2는 현재 활발히 개발되는 버전이며, ROS 1은 더 이상 새 릴리스를 내지 않는다고 명시돼 있다.[10]

ROS 2는 DDS/RTPS 미들웨어 위에 올라가며, distributed discovery와 QoS 제어를 제공한다.[11][12]

중요한 실무 해석은 아래다.

- `고정형 CCTV/VMS 업계`: ROS 2가 기본 표준은 아님
- `로봇/드론/무인순찰/모바일 감시`: ROS 2가 매우 유용
- `PTZ, 레이더, RGB-D, LiDAR, IMU`를 묶는 멀티센서 자율 시스템: ROS 2가 강함

즉 ROS 2는 `카메라 감시 일반`의 표준이라기보다 `움직이는 시스템`의 표준에 가깝다.

### 3-4. Isaac ROS는 언제 쓰나

NVIDIA Isaac ROS는 ROS 2용 가속 패키지 집합이다. 예를 들어 `Isaac ROS nvBlox`는 RGB-D나 LiDAR로 dense 3D map을 만들고 temporal costmap을 생성한다.[13]

따라서 아래 상황이면 ROS 2 + Isaac ROS가 잘 맞는다.

- 고정 CCTV가 아니라 이동형 순찰 로봇
- RGB-D / LiDAR / IMU 동시 처리
- 3D 맵핑, 자율주행, 내비게이션
- PTZ/autotracking이 아니라 실제 로봇 제어

---

## 4. 카메라 입력은 무엇을 쓰나

### 4-1. RGB visible camera

여전히 기본값이다.

장점:

- 가장 저렴함
- 해상도와 식별력 우수
- 모델 생태계가 가장 큼
- 낮/실내 환경에서 최고

단점:

- 야간/역광/안개/연기/눈부심에 취약

결론:

- 대부분의 감시시스템은 RGB로 시작한다

### 4-2. RGB + active IR / night vision

실무에서 매우 흔하다.

- 일반 보안 카메라의 야간 IR LED
- NIR 조명 기반 night surveillance

이 조합은 비용 대비 효율이 좋아서, `RGB only`보다 훨씬 자주 쓰인다.  
다만 강한 식별, 장거리, 안개/연기 관통까지 기대하면 한계가 있다.

### 4-3. Thermal LWIR

Axis와 FLIR는 thermal security camera를 전면 제품군으로 밀고 있다. Axis는 thermal camera가 complete darkness, light fog, smoke 같은 조건에서 유리하다고 설명하고,[14] FLIR는 thermal security camera가 total darkness, glaring light, smoke, and light fog에서도 detection에 강하다고 설명한다.[15]

thermal의 장점:

- 완전한 어둠
- 역광 영향 적음
- 연기, 약한 안개, 눈부심에 강함
- 프라이버시 측면에서 유리

단점:

- 식별력은 RGB보다 약한 경우 많음
- 가격이 높음
- 객체 세부 속성 인식은 불리

적합한 곳:

- perimeter security
- 야간 침입 감지
- 장거리 감시
- 화재/발열 모니터링

### 4-4. Bispectral: RGB + Thermal

이게 최근 강한 추세다.

Axis는 2026년 3월 `high-resolution thermal + 4K visual`을 한 장치에 넣은 AI-powered bispectral camera를 발표했다.[16]

이 조합의 장점은 명확하다.

- thermal로 검출
- RGB로 식별
- 한 장치에서 낮/밤/악조건 대응

결국 `detect with thermal, verify with RGB` 프레임이다.

### 4-5. Radar + Camera fusion

이것도 실제 산업 트렌드다.

Axis의 radar-video fusion camera는 radar와 video를 합쳐 detection/classification의 false/missed alarm을 줄이는 방향을 제시한다.[17] Axis는 2026년에도 wide-area radar 제품과 radar-video fusion 연동을 계속 확대하고 있다.[18]

장점:

- 악천후와 조명 변화에 강함
- 장거리 접근 탐지
- PTZ cueing/autotracking과 결합 쉬움

적합한 곳:

- 광역 경계 감시
- 공항, 항만, 발전소, 태양광 단지
- 도로/교통 감시

### 4-6. RGB-D / Stereo depth

깊이 카메라는 감시 전반의 기본은 아니다.  
하지만 실내 occupancy, 거리 측정, 로봇 순찰, 사람-물체 거리 이해에는 매우 유용하다.

Intel RealSense D455는 RGB와 depth를 함께 제공하며, depth accuracy를 개선하고 multi-camera 사용도 염두에 둔 제품이다.[19]

적합한 곳:

- 실내 인원/행동 분석
- 로봇 시야
- 접근 거리 기반 경보
- 3D scene 이해

주의:

- 야외 강광, 비/안개, 장거리에는 불리

### 4-7. Event camera

아직 감시시스템의 주류는 아니다.  
하지만 high-speed, high dynamic range, low-power 쪽에서 의미가 있다.

Prophesee 기반 industrial event camera는 `>10k fps equivalent`, `>120 dB dynamic range`, `0.08 lux`, `10-1000x less data` 같은 특징을 전면에 내세운다.[20]

적합한 곳:

- 고속 이벤트 감지
- 초저지연 반응
- 극단적 HDR 환경
- 특수 산업 감시

결론:

- `RGB`: 기본
- `RGB+IR`: 야간 기본
- `thermal`: 악조건/경계
- `bispectral`: premium
- `radar+camera`: 광역/침입/교통
- `RGB-D`: 실내/로봇
- `event`: niche

---

## 5. 실제로 어떤 모델을 쓰나

실무에서 “감시 모델”은 보통 하나가 아니다.  
아래처럼 task별 스택으로 나뉜다.

### 5-1. 1단계: 객체 검출

가장 기본이다.

- person
- vehicle
- face
- bag
- PPE
- plate region

NVIDIA TAO는 이런 edge deployment용 모델들을 꾸준히 제공해 왔고, 현재는 pretrained foundation model + task model fine-tuning 흐름을 밀고 있다.[21]

대표 예:

- `PeopleNet`
- `RT-DETR` 계열
- EfficientDet 계열
- vendor proprietary detector
- open-source YOLO 계열

NVIDIA PeopleNet은 `persons / bags / faces` 3개 클래스를 검출하는 pretrained detector로 설명된다.[22]

실무적으로는 아래가 흔하다.

- 상용 엣지 카메라: 벤더 내장 detector
- 커스텀 Jetson/NVR: YOLO, PeopleNet, RT-DETR

### 5-2. 2단계: 다중 객체 추적

검출만으로는 감시가 안 된다.  
시간 축으로 이어야 한다.

그래서 보통 아래가 붙는다.

- ByteTrack 계열
- DeepSORT/BoT-SORT/OC-SORT 계열
- vendor proprietary tracker

여기에 사람/차량 속성 추출이 붙는다.

### 5-3. 3단계: 재식별(Re-ID)

여러 카메라를 넘나들며 같은 사람/차량을 이어야 할 때 필요하다.

Hanwha Vision은 2025년 발표한 Wisenet 9 SoC에서 `Re-Identification (Re-ID)`를 직접 강조하고 있다.[23]

즉 요즘 감시는 단순 detection이 아니라:

- detect
- track
- re-identify
- search

로 간다.

### 5-4. 4단계: 규칙/행동/이상 탐지

산업 현장에서는 아래가 많다.

- intrusion
- loitering
- line crossing
- wrong-way
- occupancy
- fall/fire/smoke
- abnormal behavior

이 단계는 아직 detector+tracker+rules 조합이 주류지만, anomaly 모델과 language model이 빠르게 들어오고 있다.

### 5-5. 5단계: 자연어 검색과 VLM/LLM

가장 최근 변화다.

Hikvision은 2025년 `AcuSeek NVR`를 발표하며, large multimodal AI model 기반 자연어 검색을 전면에 내세웠다.[24]

Verkada도 AI-powered search와 unified timeline을 밀고 있고, 자연어/속성 기반 검색을 운영 경험의 중심에 두고 있다.[25][26]

NVIDIA도 Metropolis + DeepStream + Video Search/Summarization 방향으로 이동 중이다.[27][28]

즉 2026 감시 모델 흐름은 아래처럼 요약할 수 있다.

1. detector
2. tracker
3. re-id
4. metadata index
5. natural-language retrieval

---

## 6. 대표적인 기업과 제품 방향

### 6-1. NVIDIA

역할:

- 카메라 제조사가 아니라 `플랫폼 공급자`

주요 축:

- `Jetson Orin`
- `DeepStream`
- `Metropolis`
- `Jetson Platform Services`
- `TAO`
- `Isaac ROS`

NVIDIA Metropolis는 multi-camera tracking, video search/summarization, edge-to-cloud vision AI agent를 전면에 내세운다.[27]

해석:

- OEM/통합업체가 가장 많이 참고할 스택

### 6-2. Axis Communications

강점:

- 전통적인 네트워크 카메라 강자
- ONVIF/VMS/edge analytics/thermal/radar까지 제품층이 넓음

최근 방향:

- AXIS Camera Station Pro
- thermal/bispectral
- radar-video fusion
- analytics metadata

즉 Axis는 `카메라 회사`에서 `멀티센서 보안 플랫폼 회사`로 가고 있다.[29][16][17]

### 6-3. Hanwha Vision

강점:

- 한국 기업
- AI edge camera + cloud metadata analytics 양쪽 다 진행

최근 방향:

- `Wisenet 9` SoC 기반 2세대 AI 카메라
- `SightMind` cloud metadata analytics

SightMind는 raw video 자체보다 `metadata 저장·분석·시각화`를 중심에 둔다.[30]

즉 Hanwha는 `edge AI camera + cloud business analytics` 쪽을 강하게 미는 중이다.

### 6-4. Hikvision

최근 방향:

- DeepinView/DeepinMind 계열
- AcuSeek
- large multimodal AI model 기반 검색

즉 Hikvision은 `영상 저장/관제`를 넘어서 `text-to-video retrieval`을 직접 제품 기능으로 올리고 있다.[24]

### 6-5. Avigilon

강점:

- enterprise on-prem video security
- AI-powered video analytics

Avigilon Unity는 on-prem video analytics와 multi-site security operation을 전면에 둔다.[31]

즉 클라우드 전환이 있더라도, 강한 on-prem 수요는 여전히 크다는 점을 보여 준다.

### 6-6. Verkada

강점:

- cloud-managed physical security
- AI search와 cross-camera timeline

Verkada는 `cloud-based management platform`, `AI-powered search`, `cross-camera unified timeline`을 핵심 경험으로 제시한다.[25][26]

즉 `VMS + search + workflow`를 SaaS처럼 제공하는 방향이다.

### 6-7. FLIR

강점:

- thermal specialization
- perimeter/security/fire

FLIR는 thermal security camera와 radar integration을 꾸준히 밀고 있다.[15][32]

즉 RGB가 안 되는 환경에서 전문성을 가진 축이다.

---

## 7. 요즘 아키텍처는 어떻게 묶나

### 7-1. 가장 전형적인 산업형 구조

```text
IP Camera -> ONVIF/RTSP -> NVR/VMS -> Detector/Tracker -> Metadata DB -> Search/Alert UI
```

이 구조는 여전히 가장 흔하다.

### 7-2. Jetson 기반 엣지형 구조

```text
Camera -> RTSP/ONVIF ingest -> DeepStream pipeline -> Tracker/Re-ID
-> Redis/Message Bus -> REST/WebRTC -> Dashboard/Cloud
```

이 구조는 Jetson Platform Services 문서와 거의 맞닿아 있다.[5][8]

### 7-3. ROS 2 기반 로봇형 구조

```text
RGB / RGB-D / LiDAR / IMU / Thermal -> ROS 2 topics over DDS
-> perception nodes -> mapping/navigation/control -> operator UI
```

이 구조는 이동형 감시, 자율 순찰, 드론, 스마트 PTZ에 적합하다.

### 7-4. 중요한 실무 판단

아래를 명확히 나누는 게 좋다.

- 고정형 CCTV / 보안 감시
  - ONVIF/RTSP/VMS 중심
- 엣지 서버형 비디오 분석
  - DeepStream/Jetson 중심
- 로봇/드론/순찰형 감시
  - ROS 2/Isaac ROS 중심

ROS 2를 모든 감시시스템의 기본으로 보는 건 과장이다.  
반대로 ONVIF/RTSP/VMS만으로 이동형 순찰을 만들려는 것도 맞지 않는다.

---

## 8. 학술적 트렌드

### 8-1. Open-vocabulary / open-world anomaly detection

CVPR 2024에는 `Open-Vocabulary Video Anomaly Detection`이 나왔고,[33] LLM을 활용한 training-free anomaly detection도 등장했다.[34]

의미:

- anomaly를 fixed label set으로만 다루지 않으려는 흐름
- 감시시스템이 점점 “정해진 이벤트 탐지기”에서 벗어나는 중

### 8-2. 멀티카메라 추적 고도화

CVPR 2024에는 `ADA-Track: End-to-End Multi-Camera 3D Multi-Object Tracking`이 나왔고,[35] `MTMMC` 같은 real-world multi-modal camera tracking benchmark도 등장했다.[36]

의미:

- 카메라 하나가 아니라 카메라 네트워크 전체가 연구 단위가 됨
- re-id와 geometry, topology를 같이 보는 방향

### 8-3. 온라인 비디오 이해와 LLM/VLM

CVPR 2025의 `OVBench and VideoChat-Online`은 online video understanding 자체를 별도 문제로 본다.[37]

의미:

- 녹화 영상 사후 검색만이 아니라
- 스트리밍 상황에서 무엇이 일어나는지 실시간 이해하는 쪽으로 이동

이건 제품 쪽의 자연어 검색, 영상 요약과 맞물린다.

### 8-4. RGB-T / thermal understanding

CVPR 2025에는 RGB-T tracking 쪽 논문이 계속 나오고 있고,[38] 2025~2026에는 thermal/VLM 평가 벤치마크도 등장한다.[39][40]

의미:

- thermal이 더 이상 niche sensor로만 머물지 않고
- foundation model/VLM 평가 대상으로 올라오고 있다

### 8-5. 요약하면

학술 트렌드는 대체로 아래 다섯 축이다.

1. `open-world anomaly`
2. `multi-camera tracking and reasoning`
3. `language-based video retrieval`
4. `online/streaming video understanding`
5. `RGB-thermal / multimodal sensing`

---

## 9. 실무적으로 어떤 선택이 맞나

### 9-1. 고정형 감시를 만들면

권장:

- IP camera
- ONVIF/RTSP
- VMS/NVR 또는 DeepStream 기반 edge analytics
- metadata 중심 설계

ROS 2는 기본 선택이 아니다.

### 9-2. Jetson 보드를 쓴다면

권장:

- 새 프로젝트면 `Jetson Orin Nano` 이상
- `JetPack 6.x` 기준
- `DeepStream + JPS` 우선 검토

`Jetson Nano`는 유지보수/교육/간단 PoC 용도로는 가능하지만, 2026 신규 기준선으로는 약하다.

### 9-3. 센서를 고른다면

권장 기본순서:

1. `RGB`
2. 야간 많으면 `RGB + IR`
3. 경계감시/악조건이면 `thermal` 또는 `bispectral`
4. 광역 외곽/항만/공항이면 `radar + camera`
5. 실내 로봇이면 `RGB-D`

### 9-4. 모델을 고른다면

권장 기본순서:

1. detector
2. tracker
3. re-id
4. rule engine / metadata search
5. 필요 시 VLM search

처음부터 anomaly LLM만으로 가는 건 아직 과하다.

### 9-5. 요즘 제품 트렌드를 한 줄로 요약하면

`카메라`만 파는 시대에서  
`메타데이터 + 검색 + 멀티카메라 추적 + 자연어 인터페이스`를 파는 시대로 넘어가고 있다.

---

## 최종 정리

2026년의 카메라 기반 감시시스템은 더 이상 단순 CCTV 저장 장치가 아니다.

핵심 변화는 아래와 같다.

- 하드웨어는 `Jetson Nano`에서 `Orin` 계열로 올라갔다
- 통신은 여전히 `ONVIF/RTSP`가 기본이지만, 내부는 `metadata bus + REST/WebRTC + container` 구조로 가고 있다
- ROS 2는 감시업계 전체 표준이 아니라 `로봇/이동형 감시`의 핵심 스택이다
- 모델은 `검출`만이 아니라 `추적, re-id, 자연어 검색`까지 묶인 파이프라인이 된다
- 센서는 `RGB` 단일에서 `thermal, bispectral, radar fusion`으로 확장 중이다
- 제품과 학술 모두 `멀티카메라`, `멀티모달`, `자연어 검색`, `온라인 비디오 이해` 쪽으로 이동 중이다

따라서 새 시스템을 설계할 때 가장 현실적인 질문은 아래다.

- 고정형인가, 이동형인가
- RGB로 충분한가, thermal/radar가 필요한가
- VMS 중심인가, Jetson edge AI 중심인가
- 단순 알람이 필요한가, 자연어 검색과 멀티카메라 추적까지 필요한가

이 질문들에 답하면 스택이 자연스럽게 정해진다.

---

## 참고자료

[1] NVIDIA Jetson Product Lifecycle  
<https://developer.nvidia.com/embedded/lifecycle>

[2] NVIDIA Jetson FAQ  
<https://developer.nvidia.com/embedded/faq>

[3] NVIDIA Jetson Orin Nano Developer Kit Getting Started Guide  
<https://developer.nvidia.com/embedded/learn/get-started-jetson-orin-nano-devkit>

[4] NVIDIA JetPack 6.1 Release Notes  
<https://docs.nvidia.com/jetson/jetpack/6.1/release-notes/index.html>

[5] NVIDIA Jetson Platform Services Overview  
<https://docs.nvidia.com/jetson/jps/platform-services/overview.html>

[6] ONVIF Profile S  
<https://www.onvif.org/profiles/profile-s/>

[7] Axis ONVIF Profiles  
<https://www.axis.com/en-gb/onvif>

[8] NVIDIA VST Overview  
<https://docs.nvidia.com/jetson/jps/vst/VST_Overview.html>

[9] ROS 2 Documentation  
<https://docs.ros.org/en/jazzy/>

[10] About ROS  
<https://docs.ros.org/en/jazzy/About-ROS.html>

[11] ROS 2 Discovery  
<https://docs.ros.org/en/rolling/Concepts/Basic/About-Discovery.html>

[12] ROS 2 Quality of Service  
<https://docs.ros.org/en/humble/Concepts/Intermediate/About-Quality-of-Service-Settings.html>

[13] NVIDIA Isaac ROS  
<https://developer.nvidia.com/isaac/ros>

[14] Axis Thermal Cameras  
<https://www.axis.com/en-us/products/axis-q21-series>

[15] FLIR Thermal Security Cameras  
<https://www.flir.com/security/thermal-cameras/>

[16] Axis AI-powered Bispectral Camera Announcement, March 23, 2026  
<https://newsroom.axis.com/news/bispectral-camera-ai>

[17] Axis Radar-Video Fusion Camera  
<https://help.axis.com/en-US/axis-q1656-dle>

[18] Axis Radars and 2026 radar expansion  
<https://www.axis.com/products/radars>  
<https://newsroom.axis.com/en-us/news/innovative-radar>

[19] Intel RealSense D455  
<https://www.intelrealsense.com/depth-camera-d455/>

[20] Prophesee / Century Arks Event Camera  
<https://www.prophesee.ai/event-camera-century-arks-2/>

[21] NVIDIA TAO Toolkit  
<https://developer.nvidia.com/tao-toolkit>

[22] NVIDIA PeopleNet  
<https://docs.nvidia.com/tao/tao-toolkit-archive/5.2.0/text/model_zoo/cv_models/peoplenet.html>

[23] Hanwha Vision Wisenet 9 / 2nd Gen P and X Series  
<https://www.hanwhavision.com/en/news-center/1609693/>

[24] Hikvision AcuSeek NVR, June 9, 2025  
<https://www.hikvision.com/en/newsroom/latest-news/2025/hikvision-launches-groundbreaking-acuseek-nvr-redefining-video-retrieval-with-large-multimodal-ai-models/>

[25] Verkada Platform  
<https://www.verkada.com/>

[26] Verkada AI-powered Search and Unified Timeline  
<https://www.verkada.com/blog/ai-powered-search//>  
<https://help.verkada.com/verkada-cameras/historical-playback/history-player-search-and-unified-timeline>

[27] NVIDIA Metropolis  
<https://www.nvidia.com/en-us/autonomous-machines/intelligent-video-analytics-platform/>

[28] NVIDIA DeepStream SDK  
<https://developer.nvidia.com/Deepstream-sdk>  
<https://developer.nvidia.com/deepstream-download>

[29] AXIS Camera Station Pro  
<https://www.axis.com/en-us/products/axis-camera-station-pro>

[30] Hanwha Vision SightMind  
<https://support.hanwhavision.com/hc/en-001/articles/47256959849875-SightMind-How-do-I-access-the-service>  
<https://support.hanwhavision.com/hc/en-us/articles/47256903422483-SightMind-FAQ>

[31] Avigilon Unity  
<https://www.avigilon.com/unity>

[32] FLIR Total Security Solution / radar integration  
<https://www.flir.com/en-asia/discover/security/perimeter-protection/total-security-solution/>

[33] Wu et al., Open-Vocabulary Video Anomaly Detection, CVPR 2024  
<https://openaccess.thecvf.com/content/CVPR2024/html/Wu_Open-Vocabulary_Video_Anomaly_Detection_CVPR_2024_paper.html>

[34] Zanella et al., Harnessing Large Language Models for Training-free Video Anomaly Detection, CVPR 2024  
<https://openaccess.thecvf.com/content/CVPR2024/html/Zanella_Harnessing_Large_Language_Models_for_Training-free_Video_Anomaly_Detection_CVPR_2024_paper.html>

[35] Ding et al., ADA-Track, CVPR 2024  
<https://openaccess.thecvf.com/content/CVPR2024/html/Ding_ADA-Track_End-to-End_Multi-Camera_3D_Multi-Object_Tracking_with_Alternating_Detection_and_CVPR_2024_paper.html>

[36] Woo et al., MTMMC, CVPR 2024  
<https://openaccess.thecvf.com/content/CVPR2024/html/Woo_MTMMC_A_Large-Scale_Real-World_Multi-Modal_Camera_Tracking_Benchmark_CVPR_2024_paper.html>

[37] Huang et al., Online Video Understanding: OVBench and VideoChat-Online, CVPR 2025  
<https://openaccess.thecvf.com/content/CVPR2025/html/Huang_Online_Video_Understanding_OVBench_and_VideoChat-Online_CVPR_2025_paper.html>

[38] Shao et al., PURA: Test-Time Adaptation for RGB-T Tracking, CVPR 2025  
<https://openaccess.thecvf.com/content/CVPR2025/html/Shao_PURA_Parameter_Update-Recovery_Test-Time_Adaption_for_RGB-T_Tracking_CVPR_2025_paper.html>

[39] RGB-Th-Bench, 2025  
<https://arxiv.org/abs/2503.19654>

[40] ThermEval, 2026  
<https://arxiv.org/abs/2602.14989>
