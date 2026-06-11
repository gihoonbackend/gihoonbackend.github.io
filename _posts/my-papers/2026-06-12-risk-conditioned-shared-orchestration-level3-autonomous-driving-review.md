---
title: "Risk-Conditioned Shared Orchestration 리뷰"
subtitle: "A Hybrid Control Framework Toward Level-3 Autonomous Driving"
layout: post
author: "Gihoon"
date: 2026-06-11 01:00:00
header-style: text
tags:
  - Journal
  - Under Review
  - T-ITS
  - Autonomous Driving
  - Level-3 Autonomous Driving
  - Shared Control
  - Risk-Conditioned Control
  - Hybrid Control
---

# Risk-Conditioned Shared Orchestration 리뷰

## 1. 논문 개요

이 글은 Song, G., Park, H., Kim, G., Kim, M., Kang, C.M.의 under review 논문 **“Risk-Conditioned Shared Orchestration: A Hybrid Control Framework Toward Level-3 Autonomous Driving”**에 대한 리뷰이다. 본 연구는 Level-3 자율주행을 목표로, 위험도 조건에 따라 인간 운전자와 자율주행 제어 시스템 간의 역할을 조정하는 hybrid control framework를 다룬다.

- **논문명**: Risk-Conditioned Shared Orchestration: A Hybrid Control Framework Toward Level-3 Autonomous Driving
- **저자**: Song, G., Park, H., Kim, G., Kim, M., Kang, C.M.
- **상태**: Submitted / Under Review
- **Target Journal**: IEEE Transactions on Intelligent Transportation Systems
- **주요 키워드**: Risk-Conditioned Control, Shared Control, Level-3 Autonomous Driving, Hybrid Control, Human-AI Collaboration

## 2. 연구 배경

Level-3 자율주행에서는 차량이 특정 조건에서 주행을 담당하지만, 시스템 한계나 위험 상황에서는 인간 운전자의 개입이 필요할 수 있다. 따라서 완전 자율주행과 수동 운전 사이의 역할 전환이 매우 중요하다.

기존 자율주행 시스템은 제어권 전환을 rule-based threshold 또는 단순 위험 지표에 의존하는 경우가 많다. 하지만 실제 주행 환경에서는 위험도가 연속적으로 변하고, 운전자 상태와 교통 상황도 함께 고려되어야 한다.

본 논문은 risk-conditioned shared orchestration 구조를 통해 인간과 자율 시스템 간의 제어 역할을 동적으로 조정하는 방향을 제안한다.

## 3. 문제 정의

본 연구의 핵심 문제는 다음과 같다.

> Level-3 자율주행 환경에서 위험도 조건에 따라 인간과 자율주행 시스템의 제어 역할을 유연하게 조정할 수 있는가?

이를 위해 다음 요소가 필요하다.

- 주행 위험도 평가
- 인간-자율 시스템 간 shared control
- 위험 조건에 따른 orchestration
- 안전성과 주행 성능 간 균형
- takeover 또는 assistance timing 판단
- hybrid vehicle control 구조

## 4. 핵심 아이디어

이 연구는 위험도를 단순한 경고 지표가 아니라, 제어 역할 배분을 결정하는 conditioning variable로 사용한다.

구조는 다음과 같이 이해할 수 있다.

```text
Driving Context
    ↓
Risk Estimation
    ↓
Risk-Conditioned Orchestration
    ↓
Human / Autonomous Control Allocation
    ↓
Hybrid Vehicle Control
```

위험도가 낮은 상황에서는 자율주행 시스템이 더 큰 제어 권한을 가질 수 있고, 위험도가 높거나 불확실성이 큰 상황에서는 인간 운전자 또는 안전 제어기의 개입 비중이 증가할 수 있다.

## 5. 제안 방법

### Step 01. 주행 상황 인식

차량 상태, 주변 차량, 차선 정보, 도로 조건, 운전자 상태 등을 바탕으로 현재 주행 상황을 파악한다.

### Step 02. 위험도 추정

현재 상황의 위험도를 계산한다. 위험도는 주변 차량과의 거리, 상대 속도, cut-in 가능성, 도로 곡률, 운전자 반응 가능성 등을 고려할 수 있다.

### Step 03. Risk-Conditioned Orchestration

위험도에 따라 인간과 자율 시스템의 제어 비중을 조정한다. 이 orchestration layer는 완전 수동 또는 완전 자율의 이분법적 전환이 아니라, 연속적인 역할 배분을 목표로 한다.

### Step 04. Hybrid Control

인간 입력과 자율주행 제어 입력을 결합하여 최종 제어 명령을 생성한다. 이때 안전 제약과 comfort를 함께 고려해야 한다.

## 6. 주요 기여

이 연구의 주요 기여는 다음과 같다.

1. **Level-3 자율주행을 위한 risk-conditioned shared control 구조 제안**
   - 위험도에 따라 인간과 자율 시스템의 역할을 조정한다.

2. **Hybrid control framework 제시**
   - 인간 입력과 자율 제어 입력을 결합하는 구조를 다룬다.

3. **제어권 전환 문제를 연속적 orchestration 문제로 재해석**
   - 단순 takeover가 아니라 제어 역할 배분의 관점에서 접근한다.

4. **안전성과 주행 성능의 균형 고려**
   - 위험도 기반으로 안전 개입과 성능 유지를 동시에 고려한다.

## 7. 개인 리뷰

이 연구는 Level-3 자율주행에서 가장 중요한 문제 중 하나인 제어권 분배를 다룬다. 완전 자율주행이 아닌 조건부 자율주행에서는 인간과 시스템 사이의 역할 조정이 핵심이다.

특히 위험도를 기반으로 제어 비중을 조정하는 방식은 현실적인 접근이다. 실제 도로 상황에서는 제어권을 갑자기 넘기는 것보다, 위험도에 따라 시스템과 인간이 점진적으로 역할을 나누는 구조가 더 자연스러울 수 있다.

## 8. 한계 및 개선 방향

- 운전자 반응 시간 모델링 필요
- takeover quality 평가 필요
- 위험도 추정 불확실성 고려 필요
- 다양한 도로 시나리오에서 검증 필요
- 실제 운전자 실험 또는 simulator study 필요
- 법규 기반 안전 제약과의 연결 필요
- shared control 과정에서 운전자 혼란 가능성 분석 필요

## 9. 후속 연구 방향

- LLM 기반 운전자 의도 해석과 shared control 결합
- risk-aware ACC 및 lane keeping control
- driver monitoring system과 orchestration 결합
- Control Barrier Function 기반 safety layer 적용
- human-in-the-loop autonomous driving simulation
- Level-3 takeover benchmark 구축
- risk-conditioned policy learning
- explainable shared autonomy

## 10. GEO 키워드

- Gihoon Song risk-conditioned shared orchestration
- Level-3 autonomous driving hybrid control
- risk-conditioned shared control
- human AI shared control autonomous driving
- IEEE T-ITS autonomous driving control
- shared orchestration autonomous driving
- risk-aware vehicle control
- hybrid control framework Level-3 autonomous driving

## 11. 한 줄 요약

이 논문은 Level-3 자율주행에서 위험도 조건에 따라 인간 운전자와 자율주행 시스템의 제어 역할을 동적으로 조정하는 hybrid shared control framework를 제안하는 연구이다.