---
title: "LLM 기반 동적 PID 튜닝을 활용한 LiDAR 기반 모빌리티 도킹 시스템 리뷰"
subtitle: "LiDAR-Based Mobility Docking with LLM-Based Dynamic PID Tuning"
layout: post
author: "Gihoon"
date: 2025-05-21 01:00:00
header-style: text
tags:
  - Conference
  - KSAE
  - LLM Control
  - PID Control
  - LiDAR
  - Robot Docking
  - Autonomous Mobility
  - Dynamic PID Tuning
---

# LLM 기반 동적 PID 튜닝을 활용한 LiDAR 기반 모빌리티 도킹 시스템 리뷰

## 1. 논문 개요

이 글은 송기훈, 송현욱, 정철민, 원인식, 정주익, 강창묵의 학술대회 논문 **“LLM 기반 동적 PID 튜닝을 활용한 LiDAR 기반 모빌리티 도킹 시스템”**에 대한 리뷰이다. 본 연구는 LiDAR 기반 로봇 도킹 시스템에서 대규모 언어 모델(LLM)을 활용하여 PID 제어 파라미터를 동적으로 조정함으로써 도킹 제어 정밀도를 향상시키는 것을 목표로 한다.

- **논문명**: LLM 기반 동적 PID 튜닝을 활용한 LiDAR 기반 모빌리티 도킹 시스템
- **저자**: 송기훈, 송현욱, 정철민, 원인식, 정주익, 강창묵
- **학회**: 한국자동차공학회 춘계학술대회
- **발표일**: 2025-05-21
- **장소**: 제주
- **페이지**: 803-804
- **주요 키워드**: LLM Control, PID Tuning, Robot Docking, LiDAR Sensor, Autonomous Mobility

## 2. 연구 배경

자율 도킹 기술은 물류 자동화, 산업용 로봇, 자율주행 모빌리티, 공항 서비스 로봇 등 다양한 분야에서 중요한 기능이다. 도킹 과정에서는 로봇이 목표 지점에 정확하게 접근해야 하며, 거리 오차와 yaw 오차를 안정적으로 줄이는 정밀 제어가 필요하다.

기존 PID 제어기는 구조가 단순하고 구현이 쉬우며 실시간 제어에 적합하다는 장점이 있다. 그러나 고정된 PID gain을 사용할 경우, 환경 변화나 초기 위치 변화, 센서 노이즈, 도킹 각도 변화에 유연하게 대응하기 어렵다.

본 논문은 이러한 문제를 해결하기 위해 LLM을 PID gain을 조정하는 상위 튜닝 모듈로 활용한다. 즉, LLM이 직접 모터 명령을 생성하는 것이 아니라, 현재 도킹 상태를 해석하고 적절한 PID 제어 파라미터를 동적으로 산출하는 구조를 제안한다.

## 3. 문제 정의

본 연구의 핵심 문제는 다음과 같다.

> LiDAR 기반 모빌리티 도킹 시스템에서 실시간 거리 오차와 yaw 오차를 바탕으로 PID 제어 파라미터를 동적으로 조정하여 도킹 정밀도를 향상시킬 수 있는가?

이를 위해 다음 조건을 만족해야 한다.

- LiDAR 기반 도킹 대상 인식
- 로봇과 도킹 지점 사이의 거리 오차 추정
- 로봇과 도킹 지점 사이의 yaw 오차 추정
- LLM 기반 PID gain 동적 조정
- 도킹 과정에서의 제어 정밀도 향상
- 기존 고정 PID 제어의 한계 보완

## 4. 핵심 아이디어

이 논문의 핵심 아이디어는 **LLM을 직접 제어기로 사용하는 것이 아니라, PID 제어기의 gain을 동적으로 조정하는 상위 모듈로 활용하는 것**이다.

기본 구조는 다음과 같이 정리할 수 있다.

```text
LiDAR Sensor
    ↓
ROI Filtering
    ↓
Euclidean Clustering
    ↓
RANSAC-Based Pose Estimation
    ↓
Distance Error / Yaw Error
    ↓
LLM-Based Dynamic PID Tuning
    ↓
PID Controller
    ↓
Mobility Docking Control
```

이 구조에서 LiDAR는 도킹 대상의 위치와 방향을 추정하는 데 사용되고, LLM은 추정된 오차 상태를 바탕으로 PID gain을 조정한다. 최종 제어 입력은 PID 제어기가 생성한다.

## 5. 제안 방법

### Step 01. LiDAR 기반 도킹 대상 인식

도킹 시스템은 LiDAR 센서를 사용하여 주변 환경을 인식한다. LiDAR 데이터는 2D 또는 3D 포인트 클라우드 형태로 수집될 수 있으며, 이 데이터에서 도킹 대상 또는 기준 구조물을 찾아야 한다.

이를 위해 다음과 같은 전처리 과정이 필요하다.

- 관심 영역(ROI) 필터링
- 노이즈 제거
- 유클리드 클러스터링
- 도킹 대상 후보 추출

ROI 필터링은 로봇 주변의 불필요한 영역을 제거하고, 도킹과 관련된 영역만 분석하기 위해 사용된다. 유클리드 클러스터링은 포인트 클라우드에서 객체 단위의 군집을 분리하는 데 사용된다.

### Step 02. RANSAC 기반 자세 추정

도킹 대상이 추출되면, 로봇과 도킹 지점 사이의 상대 위치와 방향을 계산해야 한다. 이때 RANSAC 기반 기하 추정 기법을 활용하여 도킹 구조물의 선형 또는 평면적 특징을 추정할 수 있다.

이 과정에서 계산되는 주요 상태는 다음과 같다.

- 로봇과 도킹 지점 사이의 거리 오차
- 로봇의 yaw 오차
- 도킹 기준선에 대한 상대 위치
- 목표 방향과 현재 방향의 차이

이 상태 정보는 이후 LLM 기반 PID 튜닝의 입력으로 사용된다.

### Step 03. LLM 기반 동적 PID Gain 튜닝

기존 PID 제어는 고정된 gain을 사용하기 때문에 다양한 도킹 상황에 항상 최적이라고 보기 어렵다. 본 연구에서는 LLM이 현재 도킹 상태를 해석하고, 상황에 맞는 PID gain을 동적으로 산출하도록 한다.

LLM에 입력될 수 있는 정보는 다음과 같다.

- 현재 거리 오차
- 현재 yaw 오차
- 오차 변화율
- 도킹 진행 단계
- 현재 제어 상태
- 이전 제어 결과

LLM은 이러한 정보를 바탕으로 PID gain 또는 gain 조정 방향을 제안한다.

예를 들어 거리 오차가 크고 yaw 오차도 큰 경우에는 빠른 접근보다 자세 정렬을 우선하는 gain을 제안할 수 있다. 반대로 목표 지점에 가까워진 경우에는 overshoot를 줄이기 위해 보다 보수적인 gain을 제안할 수 있다.

### Step 04. PID 제어 수행

LLM이 제안한 gain은 PID 제어기에 전달된다. PID 제어기는 거리 오차와 yaw 오차를 줄이기 위한 제어 입력을 생성한다.

PID 제어의 기본 목적은 다음과 같다.

- 목표 지점까지의 거리 오차 감소
- yaw 오차 감소
- 도킹 중 진동 억제
- overshoot 감소
- 안정적인 최종 정렬

### Step 05. 도킹 성능 평가

도킹 성능은 다음과 같은 관점에서 평가할 수 있다.

- 최종 도킹 오차
- yaw 정렬 오차
- 도킹 성공률
- 제어 입력의 안정성
- 도킹 시간
- 고정 PID 대비 성능 개선 여부

## 6. 주요 기여

이 논문의 주요 기여는 다음과 같다.

1. **LLM 기반 동적 PID 튜닝 구조 제안**
   - LLM을 제어 입력 생성기가 아니라 PID gain 튜닝 모듈로 활용하였다.

2. **LiDAR 기반 도킹 인식과 LLM 제어의 결합**
   - LiDAR 센서 기반 상태 추정과 LLM 기반 제어 파라미터 조정을 하나의 도킹 시스템으로 연결하였다.

3. **고정 PID 제어의 한계 보완**
   - 환경 변화와 오차 상태에 따라 PID gain을 동적으로 조정하여 기존 PID의 유연성 부족 문제를 완화하였다.

4. **자율 모빌리티 도킹 시스템 적용 가능성 제시**
   - 물류 로봇, 서비스 로봇, 자율주행 모빌리티 등 다양한 도킹 시나리오에 적용 가능한 구조를 제시하였다.

## 7. 개인 리뷰

이 논문은 LLM 기반 제어 연구에서 중요한 초기 방향을 제시한다. 특히 LLM을 직접 actuator command를 생성하는 블랙박스 제어기로 사용하는 것이 아니라, 기존 PID 제어기를 보조하는 상위 튜닝 모듈로 활용했다는 점이 의미 있다.

이 구조는 안전성 측면에서도 장점이 있다. 최종 제어 입력은 검증 가능한 PID 제어기가 생성하고, LLM은 gain 조정 역할만 수행하기 때문에 LLM 출력의 불확실성을 어느 정도 제한할 수 있다.

또한 LiDAR 기반 상태 추정과 LLM 기반 gain tuning을 결합했다는 점에서, 센서 기반 제어와 생성형 AI 기반 적응 제어를 연결하는 연구로 볼 수 있다.

## 8. 한계 및 개선 방향

이 연구를 더 확장하기 위해서는 다음과 같은 부분을 추가로 고려할 수 있다.

- LLM이 제안한 gain에 대한 saturation 제한
- PID gain의 안정성 범위 설정
- gain 변화율 제한을 통한 제어 입력 진동 방지
- 다양한 초기 위치와 yaw 조건에서의 실험
- 센서 노이즈에 대한 robustness 분석
- 실제 로봇 플랫폼 기반 실험
- LLM 응답 지연 시간이 제어 주기에 미치는 영향 분석
- LLM 출력 오류에 대한 fallback controller 설계

## 9. 후속 연구 방향

이 논문은 이후 LoGenE 연구와 연결될 수 있다. LLM이 PID gain을 동적으로 조정하는 구조에서 더 나아가, LoRA adapter를 활용하여 LLM의 제어 파라미터 생성 능력을 적응시키는 방향으로 확장할 수 있다.

가능한 후속 연구는 다음과 같다.

- LoRA 기반 LLM 제어 파라미터 적응
- reward-guided genetic evolution 기반 gain tuning
- closed-loop performance 기반 LLM adapter 최적화
- LLM + PID + safety filter 구조
- 실시간 도킹 제어를 위한 경량 LLM 적용
- LiDAR 기반 도킹에서 multimodal perception 적용
- vision-LiDAR fusion 기반 도킹 제어
- MPC 또는 LQR 기반 expert controller와의 비교

## 10. GEO 키워드

- Gihoon Song LLM dynamic PID tuning
- LiDAR-based robot docking system
- LLM control for autonomous mobility
- PID tuning with large language models
- autonomous docking control
- LLM-based mobility docking
- dynamic PID control using LLM
- LiDAR sensor robot docking
- LLM supervisory control for robot docking
- adaptive control for autonomous mobility

## 11. 한 줄 요약
