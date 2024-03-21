---
title:  "[CS231n] Training Neural Networks part 2 정리" 
excerpt: ""

categories:
  - CS231n
tags:
  - [인공지능]

toc: true
toc_sticky: true
 
date: 2023-05-04
last_modified_at: 2023-05-04

---
이 글은 Lecture 7 | Training Neural Networks II 강의를 듣고 내용을 정리하였습니다. 

내용및 이미지는 다음 강의에서 발췌하였습니다.

## Optimization (최적화)

Optimization은 신경망 훈련에서 핵심적인 문제입니다.

손실함수를 통해 가중치의 적합성을 판별하고 최적화를 통해 loss값이 최적이 되도록 하는것이 목적입니다.

### SGD

![Untitled](https://user-images.githubusercontent.com/80799025/236252622-73458fdf-9e5a-4aec-860f-ace9d1620837.png)

그림의 오른쪽을 보면 2차원으로 표현된 그래프가 있습니다. 

W_1, W_2는 각각 가중치이고, 색상은 loss값에 해당합니다. (붉은색에 가까울수록 낮음)

여기서 W_1, W_2에 대해 최적화하여 loss값이 제일 작은 값을 찾아야합니다.

지금까지는 경사하강법, 확률적 경사하강법(SGD)과 같은 간단한 알고리즘을 활용하였습니다. 

하지만 SGD 알고리즘은 문제가 있습니다.

![Untitled 1](https://user-images.githubusercontent.com/80799025/236252645-c8e0a8d2-8166-4f81-a247-147cb003fb4b.png)

빨간점에서 중앙(최적점)으로 간다 생각해봅시다.

위 빨간점에서 수평 방향에 대해 둔감하고 반대로 손실은 수직 방향 변화에 매우 민감합니다.

![Untitled 2](https://user-images.githubusercontent.com/80799025/236252661-e29bba77-8be1-4fa3-9135-40f701a18460.png)

이 함수는 gradient의 방향이 최솟값을 향하지 않을 수 있습니다. gradient를 계산하고 다음 단계로 넘어갈 때 민감도가 높은 수직방향으로는 빠르게, 둔감한 수평방향은 느리게 움직여 그림과  같이 지그재그로 이동 하는 것을 볼 수 있습니다.  

다시 말하면 최적점으로 이동할 때 빨간점은 수직 방향으로 빠르게 최적점으로 이동이 가능하지만 수평방향으로는 느리게 수렴하거나 수렴하지 않을 수 있습니다.

결과적으로 SGD는 학습속도가 느리고 optimize 과정에서 최적점에 수렴하지 않는 문제가 발생하 수 있습니다. 차원이 높아질수록 이 문제가 발생할 확률이 높아집니다.

![Untitled 3](https://user-images.githubusercontent.com/80799025/236252691-af7337d5-b08e-461a-8249-87e1788cf2f4.png)

SGD의 다른 문제점은 Local minima(지역 최솟값)과 Saddle Points(안장점)에 도달할 경우 멈추게 된다는 것입니다.

지역 최솟값이나 안장점은 기울기가 0 이므로 현재 위치에서  gradient방햐이 0이라면 SGD가 더이상 연산을 진행하지 않습니다.

고차원에서는  Saddle Points(안장점)이 많이 생겨 이런 오류가 발생할 가능성이 높아집니다.

또한 안장점 근처에서도 gradient가 매우 작아 SGD가 매우 느려지는 문제도 발생합니다.

![Untitled 4](https://user-images.githubusercontent.com/80799025/236252713-0a76bfa1-e02f-4ba2-b30c-779e8f7e66b9.png)

마지막으로 SGD의 문제점은 Stochastic, 즉 무작위성입니다.

SGD는 무작위로 일부 데이터만을 선택해서 학습을 하는데 전체 데이터가 아닌 일부 데이터만을 사용하므로 gradient의 근사치를 얻는다는 점입니다.

근사치로 연산하기 때문에 최적점까지 가는데 더 오랜시간이 걸릴 수 있다는 문제가 있습니다.

### SGD Momentum

![Untitled 5](https://user-images.githubusercontent.com/80799025/236252729-6046ed89-ae78-4261-8739-bc1402fb874d.png)

지금까지의 문제점들을 해결할 간단한 방법이 있습니다. 바로 SGD와 Momentum을 함께 사용하는 방법입니다.

왼쪽 식을 보면 일반 SGD를 볼 수 있고, 오른쪽에서는 SGD+Momentum을 볼 수 있습니다.

이 수식은 속도를 유지하며 예측된 gradient를 속도에 더한 후 gradient 방향이 아닌 속도의 방향으로 이동합니다. 참고로 속도의 initialization은 거의 항상 0입니다.

여기서 `rho`는 friction constant(마찰 상수)로 속도를 감소시킵니다.  rho는 일반적으로 0.9정도의 높은 값으로 설정됩니다.

정리하면 현재 속도(vx)를 가져와 마찰(rho)로 감쇠시키고 gradient를 추가한 후 속도의 방향으로 이동한다는 것입니다. 

![Untitled 6](https://user-images.githubusercontent.com/80799025/236252778-c0a1471c-d538-4452-bec7-63035b6cb66e.png)

SGD+Momentum을 “공이 언덕을 굴러내려가면서 속도를 올린다”라 생각하시면 됩니다.

속도가 있으면 local minima를 통과하더라도 지나칠 수 있습니다. 안장점 또한 주변의 기울기가 매우 작더라도 내리막을 굴러 내려가면서 얻은 속도로 인해 지나칠 수 있습니다.

오른쪽 그림의 SGD+Momentum(파란색)과 SGD(검은색)을 비교해보면 훨씬 매끄럽고 속도도 빠른것을 볼 수 있습니다.

![Untitled 7](https://user-images.githubusercontent.com/80799025/236252789-786252cd-1ecb-4887-82ee-30d58c8f0f01.png)

SGD+Momentum은 그림과 같이 벡터로 생각할 수 있습니다

빨간점은 현재 위치를 나타내며 빨간 화살표는 gradient방향, 초록 화살표는 속도의 방향입니다.

Momentum을 업데이트 할 때 두 벡터의 합에 따라 이동합니다. 이 방법을 이용하면 gradient 근사치의 Noise를 잡는데 도움이 됩니다.

오른쪽 그림의 Nesterov Momentum은 빨간점에서 속도가 이동할 방향으로 이동한 후 그 지점에서 gradient를 계산합니다. 그 후 다시 빨간점으로 돌아와 두 벡터를 합친 방향으로 이동합니다.

이 방법은 SGD+Momentum보다 gradient 정보를 더 활용이 가능하여 convex함수에서 빠르게 수렴 가능합니다.  하지만 Neural Networks같은 non-convex에서는 보증이 약간 상실됩니다. 

### Nesterov Momentum

![Untitled 8](https://user-images.githubusercontent.com/80799025/236252811-be09e245-582f-4426-b0aa-85b71cbcc6d8.png)

SGD+Momentum은 같은 위치에서 loss와 gradeint를 구했습니다. 하지만 Nesterov Momentum은 같은 위치에서 값을 구하는 것이 아닌 다른 위치에서 구하여 사용하기 불편합니다.

그래서 Nesterov Momentum 식에 ρvt를 더하고 재정렬하면 loss와 gradient를 동일한 지점에서 평가할 수 있도록 합니다.

오른쪽 코드를 보면 첫 단계에서는 현재의 Momentum을 업데이트 하는 일반적인 SGD+Momentum 방식과 같습니다.

두번째 단계에서는 파라미터 벡터 x를 업데이트하게 됩니다.  방정식을 자세히 보면 현재 지점에서 현재 Momentum과 이전 Momentum 사이의 차이를 더합니다.

여기서 Nesterov Momentum은 현재와 이전 Momentum 사이의 오류 수정 사항을 통합합니다.

![Untitled 9](https://user-images.githubusercontent.com/80799025/236252823-6062e234-e8e5-4b87-bc9a-184f2f1a8e79.png)

위 이미지(gif인데 강의를 참고하시면 되겠습니다.)  SGD, SGD+Momentum 및 Nesterov Momemtum의 성능을 시각화 한 것입니다. SGD는 천천히 최소값으로 진행하는 반면, SGD+Momentum 과 Nesterov Momentum은 속도를 높여 최소값을 지나치고 그 이후에 오차를 수정하면서 최소값에 다가가는 모습을 보입니다.

또한 Nesterov는 기본 Momentum과 달리 보정요소 때문에 Overshooting이 극단적으로 일어나지 않습니다. 따라서 기본 Momentum보다 더 안정적으로 최적값으로 수렴하 수 있습니다.

### AdaGrad

AdaGrad 알고리즘은 optimization 과정에서 발생하는 모든 gradient의 제곱값을 누적하여 저장합니다. 

![Untitled 10](https://user-images.githubusercontent.com/80799025/236252846-a637ab5e-798a-4362-b5c6-0620dafc1a6b.png)

식을 자세히 보면 매 학습 단계에서 누적된 gradient 제곱값을 나누어 learning rate를조정합니다.

그래서 매우 높은 condition number를 가진 문제에서는 위 식의 분모가 계속 증가하여 매우 커질것입니다. 따라서 실제 학습에는 유용하지 않을 수 있습니다.

![Untitled 11](https://user-images.githubusercontent.com/80799025/236252861-9885669a-3676-4cb9-a66d-84a8635ef7ad.png)

Adagrad는 만약 2개의 좌표를 가지고 있는데 하나는 높은 gradeint를 가지고 하나는 낮은 gradient를 가질 때, 작은 gradient는 합을 추가할수록 작은 숫자로 나누기 때문에 진행이 가속화 됩니다. 반대로 gradient가 높은곳에서는 큰 숫자로 나누기 때문에  learning rate가 작아져 진행이 느려질것입니다.

또한 AdaGrad에서  제곱 gradient 추정치를 계속 업데이트 하면 추정치는 계속 단조 증가하기 때문에 learning rate가 시간이 지날수록 작아집니다.

Convex에서 수렴하려면 최솟값에 접근할 때 leraning rate가 줄어 좋지만, Non_Convex에서는 문제가 됩니다. Saddel Point(안장점)를 만나면 Adagrad가 더이상 업데이트를 하지 못할 수 있습니다.

### RMSProp

RMSProp은 AdaGrad의 약간 다른 버전으로 AdaGrad의 문제를 어느정도 해결해줍니다.

![Untitled 12](https://user-images.githubusercontent.com/80799025/236252882-431d87c1-d428-44f2-98e5-1a177050ed60.png)

RMSProp은 AdaGrad와 동일하게 제곱된 gradient의 추정치를 사용하지만, 제곱 추정치가 계속 누적되는 대신에 decay(감쇠)되도록 합니다. 

RMSProp은 gradient를 계산한 후 현재 gradient의 제곱 추정치에 decay_rate(감쇠율)을 곱한 후 `(1-decay_rate)`를 하여 더합니다. 이 식을 이용하면 시간이 지남에 따라 추정치가 서서히 줄기 때문에 문제를 해결할 수 있습니다.

![Untitled 13](https://user-images.githubusercontent.com/80799025/236252905-a987bc1b-45a6-4ceb-9403-a4d52e31c996.png)

위 그림을 봅시다.(움직이는 gif는 강의를 참고해 주세요)

검은색은 SGD를, 파란색은 SGD+Momentum, 빨간색은 RMSProp의 Optimization을 시각화 한 것입니다. RMSProp과 SGD+Momentum이 SGD보다 성능이 우수한 것을 볼 수 있습니다.

SGD+Momentum의 경우는 최솟값을 넘었다가 다시 수렴하는 모습을 보이지만 RMSProp은 모든 차원에서 거의 동일하게 진행을 하도록 조정한 모습을 보입니다.

### Adam

Momentum에서는 gradient를 속도에 더해 누적하고 그 방향으로 이동하는 개념을 보았고, AdaGrad와 RMSProp에서는 gradient의 제곱 추정치를 누적하여 나누는 개념을 보았습니다.

Adam은 위의 두 알고리즘을 함께 사용하자는 아이디어에서 나오게 되었습니다.

![Untitled 14](https://user-images.githubusercontent.com/80799025/236252926-4a4a1a66-8b32-4ba7-973f-b1690f5e050f.png)

Adam의 간단한 코드입니다. first_moment와 second_moment를 0으로 초기화 해준 후 Momentum 을 이용하여 first_moment를 구합니다. 그리고 AdaGrad를 이용하여 second_moment를 구합니다.

이제 업데이트를 수행 할 때 learning rate 에 first_moment를 곱하고 second_moment의 제곱으로 나누어 줍니다.

하지만 이 식에는 약간의 문제가 있습니다. 처음에 second_moment를 0으로 초기화 하였고, 일반적으로 beta2(decay_rate)는 1에 매우 가까운 값이므로 second_moment를 업데이트를 하더라도 0에 가까운 값이 됩니다.

이렇게 업데이트를 수행할 때 second_moment의 제곱은 매우 작은 수가 되므로 매우 크게 이동하게 될 것입니다.

![Untitled 15](https://user-images.githubusercontent.com/80799025/236252937-38ff1e63-10b9-4ced-80c1-78890dfecea5.png)

Adam에서 일어나는 매우 크게 이동하는 문제는 bias correction항을 추가하여 해결할 수 있습니다.

위 식에서 first, second_moment를 업데이트 한 후 t(current time step)를 포함한 unbiased된 추정치를 얻을 수 있습니다.

이제 first, second_moment 추정치 대신에 이러한 unbiased된 추정치를 사용하여 업데이트를 진행합니다. 이 방법을 통해 위의 오류를 해결하 수 있습니다.

Adam은 매우 우수한 Optimization 알고리즘이며`beta one = 0.9 , beta two = 0.999, learning rate=1e-3 or 5e-4` 일때 대부분의 모델에서 좋은 성능을 보입니다.

![Untitled 16](https://user-images.githubusercontent.com/80799025/236252954-e01603c5-3e14-4c14-803e-1319dcfefbc8.png)

위 그림을 봅시다.(움직이는 gif는 강의를 참고해 주세요)

Adam은 SGD+Momentum과 RMSProp을 합친 알고리즘이기 때문에 각각의 특성을 가지고 있습니다.

Adam은 SGD+Momentum처럼 최솟값을 약간 초과하고,  RMSProp처럼 모든 차원에서 같은 속도로 진행하여 곡선의 형태를 띕니다.

### Learning rate

![Untitled 17](https://user-images.githubusercontent.com/80799025/236252970-8838b501-0842-47ae-bd08-d00b0194080d.png)

저번 강의에서 봤듯이 learning rate가 너무 높으면 explode하고, 반대로 너무 낮으면 수렴하는데 오래 걸립니다.

실제로 learning rate는 고정된 값일 필요가 없고 시간이 지남에 따라 learning rate를 점차 줄이는 것이좋습니다. 이 방식을 이용하면 왼쪽 그래프의 여러 곡선들의 효과를 조합해 좋은 특성을 얻을 수 있습니다. 

이 방식에는 100000번 iteration할때마다 일정한 비율로 learning rate를 감소시키는 step decay나 지속적으로 learning rate를 감소시키는 exponential decay등 여러 방법들이 있습니다.

![Untitled 18](https://user-images.githubusercontent.com/80799025/236252980-7835c3a9-c75b-4562-af31-be87acaab6a1.png)

이 그림은 ResNet 논문 등 여러 논문에서 볼 수 있는데 Loss가 내려가다 급격히 감소하는 모습을 보입니다.  이 지점이 의미하는 것은 learning rate를 감소시키고 다시 학습을 시작한다는 것입니다.

여기서 learning rate 의 decay는 두번째 파라미터로써 작용합니다. 적절한 파라미터를 찾는것은 복잡하기 때문에 처음에는 decay 없이 learning rate를 설정하는 것이 좋습니다.

그 이후 loss 그래프를 시각적으로 평가하여 decay가 필요한지 판단 후 사용하는것이 좋습니다.

### Second Order Optimization

지금까지의 Optimization 알고리즘은 First-Order Optimization 알고리즘입니다.

![Untitled 19](https://user-images.githubusercontent.com/80799025/236253000-b1711d73-9d54-4bba-9845-d5dea7b2b4fa.png)

 First-Order Optimization 알고리즘은 gradient 정보만을 활용합니다.

우리는 빨간점에서 곡선함수의 최솟값으로 gradient를 이용하여 이동했습니다.

여기서 필요한 연산은 현재 위치에서 gradient를 계산하고 테일러 급수전개를 이용해 gradient의 일차 근삿값을 구합니다. 

이제 gradient의 근삿값을 최소화 하기 위해 이동합니다. 위 그래프의 직선을 보면 됩니다. 하지만 근사치는 넓은 범위에서는 적용되지 않기 때문에 빨간점은 멀리 이동해서는 안됩니다.

![Untitled 20](https://user-images.githubusercontent.com/80799025/236253023-e32cd4ca-017b-445b-bc37-2d780494912f.png)

Second-Order Optimization은 1차 미분과 2차미분 정보 모두를 활용하는 방법입니다.

테일러 급수를 이용하여 함수를 근사하고 ,이차함수를 locally하게 근사합니다. 위의 First-Order 그래프와 달리 최솟값으로 직접 이동할 수 있다는 장점이 있습니다.

![Untitled 21](https://user-images.githubusercontent.com/80799025/236253038-66d7b2e4-769a-42c2-95a5-a6790becb6b1.png)

이것을 여러 차원으로 일반화하면 Newton step이라는 개념을 얻을 수 있습니다. Newton step에서는 2차 도함수인 Hessian 행렬을 계산하고, 이를 역행렬로 변환하여 함수의 2차 근삿값의 최소점으로 바로 이동합니다.

이 방법은 다른 방법과 다르게 learning rate가 존재하지 않습니다. 2차 근사를 하고 최소점으로 바로 이동하므로 항상 동일한 크기의 step을 밟기 때문입니다.

하지만 Hessian 행렬을 구하고 역행렬을 구하는것은 계산 비용이 크기 때문에 딥러닝에서 사용하기 다소 비현실적입니다.

만약 Network의 파라미터 수가 1억개라면 Hessian은 1억의 제곱개 만큼 파라미터를 가집니다.  이것을 메모리에 저장하고 역행렬을 계산하는것은 거의 불가능합니다.

![Untitled 22](https://user-images.githubusercontent.com/80799025/236253061-0b3fb70f-a7b1-4823-8994-f63a5188dd90.png)

그래서 전체 Hessian 행렬을 사용하는 대신 근사치와 함께 작동하는 Quasi-Newton 방법을 사용합니다. 여기서 Low-Rank approximationans를 사용하는것이 일반적입니다.

Low-Rank approximationans은 Hessian 행렬의 근사치로 문제에서 가장 중요한 몇 개의 차원만 선택하여 Hessian을 근사합니다. 이렇게 하면 전체 Hessian을 다루지 않아도 되므로 메모리와 계산 비용을 크게 줄일 수 있습니다.

![Untitled 23](https://user-images.githubusercontent.com/80799025/236253076-b09860aa-f4e6-4866-90f5-cd15b9658fe0.png)

Quasi-Newton의 방법중 하나인 L-BFGS는 이러한 Newton 근사 방법의 하나로 Hessian 행렬의 근사를 유지하지만, 확률적인 문제나 non-convex한 문제에서 잘 작동하지 않습니다. 

대부분의 경우 Adam이 좋습니다. 하지만 full batch 업데이트를 수행하고 문제에 확률성이 없을 경우 L-BGFS를 사용하는 것이 좋습니다.

## Model Ensembles (모델 앙상블)

지금까지 배운것들은 Training error를 줄이는 것에 대한 내용입니다.

이제부터 Test 데이터에서의 성능을 높이는 방법에 대해서 알아보겠습니다.

Model Ensembles은 단일 모델을 학습하는 대신 독립적인 여러개의 다른 모델을 학습합니다. (때때로 앙상블의 모델들은 하이퍼 파라미터가 다르기도 합니다.)

Test시에는 여러개의 모델을 실행하여 prediction을 평균화 합니다. 이런 다중 모델은 overfitting을 줄이고 성능을 약간 향상 시킬 수 있습니다. 

경우에 따라 독립적으로 모델을 학습하는 대신 Training 과정에서 여러 Snapshot을 유지하고 이를 Ensemble로 사용할 수 있고 Test시에는 여러개의 Snapshot의 predictions를 평균화 해야합니다.

(여기서 말하는 Sanpshot은 훈련 도중 모델의 파라미터를 특정 시점에서 저장하는 것을 뜻합니다.)

![Untitled 24](https://user-images.githubusercontent.com/80799025/236253108-5a391d41-188a-47e9-8da4-d192b780bfa8.png)

위 사진은 learning rate를 느리게, 빠르게 다시 느리게 다시 빠르게 하는것에 대한 논문 이미지입니다.

이 아이디어는 Training 과정에서 모델이 여러 영역으로 수렴할 수 있고, 다양한 스냅샷에 대한 Ensemble을 수행하면 모델 한번만 훈련하더라도 성능을 개선할 수 있습니다.

## Regularization (정규화)

이번에 어떻게 단일모델의 성능을 끌어 올릴수 있는 Regularization(정규화)에 대해 알아봅시다. 

Regularization(정규화)는 훈련 데이터가 모델에 너무 잘 맞춰주려는 것(Overfitting)을 방지하고 성능을 높이는 방법입니다.

![Untitled 25](https://user-images.githubusercontent.com/80799025/236253132-0df8c4f2-2457-4185-9547-3265acecdcbc.png)

위 Loss Function에서 λR(W) 항이 Regularization을 나타냅니다.

전에도 살펴 보았듯이 Regularization에는 L1,L2, Elastic net 등 여러가지가 존재합니다.

하지만 다음 Regularzation 방법들은 Neural Networks에서는 파라미터수가 매우 많기 때문에 무의미 합니다. 그래서 우리는 다른 Regularization 전략을 이용합니다.

### Drop Out

Neural Networks에서 일반적인 Regularization 방법중 하나는 Drop Out입니다.

![Untitled 26](https://user-images.githubusercontent.com/80799025/236253145-cec5648a-7e4c-4061-8bd2-1716c72c83e5.png)

Drop Out은 Network를 통해 Forwart pass를 할때마다 각 레이어에서 무작위로 일부 뉴런을 0으로 (Activation을 0으로)만듭니다. 이 과정은 한번에 한 레이어씩 진행됩니다.

왼쪽 그림은 기존의 모델이고, 오른쪽 그림은 Drop Out을 수행한 후의 모습입니다.

그렇다면 왜 Drop Out이 좋은 방법인지 알아봅시다.

![Untitled 27](https://user-images.githubusercontent.com/80799025/236253154-bae7f5e7-fe28-4d05-94a4-7862f0663474.png)

Drop Out은 Feature들의 co-adaptation을 방지합니다.  이 방법은 예로 들어 고양이를 인식할때 고양이의 꼬리, 털, 귀 등 각 Feature를 인식하는 뉴런이 있을 때, 어느 한 Feature에 지나치게 의존하지 않고 여러 다른 Feture들에 분산시켜도록 해줍니다. 따라서 Overfitting을 방지할 수 있습니다.

또 다른 Drop Out의 해석으로는 각각의 다른 Drop out mask는 다른 하위 네트워크를 생성하기 때문에 여러개의 공유 파라미터를 가진 여러개의 네트워크 Ensemble을 학습하게 됩니다. 즉 거대한 네트워크 Ensemble을 모두 학습하기 때문에 더 좋은 성능을 낼 수 있다는 것입니다.

그렇다면 Test time에는 어떨지 확인해 봅시다.

![Untitled 28](https://user-images.githubusercontent.com/80799025/236253172-88ed5e64-8985-4d69-b215-d885cbfa1cb8.png)

Drop Out으로 식에 Random Mask(z)가 추가된 것을 확인 할 수 있습니다. z는 무작위 이므로 Test time에 무작위성이 존재하게 됩니다.

만일 무작위성이 존재하면 전에는 강아지라 분류되었는데 이번에는 고양이라 분류된다면 좋은 모델이 아닐것입니다. 따라서 무작위성을 제거하고 평균을 구해야 합니다.

![Untitled 29](https://user-images.githubusercontent.com/80799025/236253192-78918d9c-6450-42f4-ad55-12ed26c2b2c8.png)

적분으로 이것을 해결하는것은 거의 불가능에 가깝습니다.

그래서 위 식으로 평균을 구하는 샘플링을 통해 이 적분을 근사화 하는것을 생각할 수 있지만 여전히 무작위성이 존재합니다.

![Untitled 30](https://user-images.githubusercontent.com/80799025/236253199-e8f49d64-e910-4d7f-b21e-e4e666110029.png)

다행히 Drop out에서는 이 적분을 locally한 방법으로 비교적 적은 계산비용으로 근사할 수 있습니다.

다음 이미지처럼 하나의 뉴런을 예로 들어보겠습니다. 입력은 x,y이고 weight는 w1, w2, 출력은 a이므로 Test time에서는 a=w1x+w2y입니다. 

이제 뉴런에서 1/2확률로 Drop out이 일어난다 가정하면 총 4개의 Drop Out Mask가 있게 됩니다.

그래서 이들 모두 평균화 하면 a의 기댓값은 1/2*(w1x+w2y)가 될 것입니다.

이제 Test time에 더이상 확률적인 것 없이 출력값을 Drop Out 확률로 곱합니다. 이렇게 하면 예상 값과 같아지는 효과가 있습니다.

![Untitled 31](https://user-images.githubusercontent.com/80799025/236253214-c6abc354-7cea-4e21-b0f0-b761aca3a990.png)

Drop Out은 다음과 같이 코드로 간단히 표현 될 수 있습니다.

Predict 함수에서 각 층의 출력값을 Drop out의 확률로 곱합니다. 

그리고 Test Time때는 모든 뉴런이 살아있어 Activation 값을 Training Time의 기대치만큼  Scaling 해줘야 합니다 따라서 p를 곱해줍니다.

![Untitled 32](https://user-images.githubusercontent.com/80799025/236253230-ae78803d-3665-47e4-b6a6-c60fa75f9c46.png)

그리고 위의 코드와 다르게  Training Time에 Drop Out에 p로 나누어 미리 Scailing 해주는 Inverted Drop Out이 일반적으로 사용됩니다.

참고로 Drop Out을 사용하여 Training 할 때 일반적으로 시간이 더 걸립니다. 왜냐하면 각 단계에서 네트워크의 일부만 업데이트 하기 때문입니다. 하지만 Drop Out을 사용하면 수렴 이후에는 일반화 성능을 향상시킨다는 장점이 있습니다.

![Untitled 33](https://user-images.githubusercontent.com/80799025/236253242-d22e0edd-0199-4577-ae53-4471cb74f024.png)

Drop Out이 Training중에 무작위성을 추가하여 Overfitting이 일어나지 않도록 하고 Test시 normalize를 개선하기 위해 모든 무작위성을 평균하는 방법을 사용했지만, 사실 Batch Noramlization 또한 이 방법에 적합합니다.

Batch Noramlization는 Training 동안에 어느 하나의 데이터가 다른 mini-batch에 나타날 수 있습니다. 따라서 이 또한 무작위성을 가지고 있다 볼 수 있습니다. 

하지만 Test시에는 Drop Out과 달리 local이 아닌 global 추정치를 사용하여 무작위성을 평균화 합니다. 그리고 Drop Out과 달리 Batch Noramlization은 강도를 조절하는 파라미터가 존재하지 않아 제어가 더 어렵습니다.

실제로 Batch Noramlization과 Drop Out은 유사한 Regularization 효과를 가지고 있기 때문에 종종 함께 사용되기도 합니다.

그리고 Batch Noramlization로 Drop Out을 사용하지 않고 충분한 Regularization 효과를 얻을 수 있습니다.

### Data Augmentation (데이터 증강)

![Untitled 34](https://user-images.githubusercontent.com/80799025/236253259-d3552b3d-1eb3-445f-be6c-8a9e18f3c7b2.png)

Data Augmentation은 이미지를 무작위로 변화시켜 데이터를 늘리는 방법입니다. 

Data Augmentation은 이미지를 뒤집거나, Crop, 대조나 밝기, 컬러(Color jitter) 변경등의 여러 방법을 사용합니다.

![Untitled 35](https://user-images.githubusercontent.com/80799025/236253276-a1482371-0a6f-4bb3-a4bc-1eb49c6856b6.png)

예로들어 고양이 이미지를 Crop해도 고양이이기 때문에 이미지에서 다양한 크기의 Crop을 무작위로 샘플링 합니다. 그 후 Test하는 동안 이미지의 네 모서리와 중간, 뒤집기 등으로 평가하여 확률을 평균화 합니다.

![Untitled 36](https://user-images.githubusercontent.com/80799025/236253295-e76ef019-1b95-4b35-bc10-a4ec943ecee5.png)

일반적으로 Data Argumentation은 거의 모든 문제에 적용할 수 있습니다.

훈련중에 입력데이터는 위 이미지에 나온것 같이 무작위 변환을 적용하고 일종의 확률적 요소를 추가하여 Regularization 효과를 줍니다. Test시에는 이런 무작위성을 평균화 하여 확률적인 변동성을 줄이게 됩니다.

### ETC

위에서 알아보았던 Regularization 방법 외에도 여러가지가 있습니다. 이번에는 Drop Connect에 대해 알아보도록 하겠습니다.


![Untitled 37](https://user-images.githubusercontent.com/80799025/236253307-30f025e3-f0e6-476d-8a05-d308fa0cf72a.png)

Drop Connect는 Drop Out과 다르게 Activation 값을 0으로 만드는 대신에 weight 행렬의 일부를 무작위로 0으로 만들어 버립니다. 이 방법은 Drop Out과 비슷한 효과를 냅니다.

![Untitled 38](https://user-images.githubusercontent.com/80799025/236253319-eb511fc3-4c61-4e4e-b068-47c9a097ad27.png)

다음은 Faction Max Pooling 입니다.

이 방법은 Max Pooling과 달리 Pooling을 할 때 무작위로 영역을 선택합니다.

위의 이미지에서 보다 시피 Training 중에 나오는 무작위 Pooling 영역을 보여줍니다.

Test시에는 고정된 Pooling영역을 사용하거나 여러가지를 사용하여 무작위성을 평균화 합니다.

![Untitled 39](https://user-images.githubusercontent.com/80799025/236253344-79402b46-2cd8-40fa-9b31-2739a05b9363.png)

마지막으로 볼 것은 Stochastic depth(확률적 깊이)입니다.

Stochastic depth는 Training 중에 Network에 무작위로 레이어를 삭제하여 일부만 사용하고 Test시에는 전체 Network를 사용합니다.

이 방법은 보기에 잘 안될것 같이 보이지만 실제로는 좋은 Regularization 효과를 보입니다.

## Transfer Learning (전이 학습)

지금까지 Regularization를 통해 Training 및 Test간의 오차를 줄일 수 있음을 확인했습니다. 하지만 때때로 적은 데이터셋이 Overfitting 문제를 일으키기도 합니다.

Transfer Learning은 적은 데이터 셋으로도 좋은 결과를 낼수 있도록 해주는 방법입니다.

![Untitled 40](https://user-images.githubusercontent.com/80799025/236253361-0f74221e-0de1-46aa-9f40-30e2c56bd40e.png)

위 그림을 예시로 보면 CNN을 ImageNet와 같은 매우 큰 데이터 셋으로 학습을 시킵니다. 그러면 전체 Network는 충분히 학습이 됩니다.

만약 이 Network를 ImageNet의 1000개의 클래스가 아닌 10개 클래스만 분류하려 합니다. 

일반적으로 마지막의 Fully connected layer feature에서 최종 클래스 점수로 이동하는 연결된 weight 행렬을 Random reinitialize 해야합니다. ImageNet의 경우는 4096X1000인데 10개의 클래스에 대해서는 4096X10이 됩니다. 그리고 나머지 레이어의 weight를 고정하고 linear classifier를 훈련시켜 마지막 레이어의 파라미터만 훈련시킵니다. 이렇게 수행하면 매우 작은 데이터셋에서도 잘 작동합니다.

만약에 데이터가 더 많다면 전체 Network를 조정하거나 더 많은 레이어를 조정하는 방법도 생각해 볼 수 있습니다.

![Untitled 41](https://user-images.githubusercontent.com/80799025/236253383-61c2e1ff-cffa-4256-a515-b2d0cf3098fd.png)

Transfer Learning을 할 때 다음 표와 같이 생각 해 볼 수 있습니다.

예로들어 ImageNet에는 동물과 식물 등 많은 이미지가 포함되어 있습니다. 따라서 다른 종류의 동물이나 식물을 분류할 때 상대적으로 더 잘 작동합니다. 

데이터가 매우 적은 경우에는 linear-classifier를 학습시킬 수 있고, 더 많은 데이터를 사용하는 경우에는 Feature Extracion을 한 후 모델을 Fine-tunning하는것이 좋습니다.

하지만 데이터가 ImageNet과 매우 다른 경우(X-ray 같은 이미지)에는 Network에 더 많은 부분을 재학습 시키거나 다른 방법들을 사용해야합니다. 하지만 데이터가 충분하다면 많은 레이어를 Fine tunning 면 됩니다.

![Untitled 42](https://user-images.githubusercontent.com/80799025/236253400-d3e4a32e-3fea-4661-9f0c-f75a6be6851e.png)

논문들을 보면 위 이미지처럼 왼쪽은 Object Detection을 다루고 오른쪽은 Image Captioning을 다루는 등 다른 작업에 대한 시스템 다이어그램을 자주 볼 수 있습니다.

위의 두 모델은 CNN이 있는데 대부분 처음부터 훈련을 한 것이 아닌 Transer learning을 이용합니다.

또한 Caption 작업에서는 language와 관련된 벡터를 훈련할 수 있는데, ImageNet에서 CNN을 사전 훈련하고, 대규모 TEXT에서 language 단어 벡터를 사전 훈련한 다음 데이터 세트에 대해 전체 모델을 미세 조정할 수 있습니다.

이번 강의에서는 손실을 개선시키는 Optimization 방법들과 Regularization 방법, Model Ensembles, Transfer Learning에 대해 배웠습니다.

이 글이 공부하는 분들께 도움이 되었으면 좋겠습니다.
