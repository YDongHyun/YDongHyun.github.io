---
title:  "[CS231n] Backpropagation and Neural Networks 정리" 
excerpt: ""

categories:
  - CS231n
tags:
  - [인공지능, 머신러닝]

toc: true
toc_sticky: true
use_math: true

date: 2023-04-25
last_modified_at: 2023-04-25
header: 
  teaser: "./images/cs231n.png"

---

이번 글은 CS231n의 Lecture 4 | Introduction to Neural Networks강좌를 정리한 글입니다.

이 글의 사진 및 내용은 강의 자료에서 인용하였습니다.

## Backpropagation (역전파)

지금까지는  W가 변함에 따라 Loss에 어느 정도 영향을 미치는가를 구할떄 아래의 함수처럼 나타냈었습니다.

![Untitled](https://user-images.githubusercontent.com/80799025/234291739-e264d855-d896-4729-b2e3-764d24205d78.png)

하지만 Alex-Net만 보더라도 신경망 모델은 매우 복잡한 것을 알 수 있습니다.

![Untitled 1](https://user-images.githubusercontent.com/80799025/234291775-5ca00a57-3a5c-48c4-ace7-4c40d62be73c.png)

이 경우 지금까지 배운 방법으로는 한계가 있습니다.

그래서 복잡한 모델에서는 경사 하강법을 적용하기 위해 미분 가능한 계산 그래프(Computational Graph)를 만들어야 합니다.

그러면 계산 그래프의 모든 변수에 대한 Gradient를 계산하기 위해 Chain Rule을 사용하는 Backpropagation(역전파) 기술을 사용할 수 있습니다.

먼저 간단한 예제를 봅시다.

![Untitled 2](https://user-images.githubusercontent.com/80799025/234291808-eee0e354-e6dc-4225-8afa-207d7752a8b4.png)

함수 f(x,y,z)를 그래프로 표현한 것이 오른쪽 그림입니다.

중간에 덧셈과 곱셉으로 이루어져 있는데, 입력된 수를 각 덧셈, 곱셉해주면 됩니다.

최종적으로는 -12를 얻게 됩니다.

![Untitled 3](https://user-images.githubusercontent.com/80799025/234291835-374ff86a-a288-450b-ba59-53f58a857cfe.png)

intermediate variables(중간 변수)의 이름을 지정합니다.  덧셈 노드는 q, 마지막은 f라 지정합니다.

`q = x+y` 가 되고, `f=qz`로 표현 할 수 있습니다.

이제 이 식의 기울기를 구할 수 있습니다.

여기서 찾고자 하는 것은 x, y, z에 대한 f의 기울기 입니다.

계산의 맨 끝에서 시작하여 backward로 작업하여 기울기를 따라 계산 할 것입니다.

먼저 Local Gradient를 구합니다. 왼쪽 사진을 보면 Local Gradient를 구한 식이 정리되어있습니다.

q의 경우 덧셈 노드로, x나 y의 증가함에 따라 q도 비슷하게 증가하므로 1이됩니다.

f의 경우는 q에 대한 기울기는 z, z의 대한 기울기는 q로 나타낼 수 있습니다. 즉 서로 반대가 됩니다.

![Untitled 4](https://user-images.githubusercontent.com/80799025/234291869-39392a0e-6914-4b11-b2ea-635037ee32f0.png)

 위 식들을 이용하여 Chain Rule로  local graiednt와 global gradient(df/df)를 곱하여 x,y,z에 대한 f의 기울기를 구할 수 있습니다.

이제 간단한 예시로 계산을 해봅시다.

![Untitled 5](https://user-images.githubusercontent.com/80799025/234291910-a36d22a7-cdf0-4323-8e1c-52e788982482.png)

x에값에 대한 f의 기울기를 Backpropagation을 이용해 구해보면 다음과 같습니다.

![Untitled 6](https://user-images.githubusercontent.com/80799025/234291952-7a549eba-b16a-4251-87f8-f25cdc1d5c3e.png)

다음과 같이 df/da를 직접적으로 구할 수 없으니 단계적으로 기울기를 구하면서 역전파로 구할 수 있습니다.

이렇게 Chain Rule을 이용하여 간단히 기울기를 구할 수 있습니다.

![Untitled 7](https://user-images.githubusercontent.com/80799025/234292014-30132f36-f48b-487b-8522-92504716fba9.png)

이처럼 Backpropagation(역전파)는 방금 계산한 것 같이 인접 노드만 인식한다는 특징이 있습니다.

위 그림은 Local 입력(x,y) 출력(z)을 갖습니다.

그리고 local gradient를 알고 있으면 Chain Rule을 통해 기울기를 계산 할 수 있음을 보았습니다.

더 복잡한 예제로 자세히 알아봅시다.

![Untitled 8](https://user-images.githubusercontent.com/80799025/234292042-91787f69-8f4d-4d01-892d-086df5fc028b.png)

위 식을 아래와 같은 그래프로 표현하였습니다.

먼저 local gradient를 정의합니다.

![Untitled 9](https://user-images.githubusercontent.com/80799025/234292061-e428fe0a-4ea0-48b7-8cbb-027d3daa31b1.png)

다음과 같이 local gradient가 정의됩니다.

이제 위 식을 이용하여 역전파를 진행해봅시다.

df/df를 1로 정의하고 계산해봅시다.

![Untitled 10](https://user-images.githubusercontent.com/80799025/234292085-77e96546-4502-4099-b003-563e8943de6f.png)

그러면 다음 노드에서는 아래와 같은 식으로 계산이 됩니다.

![Untitled 11](https://user-images.githubusercontent.com/80799025/234292119-7930cadd-e0c8-48be-a958-75e82339a3a8.png)

위에서 정의한 df/dx에 값을 대입하면 쉽게 구할 수 있습니다.

다음 덧셈 노드는 local gardient가 1이므로 그대로 0.53이 되겠죠.

이제 exp 함수를 살펴봅시다.

![Untitled 12](https://user-images.githubusercontent.com/80799025/234292168-05cc19e3-058d-44a7-a2ea-43890579aa84.png)

exp 함수 또한 local gradient 값에 -1을 대입하고 upstream기울기(이전 계산한 기울기)와 곱하여 -0.20이라는 값을 얻었습니다.

다음 *-1의 경우는 local gradient를 계산해보면 a = -1로 나옵니다.

따라서 -1 X -0.20이므로 0.20이 될 것입니다. 

다음은 덧셈 노드가 여러 갈래로 나누어 졌을 땐 어떻게 되는지 봅시다.

![Untitled 13](https://user-images.githubusercontent.com/80799025/234292194-e551b4d4-27d3-4b69-b7df-43740a4327f8.png)

덧셈 노드가 여러 노드로 갈라지게 되면 같은 값을 분배하게 됩니다.

![Untitled 14](https://user-images.githubusercontent.com/80799025/234292212-a3089544-2d1e-48cc-b4dd-0dac0b2d9cd2.png)

곱셈 노드의 경우는 값이 서로 바뀌게 됩니다. 

즉 w0 노드의 local gradient는 x0값이 되고, x0의 local gradient값은 w0가 됩니다. 

이제 처음으로 돌아가봅시다. 처음 함수를 다시 보면 Sigmoid 함수랑 유사하다는 것을 알 수 있습니다.

그래프의 다음 부분을 sigmoid gate라 합시다.

![Untitled 15](https://user-images.githubusercontent.com/80799025/234292230-70ebe363-c7dd-4c8e-853f-f7ba60aa99fd.png)

sigmoid gate 부분과 sigmoid 함수는 같은 함수라 보시면 됩니다.

여기서 sigmoid gate를 어떻게 이용하는지 봅시다.

먼저 sigmoid의 기울기를 봅시다.

![Untitled 16](https://user-images.githubusercontent.com/80799025/234292258-97c1ee0c-9f05-4656-9a30-3e9dcbe93c8d.png)

sigmoid의 기울기는 자기 자신 외에는 다른 인자가 들어가지 않은 간단한 함수 형태로 표현됩니다.

따라서 역전파를 더욱 간단히 계산할 수 있습니다. 나눠서 계산하는 것과 하나로 묶어 계산한 값은 같다는 것입니다.

정말 계산값이 같은지 확인해봅시다.

저희는 예제에서 df/df =1 이고, 0.2라는 upstream gradient를 얻었습니다.

한번 sigmoid 기울기에 대입해봅시다.

![Untitled 17](https://user-images.githubusercontent.com/80799025/234292289-092a30c4-fa18-4de3-8d86-633f082b6093.png)

대입한 결과 여러 노드로 나눈 값과 동일한 값이 나왔습니다.

저희는 여러 노드를 하나의 local gradient로 표현할 수 있는 한 그래프를 더 간단하게 바꿀 수 있음을 보았습니다.

얼마나 계산을 많이 수행할 것 인지와 얼마나 간단히 그래프를 만들 것 인지에 대한 절충이 필요합니다.

다음은 max 노드에 대해 알아봅시다. 

![Untitled 18](https://user-images.githubusercontent.com/80799025/234292312-e333d136-6bf2-4f03-84b5-564238443e99.png)

max 노드는 값을 비교하여 가장 큰 값을 반환합니다.

max의 local graident의 0과 1의 값을 반환할 것입니다. 값이 큰 쪽은 1이 되고 작은 쪽은 0이 될 것입니다.

즉 여러개 값 중에 제일 큰 하나만 취하는 겁니다.

여러 노드에 역할에 정리하면 다음과 같습니다.

- 덧셈 노드(add-gate)는 distributor로서 역할을 한다.
- 곱셈 노드(mul-gate)는 swith로서 역할을 한다.
- max 노드 (max-gate)는 router로서 역할을 한다.

이것을 알면 연산하기가 더욱 수월할 것입니다.

이제 한 노드가 여러 노드에 연결되는 위치에 있을 경우를 생각해봅시다,

![Untitled 19](https://user-images.githubusercontent.com/80799025/234292326-3ed341ca-b237-4579-a173-94befb62dbce.png)

한 노드가 여러 노드에 연결되는 위치에 있다면 각 노드에서 들어오는 기울기의 값을 가져옵니다.

이 노드는 Forward-Pass에서는 연결된 두 노드 모두에게 영향을 미칠 것이라 생각할 수 있습니다.

반대로 Backward-Pass에서는 두 노드는 이 한 노드에 영향을 줄 것입니다.

![Untitled 7](https://user-images.githubusercontent.com/80799025/234293453-075cfef9-6ed4-43db-8b12-eb3b3720e6b3.png)

지금까지는 x,y,z값들이 Scalar일 경우를 살펴보았습니다.

실제 문제 해결을 위해서는 x, y, z 값들은 행렬 벡터의 형태가 될 것입니다.

Vector의 편미분인 Local gradient는 `Jacobian Matrix`가 될 것입니다.


![Untitled 20](https://user-images.githubusercontent.com/80799025/234292843-81114308-dd5b-4570-8419-2ed9fb647f31.png)

만약 4096차원의 input벡터가 있으면 output 벡터 또한 4096차원이 될 것입니다.

df/dx의 경우 Jacobian 행렬이 될 텐데 입력의 각 차원에 대한 각 차원의 편도함수의 행렬입니다.그 크기는 4096X4096이라는 거대한 행렬이 될 것입니다.

만약 100개의 입력을 동시에 배치로 작업한다면 실제로 행렬의 크기는 409600X409600으로 매우 커질 것입니다. 

행렬의 크기는 크지만 입력의 각 요소는 출력의 해당 요소에만 영향을 주므로 대각 행렬의 형태를 취합니다.

다음 벡터화한 예시를 봅시다.

![Untitled 21](https://user-images.githubusercontent.com/80799025/234292804-e0d71909-65fc-4912-b2bc-2155a3388323.png)

먼저 계산 그래프를 작성해봅시다.

w,x 가 모두 벡터이기 때문에 일전의 예제와는 조금 다른 양상을 보입니다.

위 형태에서 q와 f(q)에 대해 다음과 같이 정의가 될 수 있습니다.

![Untitled 22](https://user-images.githubusercontent.com/80799025/234292784-d780cf4e-9c5e-4a65-a9fa-7be6757556aa.png)

q는 행렬의 곱으로 다음과 같이 나타납니다.

f(q)의 경우는 행렬의 크기의 제곱, 즉 각 요소의 제곱의 합이 됩니다.

이제  한 노드 뒤로 가보겠습니다.

![Untitled 23](https://user-images.githubusercontent.com/80799025/234292742-85d89d47-f200-4199-8ad3-84a72cc90a5a.png)

df/dq의 기울기는 2q이므로 벡터에 2배를 해주게 됩니다.

벡터의 형태이므로 모든 요소에 곱을 해주면 됩니다.

또한 여기서 구한 gradient는  q의 각 요소가 f값에 어떻게 영향을 미치는가를 나타냅니다.

그래서 위의 벡터와 기울기 벡터의 행렬 크기는 같아야 합니다.

한 노드 더 뒤로 이동해봅시다.

![Untitled 24](https://user-images.githubusercontent.com/80799025/234292723-6e813f5d-4caf-483e-ac52-91b913553abc.png)

이번에는 w,x의 각 요소가 f값에 얼마나 영향을 주는가를 계산하여 구한 식입니다.

위의 예시에서 보았듯이 Chain Rule을 이용하여 문제를 해결했습니다.

df/dw와 df/dx를 각각 구하여 나타낸 결과 회색 박스의 식을 얻을 수 있었습니다.

최종적으로 대입하여 연산하면 Gradient 값을 구할 수 있습니다.

여기서 벡터의 Gradient는 원래 벡터와 같은 크기가 되고 Gradient의 각 요소는 특정 요소가 함수의 최종 출력에 얼마나 영향을 주는지 의미합니다.

신경망은 정말 복잡합니다. 때문에 모든 매개변수에 대해 기울기 공식을 직접 구하는 것은 매우 비실용적일 것입니다. 

그래서 기울기를 얻기 위해 신경망의 핵심기술인 Backpropatgation(역전파)를 어떻게 사용해야는지 이야기하고 직접 계산해 보았습니다.

이제 본격적으로 신경망(Neural Network)에 대해 알아봅시다.

## Neural Network

![Untitled 25](https://user-images.githubusercontent.com/80799025/234292670-199d01c1-5407-44fe-b03d-9868d6023b37.png)

저희는 이전까지 f=Wx의 형태의 간단한 함수로 많은 작업을 해왔습니다.

이 파트에서는 Neuarl Network를 이용할 것입니다.

아래 사진을 보면 3072개의 Input이 w1 가중치들과 연산되어 100개의 노드의 hidden-layer로 되고, 다시 w2 가중치들과 연산되어 10개의 노드의 Score가 될 것입니다. 

![Untitled 26](https://user-images.githubusercontent.com/80799025/234292658-6918d26c-3fa2-4bab-87c7-fb3014908d1d.png)

전에 배운 Linear-Classifeir의 경우를 보면 다음과 같이 클래스별 1개의 Classifier만 존재하기 때문에 분류 성능이 떨어집니다.

Neural Network를 사용할 경우는 가운데 hidden-layer에 100개의 노드가 있고, 각 노드가 하나의 Feature를 담당한다 이해를 하시면 됩니다. 당연히 분류 성능히 더욱 좋겠죠.

그리고 2-layer 뿐 아니라 3개 이상도 얼마든지 가능합니다.

여기서 노드의 개수는 실험을 통해 최적의 값을 구해야 합니다.

![Untitled 27](https://user-images.githubusercontent.com/80799025/234292632-7269ab57-1a97-4863-9a35-88d58792c62d.png)

이제 Neraul Network를 실제 뉴런과 비교해보겠습니다.

뉴런의 dendrite에서 Input 데이터를 받고, cellbody에서 ACtivation Function을 통해 Non-lineraity(비선형)하게 만들고 axon을 통해 output을 냅니다.

하지만 Neural Network를 실제 우리 두뇌와 비슷하다고 이야기 하는것은 매우 조심스럽습니다.

실제 두뇌는 Neural Network보다 훨씬 복잡한 형태로 비교하기에는 무리가 있습니다.

### Activation Function(활성화 함수)

Activation Function은 입력 신호의 총합을 출력 신호로 변환하는 비선형 함수입니다.

활성화 함수는 비선형 함수이기 때문에 입력 값의 선형 조합이 아닌 비선형 변환을 수행합니다. 

이러한 비 선형성은 복잡한 패턴을 학습할 수 있게 합니다

![Untitled 28](https://user-images.githubusercontent.com/80799025/234292619-b1d3cd32-e1d6-44ec-9a0b-477c6f47defb.png)

activation function 에는 Sigmoid, tanh, ReLU등 여러가지가 있습니다.

### Architectures(구조)

![Untitled 29](https://user-images.githubusercontent.com/80799025/234292581-1d8e704f-fee6-43fc-b8c9-787d48c18b4e.png)

Neural Network의 구조는 위 그림과 같습니다.

처음 입력의 레이어를 input layer, 마지막 출력 레이어는 output layer라 합니다.

그리고 그 사이에 연산을 수행하는 레이어를 hidden-layer라 합니다.

이것을 레이어의 개수인 `2, 3-layer Neural Net`나 hidden-layer의 개수로 `1,2-hidden-layer Neural Net`로 불립니다.

레이어가 3개인데 왜 2-layer라 표현하는가 의문이 들 수 있을텐데 weight를 가진 레이어만 세기 때문입니다.

이번 강의로 간단히 Backpropagation과 Neural-Network에 대해 알아보았습니다.

공부하시는 분들께 도움이 되었으면 좋겠습니다.
