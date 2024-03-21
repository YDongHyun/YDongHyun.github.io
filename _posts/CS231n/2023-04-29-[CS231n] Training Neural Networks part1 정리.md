---
title:  "[CS231n] Training Neural Networks part1 정리" 
excerpt: ""

categories:
  - CS231n
tags:
  - [인공지능]

toc: true
toc_sticky: true
 
date: 2023-04-29
last_modified_at: 2023-04-29

---

이 글은 Lecture 6 | Training Neural Networks I 강의를 듣고 내용을 정리하였습니다.
내용및 이미지는 다음 강의에서 발췌하였습니다.

## Training Neural Networks

오늘 강의에서는 Neural Networks를 어떻게 학습시켜야 하는지에 대해 알아봅니다.


### Activation Function(활성화 함수)

![Untitled](https://user-images.githubusercontent.com/80799025/235304879-2dbaff9c-54fc-448f-86ff-be59b79ed331.png)

이전에 봤던 그림에서 완전히 연결된 레이어에서 가중치를 곱합니다. 그리고 이것을 활성화 함수를 통해 전달합니다.

![Untitled 1](https://user-images.githubusercontent.com/80799025/235304884-9c80abcc-3564-4242-a960-53e6c7bdefe9.png)

몇 가지 Activation Function을 보겠습니다.

이것들을 비 선형성에 대한 선택과 장단점에 대해 알아봅시다.

![Untitled 2](https://user-images.githubusercontent.com/80799025/235304887-d4a3e2e3-d83b-417b-8785-6101acb8258c.png)

먼저 Sigmoid입니다.

Sigmoid는 non-linear에 입력된 각 숫자를 취하여 각 요소별로 값을  0~1 사이로 만듭니다.

따라서 매우 큰 값을 받으면 1에 가까울 것이고, 매우 작은 값을 받으면 0에 가까울 것입니다.

하지만 Sigmoid 함수에는 몇가지 문제가 있습니다.

첫번째는 포화된 뉴런이 기울기를 없앨 수 있다는 것입니다.


다음 그림을 보면 dσ/dx는 local gradient가 됩니다.![Untitled 3](https://user-images.githubusercontent.com/80799025/235304889-3dd18623-e9fa-4f5a-a6d4-7573603b34fb.png)


만약 x가 -10일 경우를 생각해봅시다.

그래프를 보면 -10의 부분은 기울기가 거의 0에 수렴하는 것을 볼 수 있습니다. 그래서 Chian Rule을 사용해서 gradient를 구할때 0이 되어버립니다.

반대로 x가 10 경우에도 기울기는 0에 수렴하므로 같은 결과가 나올 것입니다.

그래서 그래프의 오른쪽 끝부분과 왼쪽 끝부분을  포화영역이라 합니다.

![Untitled 4](https://user-images.githubusercontent.com/80799025/235304891-410f7e84-9319-4593-aea5-3704a4b5f3e3.png)

두번째 문제는 Sigmoid의  출력이 0이 아니라는 것입니다.

이것이 왜 문제가 되는지 알아봅시다.

y값은 0 이상이기 때문에 0을 중심으로 이루어지지 않습니다.

input인 뉴런 x가 언제나 양수일 때 어떤지 생각해봅시다.

함수를 w에 대해 미분을 하면 df/dwi=xi가 될 것 입니다.

![Untitled 5](https://user-images.githubusercontent.com/80799025/235304894-fea9ae12-dc3d-468e-9192-0a27c5f33fdb.png)

식을 정리하면 다음과 같습니다.

식을 보면 xi가 모두 양수일 때 dl/dwi 와 dl/df는 같은 부호를 가져야 합니다.

![Untitled 6](https://user-images.githubusercontent.com/80799025/235304898-d34b7f95-f10f-42b4-a4af-f193c4289cf6.png)

위 그래프에서 파란 화살표로 이동해야 이상적입니다.

하지만 w의 기울기는 모두 양수이거나 음수이기 때문에 위 그림의 빨간 화살표와 같이 지그재그로 내려가는 것을 볼 수 있습니다.

이것은 비효율적인 업데이트 방법입니다.

세번째 문제는 지수 함수가 있기 때문에 계산 비용이 많이 든다는 점인데 주요한 문제는 아니고 사소한 문제입니다.

![Untitled 7](https://user-images.githubusercontent.com/80799025/235304904-d095a0b9-ef82-4e33-8e43-8d285cdc7c76.png)

다음은 tanh 함수를 봅시다.

sigmoid와 비슷한 모습이지만 다른점은 -1~1 범위로 스쿼싱 된다는 것입니다.

이제 중심이 0이 되므로 Sigmoid의 두번째 문제를 없앴습니다.

그러나 여전히 뉴런이 포화 되면 gradient가 0이 되어버리는 문제가 있습니다.

![Untitled 8](https://user-images.githubusercontent.com/80799025/235304910-f65f12e4-10cf-417a-a7d1-fb021837565b.png)

다음은 ReLU 함수를 봅시다.

이 함수는 f(x)가 0와 x의 최댓값과 같습니다.

따라서 입력에 대해 요소별 연산을 수행하고 기본적으로 입력이 음수이면 0으로 설정합니다.

이전 Sigmoid, tanh에서 문제되었던 양수 영역에서 포화가 일어나지 않는 것을 확인 할 수 있습니다. 따라서 공간의 절반이 포화 되지 않을 것입니다.

그리고 이 함수는 계산적으로도 매우 효율적입니다. Sigdmoid에서는 지수 함수가 있어 계산 비용이 컸지만 ReLU함수는 max함수를 쓰기 때문에 계산 비용이 낮아 매우 빠릅니다.

하지만 이 함수 역시 문제점이 존재합니다.

첫번째 문제는 0이 중심이 아니라는 것입니다. 이것은 Sigmoid의 두번째 문제와 같습니다.

두번째 문제는 x가 음수인 경우일 때 기울기 입니다.

![Untitled 9](https://user-images.githubusercontent.com/80799025/235304918-555f9df1-c5d8-47ff-b13d-c3af5c9507d6.png)

위 그림을 보고 local graident를 생각해봅시다.

x가 10인 경우는 괜찮겠지만 -10인 경우는 gradient가 0이 되어버립니다.

또한 x가 0인 경우도 gradient는 0이 됩니다.

![Untitled 10](https://user-images.githubusercontent.com/80799025/235304920-97391059-a2b6-4d1e-8074-0b1034c68c50.png)

다음은 Dead ReLU라는 개념입니다.

Input 데이터들이 들어가는 Data Cloud가 있습니다. 

만약 뉴런이 이 Cloud안에 있다면 Active ReLU, 밖에 있다면 Dead ReLU가 됩니다.

Dead ReLU의 경우는 활성화 되지 않습니다.

Dead ReLU가 발생하는 이유는 처음 뉴런 초기화가 잘못 되거나, learning rate가 너무 높아 영역을 벗어나는 경우입니다.

그래서 뉴런을 초기화 할 때 bias를 아주 작은 양수로 초기화 하기도 합니다.

![Untitled 11](https://user-images.githubusercontent.com/80799025/235304923-f7568069-28ef-4f64-98ee-7549194828f3.png)

다음은 Leaky ReLU입니다.

이 함수는 ReLU와 유사하지만 음수 영역에서 평평하지 않고 약간의 기울기를 가진다는 점이 특징입니다. 따라서 음수 영역에서 포화가 일어나지 않습니다.

그리고 여전히 빠른 속도를 제공합니다.

그리고 Parametric ReLU는 기울기 a값을 backpropatgation하여 배울 수 있는 파라미터로써 취급합니다.

따라서 PReLU는 좀 더 유연한 함수입니다.

![Untitled 12](https://user-images.githubusercontent.com/80799025/235304927-1c2f3b4a-1d1b-458c-8648-876c8114122a.png)

다음은 지수함수 형태인 ELU 입니다.

ELU는  ReLU의 모든 장점을 가지고 있고Leaky ReLU, PReLU와 비교하여 노이즈에 약간 더 견고합니다.

ELU 함수는 입력 값이 음수일 때, 지수 함수를 사용하기 때문에 출력은 항상 양수가 됩니다. 

따라서 gradient가 0이 되는 일을 방지하고, 평균이 0에 수렴한다는 장점이 있습니다.

하지만 이 함수 역서 지수 함수를 이용하므로 계산 비용이 상대적으로 많이 든다는 단점이 있습니다.

![Untitled 13](https://user-images.githubusercontent.com/80799025/235304932-c80591e9-abc1-47c4-aa06-bf1c13de0604.png)

다음은 비선형 함수중 하나인 MaxOut 뉴런입니다.

MaxOut 함수는 w1x와 w2x 사이에 최댓값을 반환하는 함수입니다.

이 함수는 ReLU, LeakyReLU함수와 비슷하지만 입력값을 분할하여 처리하는 점에서 차이가 있습니다.

이 함수는 선형 체제에서 작동하고 gradient가 0이 되지 않는다는 장점이 있습니다.

하지만 w1,w2 이렇게 뉴런당 파라미터 수가 두 배가 되는 단점이 있습니다.

지금까지 여러 활성화 함수에 대해 알아보았습니다.

ReLU함수가 일반적으로 잘 작동하는 표준적인 것입니다. 때문에 대부분 이 함수를 사용하는 것이 좋습니다.

Leaky ReLU, Maxout등 도 시도해 볼 수 있습니다만 아직은 실험적입니다.

Sigmoid함수는 다른 함수들보다 성능이 떨어지기에 사용하지 않는 것이 좋습니다.

### Daat Preprocessing (데이터 전처리)

![Untitled 14](https://user-images.githubusercontent.com/80799025/235304937-1135a471-7229-4934-a05e-2235e92acb0a.png)

 Network를 훈련시킬  입력 데이터가 있습니다.

 이 입력 데이터를 가져와 평균값을 0으로 만들고(zero-centering), 표준편차로 나누어 Nomalize(정규화) 해야 합니다. 

그렇다면 이것을 왜 할까요?

![Untitled 15](https://user-images.githubusercontent.com/80799025/235304939-981d33d3-f41c-4dd3-8ecb-b5a3a88925b8.png)

이전에 모든 입력이 양수인 경우에 대한 이야기를 기억할 수 있습니다. 이 경우 w의 gradient는 모두 양수이거나 모두 음수일 수 밖에 없습니다. 따라서 최적의 최적화를 시행할 수 없습니다. 따라서 Zero-Centering을 수행합니다.

입력값이 모두 0이 아니거나 모두 음수가 아니더라도, 어떠한 종류의 편향이 이러한 유형의 문제를 일으킬 수 있습니다. 그래서 Normalize를 통해 데이터가 동일한 범위에 있고 동등하게 기여하도록 합니다.

하지만 이미지 데이터의 경우 0~255로 정규화 되어있기 때문에 일반적으로 Normalize 하지 않습니다.

![Untitled 16](https://user-images.githubusercontent.com/80799025/235304942-7e0f5852-ff16-4582-a1a8-46dcfcceb69c.png)

그리고 PCA와 Whitening에 대해 이야기 해볼 수 있습니다.

PCA(Principal Component Analysis)는 입력 데이터의 covariance matrix을 계산하여 데이터의 주성분을 찾아내는 방법입니다. PCA를 이용하면 입력 데이터의 차원을 축소할 수 있습니다.

Whitening은 입력 데이터의 상관 관계를 없애는 기법입니다.

이 두가지 방법은 일반적으로 Neural Network의 이미지 전처리에는 사용되지 않습니다. 이미지를 최대한 원본을 유지하는 것이 중요하기 때문입니다.

이미지 전처리에는 Zero-centering만 수행하게 됩니다.

Zero-centering 방법으로 한장 한장 모든 이미지로부터 평균을 빼주는 방법 (Alex-Net)이나, 채널 별로 mean값을 빼주는 방법(VGG-Net)을 사용합니다.

### Weight Initialization

네트워크의 weight(가중치)를 초기화 하는 방법에 대해 알아봅시다.

![Untitled 17](https://user-images.githubusercontent.com/80799025/235304949-9996bb7f-9ee5-4b48-92af-59e7119d880c.png)

다음 이미지는 2-layer 신경망 입니다.

만약 모든 파리미터를 0으로 초기화 한다면 어떻게 될까요?

그렇게 되면 모든 뉴런이 동일한 작업을 수행하게 됩니다.

입력이 주어지면 모든 뉴런은 기본적으로 입력 위에 동일한 작업을 수행하게 되어  뉴런들이 모두 같은 출력을 가질 것이기 때문에 모두 같은 gradient를 얻게 될 것입니다.

따라서 모두 같은 방식으로 업데이트 되어 모두 같은 뉴런이 될 것입니다. 이렇게 되면 결과가 좋지 않을 것입니다.

![Untitled 18](https://user-images.githubusercontent.com/80799025/235304951-8b18a2f8-1349-488e-9500-7ad5d0b2f714.png)

이것을 개선할 수 있는 방법 첫번째는 작은 무작위의 숫자를 넣는 것입니다.

모든 가중치를 분포에서 샘플링 할 수 있는 작은 난수로 설정하는 것입니다. 이 경우 소규모 Networks에서는 잘 작동합니다.

하지만 더 깊은 Networks에는 문제가 있을 것입니다.

왜 그렇게 되는지 알아봅시다.

![Untitled 19](https://user-images.githubusercontent.com/80799025/235304954-fa06714d-364c-4fc4-85a8-c0cc404a4c67.png)

500개의 뉴런을 갖는 10-layers 신경망 그리고 난수에 0.01(weghit)을 곱한 값으로 초기화 해보겠습니다. 이 경우 tanh 함수를 사용하고 작은 난수로 초기화 합니다. 임의의 데이터를  전체 Network를 통해 전달하고 각 layer에서 해당 layer으로 나오는 Activations의 통계를 살펴보겠습니다.

tanh 함수를사용하였기 때문에 layer에서 평균이 0에 가깝다는 것을 볼 수 있습니다.

하지만 문제는 표준편차가 급속하게 0으로 수렴하여 Activation이 0이 되버립니다.

![Untitled 20](https://user-images.githubusercontent.com/80799025/235304956-acca6f49-2074-4d82-bdb3-82973bcf28e3.png)

다음은 0.01대신 1을 적용하면 어떻게 되는지 봅시다.

평균과 표준편차가 모두 Overshoot하여 포화되게 됩니다.

따라서 weight를 너무 크게도, 작게도 하면 안된다는 것입니다.

![Untitled 21](https://user-images.githubusercontent.com/80799025/235304957-e52836bf-4500-478d-941d-7dbea9cf4ff4.png)

다음은 Xavier initialization입니다. 이 방법을 사용하면 다음과 같이 좋은 결과가 나오는 것을 볼 수 있습니다.

input개수 만큼 나누어 주어 input이 많으면 wegiht값이 작아지고 input이 적으면 wegiht가 커지는 초기화 방법입니다.

![Untitled 22](https://user-images.githubusercontent.com/80799025/235304959-9d6fc36a-cff7-4391-af9d-2886f6ecc64c.png)

하지만 Xavier initialization은 ReLU를 사용하면 다음과 같이 non-linearity(비선형성)이 무너지게 되는 문제가 발생합니다.

![Untitled 23](https://user-images.githubusercontent.com/80799025/235304963-c799ce54-42a4-4299-aa33-91d2a14aa548.png)

그래서 input에 2를 나누어 주어 다음과 같이 비선형성을 유지하도록 합니다.

오른쪽 작은 그래프에서 파란 점선은 xavier, 빨간 실선은 이 방법을 나타낸것입니다.

이 방법은 ReLU를 사용할 때 잘 작동하는 최적의 방법입니다.

### Batch Normalization (배치 정규화)

Batch Noramlization은  입력 데이터의 분포를 정규화하고 각 레이어에 들어가는 입력을 안정적으로 만들어 주어 학습 과정을 안정화하는 것이 목적입니다.

입력값의 분포가 다르면 학습이 불안정해지기 때문에 각 레이어를 지날때마다 Nomalization 해주는것입니다.

레이어를 지날 때 마다 Normalization하여도 미분이 가능하기 때문에 Foward, Backward Pass 하는데 문제가 없습니다.

![Untitled 24](https://user-images.githubusercontent.com/80799025/235304968-5398fdb6-5a7d-4c4a-bf15-2ab417449dc6.png)

Normalization은 출력을 일정 범위 이내로 만들 위해 가우시안 분포를 사용합니다.

unit 가우시안 activations을 위해서 현재 layer의 일괄 actvation 값을 조절하여 unit 가우시안 값을 얻을 수 있습니다. 

이 방법을 구현하기 위해 각 뉴런의 평균 및 분산을 계산하고 이를 정규화하면 됩니다

입력 데이터를 보면 현재 Batch에는 N개의 Training 데이터가 있고, 각 Batch는 D차원 입니다.

이제 각 차원마다 x의 값들을 평균과 분산을 계산하여 이로부터 Normalize 합니다.

![Untitled 25](https://user-images.githubusercontent.com/80799025/235304971-c4db96ea-7653-442c-8f44-a99fc000de83.png)

Batch Normalization은 일반적으로 Fully-Connected layer와 Convolutinal 레이어 이후에 삽입됩니다.

위 사진을 보면 FC와 tanh 사이에 BN이 있는것을 볼 수 있습니다.

Fully-Conneted layer의 경우 W(가중치)의 곱을 반복할수록 bad scailing 즉 데이터를 일정범위 안으로 조정하지 못하는 효과가 나타납니다.

Batch Normalization은 이 문제를 해결 할 수 있습니다.

Convoultion layer의 경우 모든 feature dimenstion뿐 아니라 activation map의 spatial dimension과 training example에서 동시에 Normalize하기 때문에 Fully-Connectd layer보다 더 많은 계산 비용이 듭니다. 그래서 Convolution layer에서는 각 activation map마다 하나의 평균과 표준편차를 사용하여 Normalize합니다.

따라서 Convolution layer에서 Batch Normalization을 이용하면 더 빠른 학습이 가능합니다.

그렇다면 비 선형성을 전달할 때 Batch Normalization으로 unit 가우시안을 전달하는것이 적합하는지 판단하는 것 또한 학습을 통해 판단이 가능합니다.

![Untitled 26](https://user-images.githubusercontent.com/80799025/235304977-86aa21bf-32a3-40e2-a60b-3403670bfa50.png)

식을 보면 Normalize를 한후 정규화 된 값의 scale과 shift를조정을 할 수 있도록 해줍니다.

 γ와 β는 파라미터로 학습을 통해 정해집니다.

만약 γ와 β가 오른쪽과 같이 되면 Noramlize는 일어나지 않고 입력값이 그대로 출력될 것입니다.

![Untitled 27](https://user-images.githubusercontent.com/80799025/235304981-bbc98a63-3777-42af-a056-29d5c7879408.png)

식들을 정리하면 다음과 같습니다

먼저 입력값의 mini-batch mean을 계산합니다. 이 작업을 매 mini-batch마다 수행합니다.

그리고 분산을 계산하고, 평균과 분산으로 Normalize한 후 위에서 말했던 scale과 shift 조정 합니다.

이를 통해 gradient 흐름이 좋아져 더 robust한 결과를 얻을 수 있습니다.

그리고 각 레이어의 출력값들이 해당 mini-batch 내의 다른 예제들과 결합되어 서로 영향을 주고 받는으며 함께 정규화되기 때문에 Batch Normalization은 어느 정도의 Regularization(규제) 효과도 있습니다.

마지막으로 Batch Noramlization은 학습을 할때는 batch단위로 평균과 분산을 구합니다. 하지만 테스트를 할 때는 전체 데이터의 평균과 분산을 사용합니다.이를 기반으로 Batch Normalization을 시행합니다.

### Babysitting the Learning Process

학습과정을 돌보기

학습을 모니터링 하는 방법과 좋은 학습 결과를 얻기 위해 hyper-parameter를 조정하는 방법에 대해 알아보겠습니다.

![Untitled 28](https://user-images.githubusercontent.com/80799025/235304985-7ab68980-b6cf-4011-9150-f36191d1e16a.png)

첫번째 단계는 데이터 전처리입니다. 위에서 말했듯이 평균을 0으로 만듭니다.

그 다음은 네트워크 Architecture를 선택합니다.

![Untitled 29](https://user-images.githubusercontent.com/80799025/235304988-b3b875b0-ddae-4386-9616-08ac5d92d6b5.png)

예로들어 50개의 뉴런으로 이루어진 1-hidden-layer 모델을 사용하겠습니다.

Network를 initialize 하고 Forwad pass를 수행하여 Loss값이 합리적인지 확인해야 합니다.

![Untitled 30](https://user-images.githubusercontent.com/80799025/235304989-9eefd3a7-f47f-4b7d-9bb1-14a1539a224a.png)

모델의 Loss가 regularization이 있는 경우와 없는 경우를 비교해봅시다. 

만약 10개의 Class가 있다면  Loss값은 약 2.3이 됩니다.

regularization이 있다면 새로운 항이 생기므로 Loss값은 증가할 것입니다.

이제 regularization이 잘 적용 되었음을 확인했습니다.

그리고 다음 예제에서는 매우 작은 데이터로 시작합니다. 왜냐하면 작은 Training 데이터만 있다면 Overfitting하여 좋은 Loss값을 얻을 있기 때문입니다.

![Untitled 31](https://user-images.githubusercontent.com/80799025/235304994-1f4e5eb6-9802-4c9d-b2cb-16208367abcd.png)

먼저 regularization을 없애고 Loss를 0으로 만들 수 있는지, 각각 epoch에 대해 Loss가 어떻게 변하는지 봅니다.

결과적으로 Training accuracy가 1이 되어 Overfitting이 됩니다.

이제 전반적인 증명이 다 되었습니다. 이제 regularization을 적용하여 학습해보겠습니다.

![Untitled 32](https://user-images.githubusercontent.com/80799025/235304996-011c1e4b-6f07-490a-83c6-751c6224b7ba.png)

먼저 조정해야 하는 값은 learning rate입니다. 이 값을 찾기 위해 몇가지 실험을 해보겠습니다.

learning rate를 매우 작은값으로 설정하면 Loss값이 매우 조금씩 바뀝니다. 따라서 learning rate가 너무 작으면 gradient 업데이트가 너무 작게 된다는 것입니다.

하지만 거의 변하지 않는 loss값에도 불구하고 Training accuracy가 상대적으로 빠르게 올라갑니다.

그 이유는 확률이 여전히 분산되어있기 때문에 Loss값은 비슷하지만, 가중치는 정답 방향으로 이동하기 때문에 정확도는 상승하기 때문입니다.

![Untitled 33](https://user-images.githubusercontent.com/80799025/235304998-5fc1da62-bb89-43d3-9d84-532cb201a4e7.png)

반대로 learning rate를 매우 크게 바꿔보겠습니다. 

이번에는 Loss값으로 NaN을 반환합니다. 이는 Loss값이 폭발적으로 증가했다는 의미입니다.

따라서 learning rate가 너무 높아도 제대로 된 학습이 불가능 하다는 것입니다.

이런식으로 learning rate를 바꾸어가며 조정하면 됩니다. 학습이 잘 되는 대략적인 범위는 10^-3부터 10^-5정도 입니다. 이 범위 내에서 여러 가지 값을 시도해보고 최적의 값을 찾으면 됩니다.

### Hyperparameter Optimization (하이퍼 파라미터 최적화)

이번에는 하이퍼 파라미터를 최적화 하는 방법에 대해 알아봅시다.

먼저 방법중 하나는 Cross-Validation(교차 검증)을 수행하는 것입니다.

교차 검증은 Training 데이터에서 훈련한 다음 Validaiton 데이터에서 평가하는 것입니다.

일반적으로 이것을 단계적으로 수행하기 원하는데

첫번째 단계에서는 멀리 있는 값을 선택하여 몇 epoch만 학습시킵니다.

몇 epoch만으로도 하이퍼 파라미터 값이 좋은지 어느정도 확인 가능합니다.

두번째 단계에서는 더 많은 epoch을 돌리면서 값이 좋은지 판단하고 수정하여 좋은 값을 찾는 것입니다.

![Untitled 34](https://user-images.githubusercontent.com/80799025/235305003-2e19b112-7104-4ffd-97dc-c3252b1070ba.png)

또한 하이퍼 파라미터를 Grid Search(그리드 서치)이용하여 샘플링할 수 있습니다. 그리드 서치는 일정 간격으로 움직이는 것입니다. 

그리드 서치는 등간격으로 증가하기 때문에 오히려 최적의 파라미터를 찾지 못하게 된다는 것입니다. 실제로 각 하이퍼 파라미터 값 범위에서 무작위로 샘플링하는 것의 결과가 더 좋습니다.

![Untitled 35](https://user-images.githubusercontent.com/80799025/235305007-8bfc9920-0fb0-4f56-82b6-d9ed4868cc8c.png)

실제로는 많은 파라미터와 교차검증을 합니다. 교차검증을 모니터링 하면서 어떤 하이퍼 파라미터가 잘되는가 모니터링을 합니다.

![Untitled 36](https://user-images.githubusercontent.com/80799025/235305010-cd620250-1800-45db-a43e-c9f501ff4e40.png)

이 그래프는 epoch에 따른 Loss 그래프입니다.  그래프를 모니터링 하여 learning rate가 좋은지 나쁜지 판단이 가능합니다.

이 경우는 low learning rate일 경우입니다.  따라서 learning rate를 높여야 합니다.

![Untitled 37](https://user-images.githubusercontent.com/80799025/235305012-c806898f-d6e9-46da-bb28-4ba8080688ee.png)

다음은 Loss time 곡선을 보겠습니다. 

다음 곡선은 일정 시간동안 평탄하다 급격히 감소하는 모습을 보입니다.

처음의 기울기가 변하지 않기 때문에 이 경우는 좋지 않은 initialization이 이루어졌다 볼 수 있습니다.  

이런 식으로 모니터링을 통해 하이퍼 파라미터에 대한 문제를 알아내고 적절하게 수정해 나갈 수 있습니다.

![Untitled 38](https://user-images.githubusercontent.com/80799025/235305015-94f5c838-97ec-4979-90a3-23627e9066e2.png)

만약 위 그래프처럼 validation accuracy와 training accuracy가 차이가 많이 난다면 Overfitting이 발생할 확률이 높으므로 Regularization의 강도르 높여주는 것이 좋습니다. 만약 둘의 차이가 없다면 모델의 capacity을 높여주는것이 좋습니다.

![Untitled 39](https://user-images.githubusercontent.com/80799025/235305020-5aea570d-0427-4132-ba1f-a6c1b77e5078.png)

마지막으로 모니터링 해야 할 대상은 weight의 업데이트를 weight의 magnitude로 나눠주는것을 봐야합니다.  비율은 약 0.001 정도가 되도록 하는 것이 좋습니다. 비율을 정확히 맞출 필요는 없지만, 업데이트가 weight 값에 비해 너무 크거나 작아지지 않도록 조절해야합니다.

이번 강의에서는 Activation Function, 데이터 전처리, Weight Initialization, Batch Normalization, 모니터링, 그리고 하이퍼파라미터 최적화에 대해 살펴보았습니다.

이글이 공부하시는 분들께 도움이 되었으면 좋겠습니다.
