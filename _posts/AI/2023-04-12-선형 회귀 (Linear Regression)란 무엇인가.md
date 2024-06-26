---
title:  "선형 회귀 (Linear Regression)란 무엇인가?" 
excerpt: ""

categories:
  - AI
tags:
  - [인공지능, 머신러닝]

toc: true
toc_sticky: true
use_math: true
 
date: 2023-04-12
last_modified_at: 2023-04-12
header: 
  teaser: "assets/images/teaser/ai.png"

---


인공지능 공부를 하면서 처음부터 끝까지 다시 공부하며 정리하면 좋겠다 생각하여 글을 쓰게 되었습니다.

선형회귀에 대해 공부한 내용들을 정리하였습니다.

## 선형 회귀란?

만약 아래 표와같이 공부시간에 따른 성적에 대한 데이터가 주어졌다 가정해봅시다.

과연 8시간 다음에는 어떤 데이터가 나올지 예상해봅시다.

| 공부시간 | 성적 |
| --- | --- |
| 2시간 | 40점 |
| 4시간 | 60점 |
| 6시간 | 80점 |
| 8시간 | ??? |

다음 표를 그래프로 나타내면 다음과 같습니다.

![Untitled](https://user-images.githubusercontent.com/80799025/231465832-87d38ef6-fe87-421c-8ecb-0ae4faa7fcf3.png){: width="500" height="500"}{: .center}


산점도 그래프는 다음과 같은 결과를 나타냅니다. 

그럼 이 점들과 가장 알맞은 직선을 그려보도록 하겠습니다.

![Untitled 1](https://user-images.githubusercontent.com/80799025/231465847-b8eee921-a1c4-40e7-92f6-3935c02b0e2f.png){: width="500" height="500"}{: .center}


다음과 같은 직선을 얻을 수 있습니다. 이를 통해 8시간 공부 후 100점을 받을 것이라 예측하게 됩니다.

이와 같이 선형 회귀란 주어진 데이터를 통해 데이터와 가장 잘 맞는 직선을 찾는 과정이라 생각하시면 됩니다.

## 수식 알아보기

선형 회귀는 1차원 함수로 다음과 같이 수식으로 표현 할 수 있습니다.

>$h(x)=Wx+b$

X 인풋을 통해  아웃풋을 얻을 수 있습니다.

X를 독립변수, h(x)는 X에 영향을 받으므로 종속변수라 합니다.

이러한 수식 h(x)를 Hypothesis 라 합니다.

이 수식은 w값과 b 값에 따라 직선의 모양이 달리집니다.

선형 회귀의 목적은 이 W,b값을 찾는 것이고, 이 값들을 Hypothesis의 Parameter라 합니다.

## 오차 (Loss)

최적의 직선을 찾는 과정에서 얼마나 오차가 발생했는지 확인하고 반영하는 과정이 필요합니다.

먼저 어떤것을 오차라 부르는지 그래프로 알아봅시다.

![Untitled 3](https://user-images.githubusercontent.com/80799025/231465911-f032705c-da97-4848-bea8-f8fc95dbc587.png){: width="500" height="500"}{: .center}


위 그래프와 같이 직선과 데이터 값과의 거리가 Loss값이 됩니다.

이 Loss들의 평균을 계산하기 위해 여러 Loss Fuction이 이용되는데 여기서는 MSE(Mean Squared error)라는 Loss Function을 알아보도록 합시다.


>$MSE = \frac{1}{n}  \Sigma_{i=1}^n({h(x^i)}-{y^i})^2$


Loss Function값이 작으면 작을수록 오차가 작음을 나타냅니다.

다음 식을 간단히 설명해보겠습니다.

>${h(x^i)}-{y^i}$

h(x) 즉 직선과 데이터 (점) 간의 y값의 차. 즉 위에서 보았던 그래프의 Loss를 나타냅니다.

저 식의 Loss의 경우 음수가 될 수도, 양수가 될 수도 있습니다. 

따라서 평균을 구할 때 오차가 큼에도 불구하고 작은 Loss값의 평균이 나오는 상황이 발생하기도 합니다. 

이 문제를 해결하기 위해 식에 제곱을 해주어 해결합니다.


>$({h(x^i)}-{y^i})^2$

 n개의 데이터가 있을 경우 모두 합하여 데이터 갯수만큼 나누어 Loss값을 구하게 됩니다.


>$\frac{1}{n}  \Sigma_{i=1}^n({h(x^i)}-{y^i})^2$

Loss Function의 최솟값을 찾는것이 선형회귀의 목표입니다.


## 경사 하강법 (Gradient Decent)

이제까지 식들을 보고 Loss Fuction이 최솟값이 되도록 하는 W,b값을 찾는 방법을 알아보겠습니다.

경사 하강법은 최적화 알고리즘(Optimizer)중 하나로 적절한 W,b값을 찾아내는 **학습**을 진행합니다.

다음 식을 다시 봅시다.

>$h(x)=Wx+b$

여기서 W값은 기울기를 나타냅니다. 

아래 그래프를 확인해 봅시다.

![Untitled 3](https://user-images.githubusercontent.com/80799025/231483983-645046e2-09de-40d7-ad1c-75ea2b5e5c16.png){: width="500" height="500"}{: .center}

W값이 매우 커지거나 작아지면 큰 오차를 보여줍니다.

그래 W값과 Loss값 사이는 다음과 같은 2차함수 그래프로 나타낼 수 있습니다.

![Untitled 4](https://user-images.githubusercontent.com/80799025/231484016-a8cbf121-ec31-4cac-a314-26e7e5fc6ffd.png){: width="500" height="500"}{: .center}

W와 Loss간의 관계를 직관적으로 볼 수 있습니다. 최적값에서 멀어질 수록 Loss가 커지는것을 확인 할 수 있습니다.

![Untitled 5](https://user-images.githubusercontent.com/80799025/231484032-f79556f0-1b17-446f-9a9f-663386374cbe.png){: width="500" height="500"}{: .center}

여기서 Loss가 최소가 되는 W의 값을 학습을 통해찾아야 합니다.

학습은 초기의 임의의 W값을 지정한 후, 접선의 기울기가 0(최솟값)이 되도록 W값을 수정해 나갑니다. 그래프를 보면 최적값에 가까울수록 기울기 값은 작아지는것을 알 수 있습니다.


따라서 기울기가 최소가 되는 값을 찾는것이 경사하강법입니다.

W를 구하는 경사하강법의 식은 다음과 같습니다.


>$W=W-\alpha\frac{\partial }{\partial W}Loss(W)$

Loss Function의 기울기값과 α값을 곱하여 W값을 갱신하는 것입니다.


여기서 **α**값은 학습률 (Learning Late)를 나타냅니다.

학습률이란 얼마나 값을 크게 변경시킬지 설정하는 것입니다.

만약 학습률이 크다면 빠르게 값을 찾을것 같지만 실제로는 제대로된 학습을 할 수 없습니다.


아래의 예시를 보겠습니다.

![Untitled 6](https://user-images.githubusercontent.com/80799025/231484048-4d141851-715a-4696-a871-d61a6c098bb1.png){: width="500" height="500"}{: .center}

순차적으로 W의 값을 찾는것이 아니라 W의 값이 발산하게 됩니다.

반대로 학습률이 너무 작다면 학습이 오래걸리는 단점이 존재하겠죠.

따라서 학습률 설정에 있어 적당한 α값을 설정해줘야 합니다.


옵티마이저를 통한 학습 과정을 통해 최종적으로 W,b값을 구하여 최적의 직선을 구할 수 있습니다.
