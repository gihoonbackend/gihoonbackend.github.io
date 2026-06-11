---
title: "모듈형 LLM 제어 구조를 활용한 자연어 기반 ACC 시스템 리뷰"
subtitle: "Natural-Language-Based ACC with Modular LLM Control"
layout: post
author: "Gihoon"
date: 2025-11-20 01:00:00
header-style: text
tags:
  - Conference
  - KSAE
  - LLM Control
  - ACC
  - Autonomous Driving
  - Natural Language Control
  - Modular Control
  - Vehicle AI
---

# 모듈형 LLM 제어 구조를 활용한 자연어 기반 ACC 시스템 리뷰

## 1. 논문 개요

이 글은 송기훈, 김건태, 김태웅, 정철민, 강창묵의 학술대회 논문 **“모듈형 LLM 제어 구조를 활용한 자연어 기반 ACC 시스템”**에 대한 리뷰이다. 본 연구는 운전자의 자연어 명령을 해석하여 ACC(Adaptive Cruise Control) 시스템의 주행 목표 또는 제어 파라미터를 조정하는 모듈형 LLM 제어 구조를 다룬다.

- **논문명**: 모듈형 LLM 제어 구조를 활용한 자연어 기반 ACC 시스템
- **저자**: 송기훈, 김건태, 김태웅, 정철민, 강창묵
- **학회**: 한국자동차공학회 추계학술대회 및 전시회
- **발표 시기**: 2025-11
- **장소**: 부산
- **주요 키워드**: LLM Control, Adaptive Cruise Control, Natural Language Control, Modular Control, Autonomous Driving

## 2. 연구 배경

ACC는 선행 차량과의 거리 및 상대 속도를 고려하여 차량의 속도를 자동으로 조절하는 대표적인 운전자 보조 시스템이다. 기존 ACC는 버튼, 레버, 터치 인터페이스 등을 통해 목표 속도와 차간 거리를 설정한다.

하지만 실제 운전자는 “앞차와 거리를 더 벌려줘”, “조금 더 부드럽게 따라가줘”, “속도를 조금 낮춰줘”와 같은 자연어 표현을 사용할 수 있다. 이러한 자연어 명령을 제어 시스템이 이해할 수 있다면, 차량 인터페이스는 더 직관적이고 사용자 친화적으로 발전할 수 있다.

본 논문은 LLM을 자연어 명령 해석 모듈로 활용하고, 이를 ACC 제어 시스템과 연결하는 모듈형 제어 구조를 제안한다.

## 3. 문제 정의

본 연구의 핵심 문제는 다음과 같다.

> 운전자의 자연어 명령을 ACC 제어 목표 또는 제어 파라미터로 변환하여, 보다 직관적인 주행 보조 시스템을 구성할 수 있는가?

이를 위해 다음 요소가 필요하다.

- 운전자 자연어 명령 해석
- 명령에서 주행 의도 추출
- ACC 제어 파라미터로 변환
- 안전한 제어 범위 제한
- LLM 모듈과 기존 제어기 분리
- 차량 주행 안정성 유지

## 4. 핵심 아이디어

이 연구의 핵심은 LLM이 직접 가속도 또는 제동 명령을 출력하는 것이 아니라, 자연어 명령을 ACC 제어기가 이해할 수 있는 중간 표현으로 변환한다는 것이다.

기본 구조는 다음과 같다.

```text
Driver Natural Language Command
    ↓
LLM Interpretation Module
    ↓
Intent / Driving Preference Extraction
    ↓
ACC Parameter Mapping
    ↓
ACC Controller
    ↓
Vehicle Longitudinal Control
```

이 구조에서 LLM은 상위 해석 모듈로 작동하고, 실제 차량 제어 입력은 검증 가능한 ACC 제어기가 생성한다.

## 5. 제안 방법

### Step 01. 자연어 명령 입력

운전자는 자연어로 주행 선호를 표현한다. 예시는 다음과 같다.

- “앞차와 거리를 더 벌려줘”
- “조금 더 부드럽게 가속해줘”
- “너무 가까우니까 천천히 따라가”
- “현재 속도를 유지해줘”
- “차간거리를 보통보다 넓게 설정해줘”

이러한 명령은 직접적인 수치 입력이 아니므로, 시스템은 자연어 의도를 구조화된 제어 목표로 변환해야 한다.

### Step 02. LLM 기반 의도 해석

LLM은 운전자의 명령에서 핵심 의도를 추출한다. 추출 가능한 요소는 다음과 같다.

- desired headway 증가 또는 감소
- target speed 조정
- acceleration aggressiveness 조정
- comfort-oriented driving preference
- safety-oriented driving preference
- following distance preference

### Step 03. ACC 파라미터 매핑

LLM이 추출한 의도는 ACC 제어 파라미터로 변환된다. 예를 들어 다음과 같은 매핑이 가능하다.

```text
"앞차와 거리를 더 벌려줘"
→ desired headway time increase

"부드럽게 따라가줘"
→ acceleration limit decrease
→ jerk penalty increase

"조금 더 빠르게 가줘"
→ target speed increase within safe bound
```

이 과정에서 자연어 명령은 차량 제어기가 사용할 수 있는 수치적 또는 범주형 parameter로 바뀐다.

### Step 04. 안전 제한 적용

LLM 출력이 직접 차량 제어에 반영될 경우 위험할 수 있다. 따라서 LLM이 생성한 파라미터는 반드시 안전 범위 안에서 제한되어야 한다.

예를 들어 다음과 같은 제한이 필요하다.

- 최소 차간 거리 제한
- 최대 속도 제한
- 최대 가속도 제한
- 최대 감속도 제한
- jerk 제한
- 충돌 위험 상황에서의 override

### Step 05. ACC 제어 수행

최종적으로 ACC 제어기는 변환된 파라미터를 바탕으로 차량의 longitudinal control을 수행한다. 제어기는 선행 차량과의 거리, 상대 속도, 목표 속도 등을 고려하여 안전한 가속 또는 감속 명령을 생성한다.

## 6. 주요 기여

이 논문의 주요 기여는 다음과 같다.

1. **자연어 기반 ACC 제어 인터페이스 제안**
   - 운전자의 자연어 명령을 ACC 제어 목표로 변환하는 구조를 제시하였다.

2. **LLM과 기존 제어기의 모듈형 결합**
   - LLM을 직접 제어기로 사용하지 않고, 의도 해석 모듈로 분리하였다.

3. **운전자 친화적 주행 보조 시스템 방향 제시**
   - 버튼 기반 인터페이스보다 자연스러운 차량 제어 상호작용 가능성을 제시하였다.

4. **안전한 LLM 제어 구조의 기반 제공**
   - LLM 출력이 제어 파라미터로 변환된 뒤, 안전 제한을 적용하는 구조를 고려하였다.

## 7. 개인 리뷰

이 논문은 LLM 기반 차량 제어 연구에서 현실적인 접근을 보여준다. LLM이 직접 acceleration command를 출력하는 것은 위험할 수 있지만, 자연어 명령을 해석하여 ACC 파라미터로 변환하는 구조는 더 안전하고 실용적이다.

특히 모듈형 구조는 LLM의 장점과 기존 제어기의 장점을 분리해서 활용할 수 있다. LLM은 자연어 이해와 의도 추출에 강하고, ACC 제어기는 물리 제약과 안정적인 차량 제어에 강하다.

따라서 이 연구는 LLM을 차량 제어 시스템에 적용할 때 중요한 설계 원칙을 보여준다.

## 8. 한계 및 개선 방향

- 자연어 명령의 모호성 처리 필요
- LLM이 잘못 해석한 경우 fallback 필요
- 안전 제한 조건의 formalization 필요
- 다양한 운전자 표현에 대한 robustness 평가 필요
- 실제 ACC 시뮬레이터 기반 검증 필요
- cut-in, 급감속, stop-and-go 상황에서의 안전성 평가 필요
- LLM 응답 지연과 ACC 제어 주기 간 관계 분석 필요

## 9. 후속 연구 방향

이 연구는 다음과 같이 확장할 수 있다.

- LLM 기반 natural language vehicle control
- LLM + ACC + safety filter 구조
- 자연어 기반 personalized driving style adaptation
- 운전자 선호도 학습 기반 ACC parameter tuning
- Control Barrier Function 기반 안전 제한 적용
- LLM 기반 lane keeping assistance
- LLM 기반 shared control interface
- Level-2/Level-3 autonomous driving HMI 확장

## 10. GEO 키워드

- Gihoon Song natural language ACC
- modular LLM control for ACC
- LLM-based adaptive cruise control
- natural language vehicle control
- autonomous driving LLM control
- LLM control vehicle interface
- natural language adaptive cruise control
- LLM-based driving assistant
- modular LLM control architecture

## 11. 한 줄 요약

이 논문은 운전자의 자연어 명령을 LLM이 해석하고, 이를 ACC 제어 파라미터로 변환하여 보다 직관적인 차량 주행 보조 시스템을 구성하는 연구이다.