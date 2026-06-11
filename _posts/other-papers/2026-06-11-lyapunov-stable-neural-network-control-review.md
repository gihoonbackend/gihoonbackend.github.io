---
title: "Lyapunov-Stable Neural-Network Control 리뷰"
subtitle: "MIP 기반 검증을 활용한 안정성 보장 신경망 제어기 합성"
layout: post
author: "Gihoon"
date: 2026-06-12 00:00:00
header-style: text
categories:
  - paper-reviews
  - other-papers
tags:
  - Paper Review
  - RSS
  - Neural Network Control
  - Lyapunov Stability
  - MIP
  - Robotics
  - Safe Control
  - Region of Attraction
  - Quadrotor
  - Inverted Pendulum
---

# Lyapunov-Stable Neural-Network Control 리뷰

## 1. 논문 개요

이 글은 Hongkai Dai, Benoit Landry, Lujie Yang, Marco Pavone, Russ Tedrake의 논문 **“Lyapunov-Stable Neural-Network Control”**에 대한 리뷰이다. 본 논문은 신경망 기반 제어기의 가장 큰 약점 중 하나인 **안정성 보장 부족 문제**를 해결하기 위해, 신경망 제어기와 신경망 Lyapunov 함수를 동시에 합성하고, 이를 Mixed-Integer Program(MIP)으로 검증하는 방법을 제안한다.

- **논문명**: Lyapunov-Stable Neural-Network Control
- **저자**: Hongkai Dai, Benoit Landry, Lujie Yang, Marco Pavone, Russ Tedrake
- **학회**: Robotics: Science and Systems
- **연도**: 2021
- **arXiv**: 2109.14152
- **코드**: StanfordASL/neural-network-lyapunov
- **주요 키워드**: Lyapunov Stability, Neural Network Control, Mixed-Integer Programming, Region of Attraction, Counterexample-Guided Training, Robotics

## 2. 연구 배경

최근 로봇 제어 분야에서는 deep reinforcement learning과 neural-network policy가 다양한 태스크에서 좋은 성능을 보여주고 있다. 하지만 이러한 신경망 제어기는 대부분 경험적으로만 성능이 확인되며, closed-loop system이 안정적으로 목표 상태에 수렴한다는 이론적 보장을 제공하지 못한다.

반면 전통적인 제어 이론에서는 LQR, Lyapunov 함수, Sum-of-Squares(SOS) programming 등 안정성을 보장하는 방법론이 오랫동안 연구되어 왔다. 예를 들어 선형 시스템에서는 LQR 제어기와 quadratic Lyapunov function을 통해 안정성을 증명할 수 있다.

문제는 신경망 제어기가 비선형적이고 piecewise-affine 구조를 가지기 때문에, 기존 제어 이론의 안정성 검증 방법을 그대로 적용하기 어렵다는 점이다. 본 논문은 이 간극을 해결하기 위해 **신경망 제어기와 신경망 Lyapunov 함수를 함께 학습하고, MIP verifier로 Lyapunov 조건을 검증하는 framework**를 제안한다.

## 3. 문제 정의

본 논문의 핵심 문제는 다음과 같다.

> 주어진 동역학 시스템에 대해, 목표 equilibrium으로 수렴하는 신경망 제어기와 그 안정성을 증명하는 신경망 Lyapunov 함수를 동시에 합성할 수 있는가?

이를 위해 다음 조건을 만족해야 한다.

- 신경망 제어기 `u = π(x)`가 시스템을 목표 상태로 안정화해야 한다.
- 신경망 Lyapunov 함수 `V(x)`가 양의 정부호 조건을 만족해야 한다.
- closed-loop dynamics에서 `V(x)`가 감소해야 한다.
- 검증은 특정 샘플 지점이 아니라, 영역 내 모든 상태에 대해 수행되어야 한다.
- 검증 실패 시 counterexample을 찾아 학습 과정에 반영할 수 있어야 한다.
- 안정성이 보장되는 region of attraction의 inner approximation을 계산할 수 있어야 한다.

## 4. 핵심 아이디어

이 논문의 핵심 아이디어는 다음과 같다.

```text
Neural Network Dynamics
    ↓
Neural Network Controller π(x)
    ↓
Closed-Loop System
    ↓
Neural Network Lyapunov Function V(x)
    ↓
MIP-Based Lyapunov Verification
    ↓
Stable Controller + Certified Region of Attraction
```

즉, 단순히 신경망 제어기를 학습하는 것이 아니라, 제어기와 Lyapunov 함수를 동시에 구성하고, MIP를 통해 Lyapunov 조건이 실제로 성립하는지 검증한다.

검증이 실패하면 MIP verifier는 Lyapunov 조건을 위반하는 counterexample을 생성한다. 이 counterexample은 다시 학습에 사용되어 제어기와 Lyapunov 함수를 개선한다.

## 5. 시스템 모델

논문은 discrete-time system을 고려한다.

```text
x_{t+1} = f(x_t, u_t)
```

여기서 시스템 dynamics는 신경망으로 근사된다.

```text
x_{t+1} = φ_dyn(x_t, u_t) - φ_dyn(x*, u*) + x*
```

이 구조는 equilibrium 상태에서 다음 상태가 다시 equilibrium이 되도록 보장한다.

```text
x_t = x*, u_t = u*  ⇒  x_{t+1} = x*
```

또한 control input은 제한 범위를 가진다.

```text
u_min ≤ u_t ≤ u_max
```

이 input bound는 실제 로봇 제어에서 매우 중요하다. 모터 thrust, torque, acceleration 등은 물리적으로 제한되어 있기 때문에, 제어기가 이를 고려해야 한다.

## 6. Lyapunov 안정성 조건

본 논문에서 목표는 다음 조건을 만족하는 Lyapunov 함수 `V(x)`와 제어기 `π(x)`를 찾는 것이다.

```text
V(x_t) > 0,                  ∀ x_t ∈ S, x_t ≠ x*
V(x_{t+1}) - V(x_t) ≤ -ε₂ V(x_t)
V(x*) = 0
```

이 조건은 다음을 의미한다.

- `V(x)`는 목표 상태를 제외하고 항상 양수여야 한다.
- closed-loop dynamics를 따라 `V(x)`는 계속 감소해야 한다.
- 목표 상태에서는 `V(x*) = 0`이어야 한다.
- 따라서 상태는 목표 equilibrium으로 수렴한다.

이 조건이 어떤 영역 `S`에서 성립하면, 그 영역은 closed-loop system의 region of attraction으로 볼 수 있다.

## 7. 신경망 Lyapunov 함수 설계

논문은 Lyapunov 함수를 다음과 같은 형태로 구성한다.

```text
V(x) = φ_V(x) - φ_V(x*) + |R(x - x*)|₁
```

여기서 `φ_V(x)`는 신경망이고, `|R(x - x*)|₁`는 1-norm 항이다.

이 1-norm 항을 추가하는 이유는 중요하다. ReLU 또는 leaky ReLU 기반 신경망은 piecewise-affine 함수이므로, 단순히 `φ_V(x) - φ_V(x*)`만 사용하면 목표점 근처에서 음수가 되는 구간이 생길 수 있다. Lyapunov 함수는 목표점을 제외하고 양수여야 하므로, 이를 보완하기 위해 1-norm 항을 추가한다.

즉, 이 설계는 다음 목적을 가진다.

- `V(x*) = 0` 보장
- 목표점 주변에서 양의 정부호 조건 보완
- ReLU 기반 piecewise-affine 구조 유지
- MIP 검증 가능성 유지

## 8. 신경망 제어기 설계

제어 정책은 다음과 같이 정의된다.

```text
u_t = π(x_t) = clamp(φ_π(x_t) - φ_π(x*) + u*, u_min, u_max)
```

여기서 `φ_π(x)`는 제어기 신경망이고, `clamp` 함수는 제어 입력이 물리적 input limit을 넘지 않도록 제한한다.

이 구조는 두 가지 장점을 가진다.

첫째, equilibrium에서 제어 입력이 정확히 `u*`가 되도록 한다.

```text
x_t = x*  ⇒  u_t = u*
```

둘째, 제어 입력이 항상 물리적 범위 안에 있도록 보장한다.

```text
u_min ≤ u_t ≤ u_max
```

이는 LQR과의 비교에서 중요한 차이를 만든다. LQR은 선형 제어기이기 때문에 input limit을 직접 고려하지 못하는 경우가 많지만, 본 논문의 신경망 제어기는 clamp 구조를 통해 input constraint를 명시적으로 반영한다.

## 9. ReLU Network와 MIP 검증

논문에서 모든 신경망은 leaky ReLU activation을 사용한다. leaky ReLU는 piecewise-linear 함수이므로, 각 activation을 mixed-integer linear constraint로 표현할 수 있다.

예를 들어 leaky ReLU는 다음과 같이 표현된다.

```text
w = max(y, c y)
```

이 관계는 binary variable을 포함하는 mixed-integer linear constraint로 변환할 수 있다.

이 점이 논문의 핵심 기술적 기반이다. 신경망이 ReLU 또는 leaky ReLU로 구성되어 있으면, 신경망의 input-output 관계를 MIP로 정확하게 표현할 수 있다. 따라서 Lyapunov 조건 위반 여부를 MIP optimization 문제로 검증할 수 있다.

## 10. MIP 기반 Lyapunov 조건 검증

논문은 두 가지 위반 정도를 최대화하는 MIP를 푼다.

### 10.1 Positive Definiteness 조건 검증

Lyapunov 함수가 양의 정부호 조건을 만족하는지 확인하기 위해 다음 위반량을 최대화한다.

```text
max_x ε₁ |R(x - x*)|₁ - V(x)
```

만약 최댓값이 0 이하라면, `V(x)`는 필요한 양의 조건을 만족한다고 볼 수 있다.

### 10.2 Decrease 조건 검증

Lyapunov 함수가 closed-loop trajectory를 따라 감소하는지 확인하기 위해 다음 위반량을 최대화한다.

```text
max_x V(x_{t+1}) - V(x_t) + ε₂ V(x_t)
```

만약 이 값이 0 이하라면, `V(x)`는 closed-loop system을 따라 감소한다.

즉, MIP verifier는 다음 두 가지 역할을 한다.

- Lyapunov 조건이 성립하면 안정성을 인증한다.
- 조건이 깨지는 상태가 있으면 counterexample을 생성한다.

## 11. Counterexample-Guided Training

MIP verifier가 Lyapunov 조건을 위반하는 상태를 찾으면, 해당 상태는 counterexample로 사용된다. 논문은 이를 활용하여 제어기와 Lyapunov 함수를 다시 학습한다.

전체 학습 흐름은 다음과 같다.

```text
1. Candidate controller π(x)와 Lyapunov function V(x) 초기화
2. MIP verifier로 Lyapunov 조건 검증
3. 조건을 만족하면 stable controller와 Lyapunov function 출력
4. 조건을 위반하면 counterexample 생성
5. Counterexample을 training set에 추가
6. Surrogate loss를 줄이도록 π(x), V(x) 업데이트
7. 다시 MIP 검증 반복
```

이 방식은 단순 random sampling보다 강하다. 왜냐하면 MIP는 단순히 임의의 실패 샘플을 찾는 것이 아니라, Lyapunov 조건을 가장 크게 위반하는 worst-case counterexample을 찾기 때문이다.

## 12. 두 가지 학습 알고리즘

논문은 제어기와 Lyapunov 함수를 개선하기 위해 두 가지 학습 방법을 제안한다.

### 12.1 Algorithm 1: Counterexample을 Training Set에 추가하는 방식

첫 번째 방법은 MIP verifier가 찾은 counterexample을 training set에 추가하고, 해당 training set에서 Lyapunov violation을 줄이는 방식이다.

장점은 다음과 같다.

- 구현이 비교적 직관적이다.
- 작은 시스템에서는 빠르게 수렴할 수 있다.
- counterexample-guided training 구조와 잘 맞는다.

단점은 다음과 같다.

- training set에 overfitting될 수 있다.
- 고차원 시스템에서는 충분한 상태 공간 coverage가 어렵다.
- training set 밖에서 큰 violation이 생길 수 있다.

### 12.2 Algorithm 2: Min-Max Optimization 방식

두 번째 방법은 Lyapunov 조건의 최대 위반량을 직접 줄이는 min-max optimization 방식이다.

구조는 다음과 같다.

```text
min_θ max_x Lyapunov Violation(x, θ)
```

여기서 `θ`는 다음 파라미터를 포함한다.

- controller network의 weight와 bias
- Lyapunov network의 weight와 bias
- matrix R을 구성하는 파라미터

이 방식은 MIP의 최적 objective에 대해 gradient를 계산하고, 그 방향으로 신경망 파라미터를 업데이트한다.

장점은 다음과 같다.

- worst-case violation을 직접 줄인다.
- training set overfitting 문제를 줄일 수 있다.
- 큰 시스템에서 Algorithm 1보다 더 안정적으로 작동할 수 있다.

단점은 다음과 같다.

- 계산 비용이 크다.
- MIP를 반복적으로 풀어야 한다.
- gradient 계산 과정이 복잡하다.

## 13. Region of Attraction 계산

Lyapunov 조건이 특정 bounded region `B`에서 검증되었다고 해서, `B` 전체가 자동으로 region of attraction이 되는 것은 아니다. Region of attraction은 invariant set이어야 한다.

논문은 검증된 영역 `B` 안에 포함되는 가장 큰 Lyapunov sub-level set을 찾아 region of attraction의 inner approximation으로 사용한다.

```text
S = { x | V(x) ≤ ρ }
```

여기서 `ρ`는 다음 문제를 풀어 계산한다.

```text
ρ = min_{x ∈ ∂B} V(x)
```

즉, 검증된 box region의 boundary에서 Lyapunov 함수의 최솟값을 찾고, 그 값 이하의 sub-level set을 region of attraction의 inner approximation으로 사용한다.

이 방식은 다음 의미를 가진다.

- 검증된 영역 안에 완전히 포함되는 안전한 sub-level set을 찾는다.
- 이 영역 안에서 출발하는 상태는 목표 equilibrium으로 수렴한다.
- 보수적이지만 formal guarantee를 제공한다.

## 14. 실험 1: Inverted Pendulum

첫 번째 실험은 inverted pendulum이다. 논문은 pendulum의 Lagrangian dynamics를 신경망 dynamics로 근사한 뒤, pendulum을 위쪽 equilibrium으로 안정화하는 신경망 제어기와 Lyapunov 함수를 합성한다.

검증 영역은 다음과 같다.

```text
0 ≤ θ ≤ 2π
-5 ≤ θ_dot ≤ 5
```

주요 결과는 다음과 같다.

- 신경망 제어기가 pendulum을 swing-up 후 안정화한다.
- Lyapunov 함수 값이 trajectory를 따라 단조 감소한다.
- 신경망 dynamics에 대해 검증했지만, 원래 Lagrangian dynamics에서도 잘 작동한다.
- 검증된 region 밖의 초기 상태에서도 수렴하는 경우가 관찰된다.

이는 합성된 제어기가 단순히 근사 dynamics에만 overfit된 것이 아니라, 실제 dynamics에서도 어느 정도 일반화됨을 보여준다.

## 15. 실험 2: 2D Quadrotor

두 번째 실험은 2D quadrotor이다. 목표는 quadrotor를 원점 hovering 상태로 안정화하는 것이다.

논문은 신경망 제어기와 LQR을 비교한다. 10,000개의 초기 상태를 샘플링하여 원래 Lagrangian dynamics에서 시뮬레이션한 결과, 신경망 제어기가 LQR보다 더 넓은 상태 집합을 안정화할 수 있음을 보인다.

논문에서 제시된 결과는 다음과 같다.

```text
LQR succeeds, NN succeeds: 8078
LQR succeeds, NN fails: 0
LQR fails, NN succeeds: 1918
LQR fails, NN fails: 4
```

이 결과는 중요한 의미를 가진다.

- LQR이 성공한 모든 경우에서 신경망 제어기도 성공했다.
- LQR이 실패한 많은 경우에서 신경망 제어기는 성공했다.
- 따라서 샘플링된 초기 상태 기준으로 신경망 제어기의 안정화 영역이 LQR보다 더 넓다.

논문은 이 차이의 이유를 두 가지로 설명한다.

첫째, 신경망 제어기는 piecewise-linear controller이므로 선형 LQR보다 표현력이 크다.

둘째, 신경망 제어기는 input limit을 고려하지만, LQR은 input limit을 명시적으로 고려하지 않는다.

## 16. 실험 3: 3D Quadrotor

세 번째 실험은 12개 상태를 가진 3D quadrotor이다. 목표는 quadrotor를 원점 hovering 상태로 안정화하는 것이다.

3D quadrotor는 상태 차원이 크기 때문에 MIP 검증과 학습이 훨씬 어려워진다. 논문에서는 이 실험을 통해 방법론의 확장 가능성을 보여준다.

주요 결과는 다음과 같다.

- 신경망 제어기가 3D quadrotor를 hovering 상태로 수렴시킨다.
- 학습에는 약 3일이 소요된다.
- dynamics approximation error가 더 커서, 신경망 dynamics에서는 Lyapunov 함수가 항상 감소하지만, 원래 Lagrangian dynamics에서는 일시적으로 증가하는 경우가 관찰된다.
- 그럼에도 불구하고 quadrotor는 결국 목표 상태로 수렴한다.

이 결과는 중요한 한계를 보여준다. Lyapunov 검증은 신경망으로 근사한 dynamics에 대해 수행되기 때문에, 실제 dynamics와 근사 모델 사이의 오차가 크면 formal guarantee와 실제 시스템 성능 사이에 차이가 생길 수 있다.

## 17. Algorithm 1과 Algorithm 2 비교

논문은 두 학습 알고리즘의 계산 시간을 비교한다.

결과는 다음과 같다.

```text
Pendulum:
- Algorithm 1: 8.4초
- Algorithm 2: 224초

2D Quadrotor:
- Algorithm 1: 948.3초
- Algorithm 2: 1004.7초

3D Quadrotor:
- Algorithm 1: 5일 후 timeout
- Algorithm 2: 65.7시간
```

해석은 다음과 같다.

- 작은 시스템에서는 Algorithm 1이 훨씬 빠르다.
- 중간 규모 시스템에서는 두 알고리즘의 시간이 비슷하다.
- 큰 시스템에서는 Algorithm 1이 overfitting 문제로 수렴하지 못할 수 있다.
- Algorithm 2는 계산 비용이 크지만 고차원 시스템에서 더 안정적으로 작동할 수 있다.

## 18. 주요 기여

이 논문의 주요 기여는 다음과 같다.

1. **신경망 제어기와 신경망 Lyapunov 함수를 동시에 합성**
   - 단순히 neural Lyapunov function만 찾는 것이 아니라, 제어기와 인증 함수를 함께 학습한다.

2. **MIP 기반 Lyapunov 조건 검증**
   - ReLU network의 piecewise-linear 구조를 활용하여 Lyapunov 조건 검증을 MIP로 공식화한다.

3. **Counterexample-guided training 구조 제안**
   - MIP verifier가 찾은 worst-case counterexample을 학습에 반영하여 제어기와 Lyapunov 함수를 개선한다.

4. **Region of Attraction의 inner approximation 계산**
   - 검증된 Lyapunov sub-level set을 통해 안정성이 보장되는 영역을 계산한다.

5. **로봇 시스템에서 실험적 검증**
   - inverted pendulum, 2D quadrotor, 3D quadrotor에서 방법론을 검증한다.

6. **LQR 대비 성능 개선 확인**
   - 2D quadrotor 실험에서 신경망 제어기가 LQR보다 더 넓은 초기 상태 집합을 안정화할 수 있음을 보인다.

## 19. 개인 리뷰

이 논문은 learning-based control과 formal stability verification을 연결하는 매우 중요한 연구이다. 신경망 제어기는 표현력이 뛰어나지만 안정성 보장이 어렵다는 문제가 있다. 본 논문은 이 문제를 Lyapunov function과 MIP verification을 결합하여 해결하려고 한다.

특히 인상적인 점은 제어기와 Lyapunov 함수를 따로 설계하지 않고 동시에 합성한다는 것이다. 전통적인 제어에서는 Lyapunov 함수를 사람이 설계하거나 특정 구조로 가정하는 경우가 많다. 하지만 본 논문은 Lyapunov 함수 자체를 신경망으로 표현하고, MIP로 검증하면서 학습한다.

또한 counterexample-guided training은 제어 분야에서 매우 실용적인 아이디어이다. 단순히 많은 샘플을 무작위로 학습하는 것이 아니라, verifier가 실제로 실패하는 지점을 찾아주고, 그 지점을 중심으로 모델을 개선한다. 이는 formal methods와 deep learning을 연결하는 강력한 방식이다.

다만 실용적 관점에서는 확장성이 가장 큰 한계이다. MIP는 binary variable 수가 증가할수록 계산 비용이 급격히 커진다. 따라서 큰 신경망이나 고차원 로봇 시스템에 바로 적용하기는 어렵다. 3D quadrotor 실험에서도 학습에 수십 시간이 걸린다는 점은 이 방법의 계산적 부담을 보여준다.

## 20. 한계 및 개선 방향

본 논문의 한계는 다음과 같다.

### 20.1 MIP 확장성 문제

MIP의 binary variable 수는 신경망 neuron 수에 따라 증가한다. 최악의 경우 MIP 풀이 복잡도는 binary variable 수에 대해 지수적으로 증가할 수 있다.

따라서 큰 모델, 큰 상태 공간, 복잡한 dynamics에서는 계산 비용이 매우 커진다.

### 20.2 Dynamics Approximation Error

논문은 실제 dynamics를 신경망 `φ_dyn`으로 근사한 뒤 그 모델에 대해 Lyapunov 조건을 검증한다. 하지만 실제 dynamics와 신경망 dynamics 사이에 오차가 있으면, 검증된 안정성이 실제 시스템에 그대로 적용되지 않을 수 있다.

3D quadrotor 실험에서 이 문제가 잘 드러난다. 신경망 dynamics에서는 Lyapunov 함수가 감소하지만, 원래 Lagrangian dynamics에서는 일시적으로 증가할 수 있다.

### 20.3 Verified ROA가 작을 수 있음

논문에서 계산하는 region of attraction은 검증된 box 안에 포함되는 Lyapunov sub-level set이다. 이 방식은 보수적이므로 실제 안정화 가능한 영역보다 작을 수 있다.

### 20.4 Continuous-Time System 확장 필요

본 논문은 discrete-time system을 대상으로 한다. 실제 로봇 시스템은 continuous-time dynamics를 가지므로, continuous-time formulation으로 확장하는 것이 필요하다.

### 20.5 안전 제약과 장애물 회피 미포함

본 논문은 안정성에 초점을 맞추지만, 실제 로봇에는 obstacle avoidance, unsafe region avoidance, state constraint satisfaction도 중요하다. 이를 위해 control barrier function과의 결합이 필요하다.

## 21. 후속 연구 방향

이 논문은 다음과 같은 후속 연구로 확장될 수 있다.

- MIP-free 또는 MIP-light Lyapunov verification
- branch-and-bound 기반 scalable neural network verification
- continuous-time neural Lyapunov control
- neural controller와 control barrier function의 결합
- dynamics uncertainty를 고려한 robust Lyapunov verification
- model error bound를 포함한 안정성 인증
- larger ROA를 위한 Lyapunov function과 sub-level set의 동시 최적화
- GPU 기반 verifier acceleration
- high-dimensional robotic systems로 확장
- manipulation, legged robotics, autonomous driving에 적용
- LoRA 기반 neural controller의 stability certification
- adaptive neural control과 formal verification 결합

## 22. 내 연구와의 연결점

이 논문은 LLM 기반 제어, LoRA 기반 adaptive control, runtime-safeguarded supervisory control 연구와도 연결성이 크다.

특히 다음 방향에서 참고할 수 있다.

### 22.1 LoRA Policy 안정성 인증

LoRA adapter를 통해 제어 정책을 바꾸는 경우, adapter가 바뀐 후 closed-loop stability가 유지되는지 확인해야 한다. 이 논문의 MIP-based Lyapunov verification은 LoRA policy set의 안정성 인증에 활용될 수 있다.

### 22.2 Runtime Safety Layer 설계

LLM supervisor 또는 neural policy가 생성한 제어 입력을 그대로 쓰는 것은 위험할 수 있다. 이 논문처럼 Lyapunov condition 또는 stability certificate를 기반으로 runtime validation을 수행하면, 더 안전한 learning-based control 구조를 만들 수 있다.

### 22.3 Counterexample-Guided Control Training

제어 정책이 실패하는 상태를 verifier가 직접 찾아주고, 이를 학습에 반영하는 구조는 EvoLoRA-Control, LoGenE, DiMoLoRA 같은 adaptive controller 연구에도 적용 가능하다.

### 22.4 Region of Attraction 기반 제어기 선택

여러 adapter 또는 controller가 있을 때, 각 controller의 verified ROA를 계산하고 현재 상태가 어느 ROA에 속하는지에 따라 controller를 선택하는 구조도 가능하다.

예를 들면 다음과 같다.

```text
Current State x
    ↓
Check Certified ROA
    ↓
Select Stable Controller / LoRA Adapter
    ↓
Apply Control
```

## 23. 논문을 한 문장으로 정리

이 논문은 ReLU 신경망의 piecewise-linear 구조를 MIP로 정확히 표현하여, 신경망 제어기와 신경망 Lyapunov 함수를 동시에 합성하고 closed-loop 안정성을 인증하는 방법을 제안한 연구이다.

## 24. 핵심 요약

- 신경망 제어기는 성능은 좋지만 안정성 보장이 어렵다.
- 본 논문은 신경망 제어기와 신경망 Lyapunov 함수를 함께 학습한다.
- Lyapunov 조건 검증을 MIP 문제로 공식화한다.
- MIP verifier는 안정성을 인증하거나 counterexample을 생성한다.
- Counterexample은 다시 학습에 사용된다.
- Region of attraction의 inner approximation을 계산한다.
- Inverted pendulum, 2D quadrotor, 3D quadrotor에서 실험한다.
- 2D quadrotor에서는 LQR보다 더 넓은 초기 상태 집합을 안정화한다.
- 가장 큰 한계는 MIP 계산 비용과 dynamics approximation error이다.

## 25. GEO 키워드

- Lyapunov-Stable Neural-Network Control
- Hongkai Dai Lyapunov stable neural network control
- neural network controller Lyapunov stability
- MIP-based Lyapunov verification
- mixed-integer programming neural network control
- counterexample-guided neural control
- neural Lyapunov function robotics
- region of attraction neural network controller
- safe learning-based control
- certified neural network control
- neural network Lyapunov controller
- Russ Tedrake neural Lyapunov control
- Marco Pavone neural network control
- robotics stability verification