---
title:  "[CS231n] Convolutional Neural Networks 정리" 
excerpt: ""

categories:
  - CS231n
tags:
  - [인공지능, 머신러닝]

toc: true
toc_sticky: true
use_math: true

date: 2023-04-26
last_modified_at: 2023-04-26

---

이번 글은 CS231n의 Lecture 5 Convolutional Neural Networks강좌를 정리한 글입니다.

이 글의 사진 및 내용은 강의 자료에서 인용하였습니다.

## History

먼저 Nerual Network에 간단한 역사를 알아봅시다.

먼저 1957년으로 거슬러 갑니다.  이때 Frank Rosenblatt의 Mark 의 Perceptron Machine이 등장하게 됩니다.

![Untitled](https://user-images.githubusercontent.com/80799025/234541468-78bea5e5-dd10-4bfe-bfc4-8ae626b5d159.png)

이 기계는 퍼셉트론이 제시된 최초의 기계입니다. 

하지만 이 기계의 퍼셉트론은 미분이 불가능합니다. 즉 backpropagation이 불가능하다는 겁니다.

그래서 w(가중치)값을 임의로 조정하면서 최적화를 시도했습니다.

![Untitled 1](https://user-images.githubusercontent.com/80799025/234541506-02cc981f-768e-4e80-8017-76a39dcf1cc9.png)

다음은 1960년 Widrow 와 Hoff의  adalin/madaline은 선형 레이어를 다층 퍼셉트론 네트워크에 쌓기 시작했습니다. 

이것은 신경망 계층에 대한 최초의 아이디어입니다. 하지만 이것 역시 backpropagation이 불가능했습니다.

![Untitled 2](https://user-images.githubusercontent.com/80799025/234541519-6322e2ac-4c1b-4528-8338-1c4f06771896.png)

backpropagtion을 최초로 제시한건 1986년에 rumelhart였습니다. 미분이 가능하고, Chain Rule과같은 방법을 사용하여 가중치를 체계적인 방법으로 찾을 수 있다는 것을 제시했습니다.

하지만  생각처럼 잘 동작하지 않았습니다. 

그 이후 신경망의 확장이 불가능하듯 보여  침체가 되었습니다.

![Untitled 3](https://user-images.githubusercontent.com/80799025/234541562-53bb5ea1-d94c-42ce-90ec-d93478d1fe45.png)

 2006년에 Geoff Hinton과 Ruslan Salakhytdunov의 논문이 발표되었습니다.  이 논문에서 pre-training과 backpropagation을 통해 심층신경망을 훈련 시킬 수 있다는 것을 보여줬고, 이것을 효과적으로 할 수 있음을 보여줬습니다. 

![Untitled 4](https://user-images.githubusercontent.com/80799025/234541579-d3b240f6-686c-44b6-ac36-f056f687af26.png)

이후에 진보된 딥러닝 모델이 등장하면서 딥러닝이 크게 주목받게 되어 컴퓨팅 자원의 발전, 데이터의 폭발적인 증가 등 여러 요인들에 의해 더욱 발전하게 되었습니다.

Convolutional Neural Networks 정말 많은 분야에서 사용됩니다.

예로들어 이미지의 Feature를 구분하는 Detection, Segmentation이 있고

얼굴인식, 사람의 Pose인식, 도로 표지판 인식 그리고 이미지를 보고 이미지를 설명하는 등 매우 많은 곳에 사용됩니다.

## Convolutional Neural Networks (CNN)

그렇다면 Convolutional Neural Networks는 어떻게 작동되는지 알아봅시다.

![Untitled 5](https://user-images.githubusercontent.com/80799025/234541601-f249862f-0526-4478-840c-e83b85bc4190.png)

지금까지 배운 Fully Connected Layer는 완전히 연결된 레이어에 대해 벡터로 작업하는 것입니다.

Input 이미지가 32X32X3일떄, 이미지 벡터는 1X3072인 3072차원의 벡터가 됩니다.

output은 1x10으로 10개로 나누어집니다. 이에 가중치 W에 대한 벡터는 3072X10이 되는겁니다.

결국 3072차원 입력과 가중치와의 내적을 통해 output을 내게 되는 것입니다.

![Untitled 6](https://user-images.githubusercontent.com/80799025/234541618-7ba4cab5-c5ae-42d4-a6ee-edd901f60068.png)

이전까지 이미지를 긴 길이의 벡터로 만들었다면,  CNN은 이미지의 3차원 구조를 유지하면서 진행합니다. 

가중치를 본 이미지보다 작은영역의 filter를 이용하여 표현합니다.

공간적으로 필터는 이미지 위로 움직이면서 모든 공간적 위치에서 내적을 계산하게 됩니다.

그리고 필터는 이미지 모든 깊이를 통과할 것이므로 사이즈를 3으로 맞춰주었습니다.

![Untitled 7](https://user-images.githubusercontent.com/80799025/234541682-2e709952-54d8-4044-8018-422ee0fb0de5.png)

다음 필터가 이미지 위를 슬라이드 하면서 각 요소와 내적을 합니다.

기본적으로는 필터 W의 전치 행렬과 이미지 x의 곱, 그리고 bias의 합으로 이루어집니다.

필터는 왼쪽 상단 모서리에서 이동하면서 계산하며 값을 도출해냅니다.

다시 계산된 값들이 모여 Activation map을 만듭니다. 또한 원하는 만큼 필터 연산을 수행하여 여러개의 Activation map을 생성할 수 있습니다.

여기서 Activation map과 이미지의 사이즈가 다른 것을 볼 수 있는데 이것은 뒤에서 설명하도록 하겠습니다.

![Untitled 8](https://user-images.githubusercontent.com/80799025/234541704-a1dcc980-e843-4875-99dc-c2210f7b5147.png)

ConvNet은 신경망의 선형 레이어와 같은 방식으로 쌓인 레이어의 시퀸스가 될것입니다. 

위 예제에서는 filter계산을 한 후 Relu 활성화 함수를 함께 사용하여 Activation map을 만들었습니다.

다음 Activation map은 이전에 만들어진 Activation map으로부터 연산을 하게 됩니다.

![Untitled 9](https://user-images.githubusercontent.com/80799025/234541726-0d6cd713-7c0e-405a-ac6a-90df43f8b09a.png)

그리고 각 필터는 계층 구조를 학습하게 됩니다.

예로 들어 하위 계층에서는 edge를 추출하고, 중간 계층에서는 coner를 추출하고, 상위 계층에서는 직선을 추출하는 등 점점 더 복잡한 특성을 추출할 것입니다.

![Untitled 10](https://user-images.githubusercontent.com/80799025/234541748-9bad559b-9412-489a-948f-a954896b3d0c.png)

이러한 결과는 1959년에 생각했던 “뇌의 초기에는 간단한 세포가 있고 갈수록 복잡한 세포가 있으며 갈수록 복잡한 형상을 컨트롤 할 수 있다”는 가설과 현재 우리가 Convnet과 매우 유사하다는 것을 알 수 있습니다.

![Untitled 11](https://user-images.githubusercontent.com/80799025/234541775-92f796f3-4337-4623-b7a1-024fc73f2274.png)

위 이미지는 각 필터로 만들어진 Acitvation Map을 시각화 한 것입니다.

5x5 필터가 총 32개 있습니다. 

먼저 첫번째 필터를 보면 edge처럼 보입니다. 이미지의 edge를 찾는 역할을 하는 필터인 것입니다.

따라서 Convolution을 통해 이러한 종류의 형태가 얼마나 많이 일어나는지 보여주는 것입니다.

그래서 두 개의 신호 즉, 필터와 이미지의 Convolution 연산을 통해 진행되므로 Convolution Neural Network라 불립니다.  

![Untitled 12](https://user-images.githubusercontent.com/80799025/234541801-fabc658e-fcdd-4ed2-beb0-408e3821fd82.png)

Convolution의 전체 네트워크가 어떻게 생겼는지 보여주는 그림입니다.

input 이미지가 Conv 레이어를 통과하고, 비선형 함수인 RELU 레이어를 통과합니다. 

이 둘이 반복되다  pooling 레이어를 통과하는데 이는 activation map의 크기를 다운 샘플링 하는 것입니다.

결과적으로 마지막은 모든 Convolution 출력에 연결된 Fully-connected 레이어를 사용하고 이를 사용하여 최종 점수를 구합니다.

이제 위에서 봤던 예제에서 32X32 이미지가 어떻게 28X28의 크기로 나오게 되었는지 알아봅시다.

7X7 이미지와 3X3의 필터의 예시를 봅시다. filter는 이미지에서 다음과 같이 슬라이드 합니다.!

![Untitled 13](https://user-images.githubusercontent.com/80799025/234541847-76effd91-7f1a-4b5d-945d-b1a71d40db29.png)

필터는 왼쪽 상단에서 시작하며 곱 내적을 수행하고 결과값을 activation map의 왼쪽 상단부터 가져올 것입니다.

그리고 activation map의 크기는 5X5가 될 것입니다.

위의 예제는 Stride(보폭)이 1인 경우입니다. Stride가 2인 경우도 한번 보겠습니다.

![Untitled 14](https://user-images.githubusercontent.com/80799025/234541870-e12d7a80-a3d1-4460-a64e-b1f6a54f5294.png)

Stride가 2이므로 필터는 2칸씩 이동하게 됩니다.

결과적으로 3X3의 activation map을 얻을 것입니다.

다음은 Stride가 3인 경우를 보겠습니다.

![Untitled 15](https://user-images.githubusercontent.com/80799025/234541894-0bd6d7df-c73b-4e72-9746-499290145274.png)

Stride가 3인 경우는 비대칭 출력이 발생하기 때문에 시행하지 않습니다.

다시 말해 이미지와 맞지 않다는 것입니다.

그래서 이것을 통해 이미지와 필터 사이에서 나오는 activation map의 크기는 다음과 같이 식으로 정의할 수 있습니다.

![Untitled 16](https://user-images.githubusercontent.com/80799025/234541912-d1986b03-0987-43e5-818e-440fa416849c.png)

Output size는 (N-F)/Stride+1로 정의될 수 있습니다.

32x32 이미지와 5x5 필터, stride 1을 적용하면 (32-5)/1 +1   =28이 됩니다.

stirde 3인 경우에는 소수점 자리가 나오는 것을 볼 수 있는데 이는 맞지 않는 것입니다.

![Untitled 17](https://user-images.githubusercontent.com/80799025/234541930-d1b1add4-20d7-43a1-9353-fb499d8d2c3c.png)

실제로는  output의 크기를 원하는 대로 하기 위해 zero padding(제로 패딩)하는 것이 일반적입니다.

제로 패딩은 이미지의 가장자리에 0을 채워놓는 것입니다.

패딩 없이 연산을 반복적으로 수행하면 activation map의 크기가 작아질 것입니다.

다시 말하면 패딩을 사용하지 않고 매우 레이어가 여러개인 모델을 사용하면 output의 사이즈는 매우 작아질 것이라는 겁니다.

그리고 패딩이 없으면 일부 정보를 놓칠 수 있다는 단점이 있습니다. 

그래서 패딩은 출력 데이터의 크기를 입력 데이터와 동일하게 유지하여 정보의 손실을 방지하는 역할을 합니다.

그러면 7X7 이미지에 5X5 filter, stride 1, padding 1로 놓으면 output 사이즈는 어떻게 될까요?

Padding을 1로 넣어주면 N값은 2가 증가하여 9가 됩니다. 식에 대입하면

(9-3)/1+1 =7 이 됩니다.  즉 입력과 크기가 동일해집니다.

![Untitled 18](https://user-images.githubusercontent.com/80799025/234541948-6a3d6e8a-f3ee-48ad-8fa2-7e5597ef10d9.png)

또 다른 예제입니다. 

32x32x3 이미지에 10개의 5x5x3 필터를 stride 1, pad2로 적용하면 어떻게 될까요?

(32(image)+2*2(pad)-5(filter))/1+1 =32가 되어 32x32크기가 되고, 10개의 activation map이 생성되어 32x32x10의 형태가 될 것입니다.

각 필터의 parameter의 개수는 5x5x3+1(bias) = 76개가 될 것입니다. 그래서 모든 parameter의 개수는 76x10 = 760개가 됩니다.

![Untitled 19](https://user-images.githubusercontent.com/80799025/234541967-da73e99e-89dd-43c5-b869-201f570e9696.png)

요약입니다. 

K는 필터의개수, F는필터의 크기, S는 Stride, P는 zero-padding의 크기를 나타냅니다.

그 이외에 공식이 내용들은 위에서 언급하였던 내용이니 참고해주시기 바랍니다.

![Untitled 20](https://user-images.githubusercontent.com/80799025/234541986-c0b0bad2-48a3-4c21-bb47-5e73c3168813.png)

그리고 1x1사이즈의 필터로 하나하나 Convolution을 시행할 수 있습니다.

필터와 이미지의 깊이는 같아야 하므로, 각 필터는 1x1x64의 크기를 가지게 됩니다.

그리고 위와 같은 방식으로 진행하면 56x56 입력과 같은 크기가 나옵니다.

여기서 32개의 필터를 사용하였으니 결과는 56x56x32가 되는 겁니다.

![Untitled 21](https://user-images.githubusercontent.com/80799025/234542015-89aecafd-bc7d-42c5-b8b5-4333bcdb4016.png)

추가적으로 몇 가지 설명을 더 하겠습니다. 

filter로 부터 한개의 값이 나오는 부분이 뉴런과 유사한 오른쪽과 같은 과정을 거친다 생각하시면 됩니다.

필터로 이미지의 일부를 보는 부분을 receptive field라 합니다.

그리고 같은 activation map에 있는 값들은 같은 weight를 공유합니다.

![Untitled 22](https://user-images.githubusercontent.com/80799025/234542027-e9fec4ba-f43c-4f98-9c78-a98a04a614cb.png)

위 사진과 같이 activation map이 5개인 경우, 동그라미의 값들은 같은 receptive field로부터 나왔지만 다른 weight를 가집니다. 다시 말해 다른 activation map마다 weight를 공유하지 않는다는 이야기입니다.

이번에는 Pooling layer에 대해 알아봅시다.

![Untitled 23](https://user-images.githubusercontent.com/80799025/234542045-6d675492-b4ca-4c89-90e7-a85264164818.png)

Pooling layer를 사용하는 이유는 레이어를 더 작게 만들고 관리하기 쉽게 만들기 위해서입니다.

간단히 말해 다운 샘플링을 진행하는 것입니다.

Pooling은 깊이에 영향을 주지 못합니다. 때문에 입력과 출력의 깊이값은 같습니다.

![Untitled 24](https://user-images.githubusercontent.com/80799025/234542064-be102c6e-0885-4118-80a1-6ad9ede04d5a.png)

Max Pooling은 필터에서 가장 큰 값을 가져오는 Pooling 방법입니다.

위의 예시와 같이 2x2 필터안에서 가장 큰값을 반환하여 레이어의 크기를 줄입니다.

![Untitled 25](https://user-images.githubusercontent.com/80799025/234542088-5b5af565-7a9a-42ed-bffa-98c16d116484.png)

Pooling 필터의 크기를 F라 하고 Stride 를 S라 합니다.

F=2, S=2 또는 F=3, S=2로 흔히 사용됩니다.

또한 Pooling layer후의 사이즈는 (W-F)/S + 1로 표현 할 수 있습니다.




이번 강의에서는 CNN에 대한 기초적인 내용에 대해 배웠습니다.

아직 이해가 부족한 부분이 있지만 공부하시는 분들께 도움이 되었으면 좋겠습니다.
