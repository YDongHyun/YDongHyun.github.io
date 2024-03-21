---
title:  "로지스틱 회귀 (Logistic Regression)란 무엇인가?" 
excerpt: ""

categories:
  - AI
tags:
  - [인공지능, 머신러닝]

toc: true
toc_sticky: true
use_math: true

date: 2023-04-13
last_modified_at: 2023-04-13

---


이번 글에서는 로지스틱 회귀 (Logistic Regression)에 대해 공부한 내용을 정리하였습니다.

## 로지스틱 회귀 (Logistic Regression)

다음과 같은 데이터가 있다 가정해봅시다.

점수에 따른 합격 불합격을 나타낸 표입니다.

| 점수 | 합/불 |
| --- | --- |
| 30 | 불합격 |
| 40 | 불합격 |
| 50 | 불합격 |
| 60 | 합격 |
| 70 | 합격 |
| 80 | 합격 |

이 데이터는 `합격, 불합격` 이라는 2가지 결과 값을 가지고 있습니다.

이렇게 두가지 중 하나로 결정하는 것을 **이진분류**라 합니다.

![Untitled](https://user-images.githubusercontent.com/80799025/231719987-4734c94b-d94a-4c63-ad8b-79cc2b20fbd4.png){: width="500" height="500"}{: .center}

다음 그래프를 봅시다.

그래프를 보면 일정 점수를 기점으로 `0(불합격), 1(합격)`을 나타내는 것을 확인할 수 있습니다.

이 그래프를 이진분류를 하려면 어떻게 해야할지 생각해봅시다.

저번에 공부했던 선형회귀를 이용한다 해도 분류를 할 순 없습니다.

데이터를 분류하기 위해서는 S자 형태의 함수를 사용해야함을 짐작할 수 있습니다.

**로지스틱 회귀**는 이러한 이진분류 문제를 해결하기 위한 알고리즘입니다.

그렇다면 어떻게 이 문제를 해결할 수 있는지 알아봅시다.

## 시그모이드 (Sigmoid) 함수

위의 문제를 해결하기 위해 제기된 함수입니다.

이런 식을 Hypothesis라 불렀죠

시그모이드 함수의 식은 다음과 같습니다.


>$h(x)= \frac{1} {1+ e^{-(Wx+b)}} = \sigma (Wx+b)$

선형 회귀와 마찬가지로 이 식에서 W,b값을 찾는 것이 목표입니다.

다음 Sigmoid 함수의 개형은 대략 다음과 같습니다.

![Untitled 1](https://user-images.githubusercontent.com/80799025/231720064-1474c78e-aa9b-4535-8fd8-212271afbed3.png){: width="500" height="500"}{: .center}

함수의 출력값은 0~1사이의 값을 가집니다.

이 함수를 이용하여 일정 값을 기준으로 이진분류 할 수 있도록 합니다.

이 식에서 W와 b는 어떤 것을 나타내는지 그래프로 알아봅시다.

다음 식을 MatLab으로 구현한 후 W값을 변화시켜 보겠습니다.

![Untitled 2](https://user-images.githubusercontent.com/80799025/231720143-24543884-661e-412a-9271-b35ba656050d.png){: width="500" height="500"}{: .center}

W값의 변화에 따른 그래프의 변화 모습입니다.

W값이 커질수록 경사는 가팔라지고, 반대로 작아질수록 완만해지는 것을 볼 수 있습니다.

W값에 따라 그래프의 경사도가 달라지는것을 확인 할 수 있습니다.

따라서 W값은 그래프의 경사도를 나타냅니다.

다음은 b값을 변화시켜 보겠습니다.

![Untitled 3](https://user-images.githubusercontent.com/80799025/231720165-a5d3c481-214f-4495-877f-72e4251d658f.png){: width="500" height="500"}{: .center}

b값이 변함에 따라 그래프가 이동함을 볼 수 있습니다.

## 비용 함수(Cost Function)

비용 함수는 오차의 평균값을 구하는 함수입니다.

비용함수를 최소화 해 최적의 W,b값을 찾아야 합니다.

그전에 최솟값에 대해 잠깐 설명하겠습니다.

만약 선형 회귀에 사용한 비용함수(MSE) 를 그대로 사용한다면 다음과 같은 현상이 발생합니다.

![Untitled 4](https://user-images.githubusercontent.com/80799025/231720204-22b8e651-fcc8-457d-b7f0-8f55a9be1edd.png){: width="500" height="500"}{: .center}

최솟값을 구할때는 전역(Global) 최솟값, 지역(Local) 최솟값이 존재합니다.

저번에 경사하강법은 기울기가 0이 되는 지점을 찾아 최적의 값을 찾는것이라 하였습니다.

Convex 함수(최솟값이 하나인 함수)에서는 바른 값을 찾을수 있습니다. 

위 그래프의 경우에는 기울기가 0이되는 지점이 여러개이므로 잘못된 값을 찾을수도 있습니다. →Non-Convex 함수

따라서 로지스틱 회귀에서는 다른 비용함수를 이용합니다.

>$Cost(W)= -\frac{1}{m} \Sigma_{i=1}^my^ilog(\hat{y}^i) + (1-y^i)log(1-\hat{y}^i)$

왜 이러한 비용함수를 사용하는지 알아봅시다.

시그모이드 함수는 0~1사이의 아웃풋을 가집니다.

![Untitled 5](https://user-images.githubusercontent.com/80799025/231720235-e7198f72-fa25-43a5-ad8d-8477ae356da4.png){: width="500" height="500"}{: .center}

다음과 같은 그래프로 Cost를 표현 할 수 있습니다.

정답값이 0일때 예측값이 1에 가까울수록 Cost는 커지고, 반대로 정답값이 1일때 예측값이 0에 가까울수록 Cost가 커집니다.

>$y^ilog(\hat{y}^i) + (1-y^i)log(1-\hat{y}^i)$

따라서 y(정답)값이 0일경우 뒤의 식만 남고 1일 경우에는 앞의 식만 남게 됩니다.

>$Cost(W)= -\frac{1}{m} \Sigma_{i=1}^my^ilog(\hat{y}^i) + (1-y^i)log(1-\hat{y}^i)$

그리고 모든 오차의 평균을 구합니다.

>$W=W-\alpha\frac{\partial }{\partial W}Cost(W)$

선형 회귀와 마찬가지로 경사하강법을 통해 W와 b의 최적의 값을 찾게 됩니다.
