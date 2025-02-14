# 딥러닝

## 7-1 인공 신경망
<br/>

**인공 신경망**

```
<인공 신경망>

    ㅇ 출력층 : 신경망의 최종 값
    ㅇ z 값 : 활성화 함수의 입력
    ㅇ 뉴런 : z 값을 계산하는 단위, z 값이 작으면 비활성화 될 수도 있음
    ㅇ 입력층 : 픽셀값 자체
```
```
<텐서플로>

    ㅇ 머신러닝과 달리 딥러닝 라이브러리는 GPU를 사용하여 인공 신경망을 훈련
    ㅇ GPU는 벡터와 행렬 연산에 매우 최적화되서 곱셈과 덧셈이 많이 수행됨

from tensorflow import keras
(train_input, train_target), (test_input, test_target) =\
    keras.datasets.fashion_mnist.load_data()
```
```
<처음 10개 시각화>

import matplotlib.pyplot as plt
fig, axs = plt.subplots(1,10, figsize=(10,10))
for i in range(10):
    axs[i].imshow(train_input[i], cmap='gray_r')
    axs[i].axis('off')
plt.show()
```
```
<타킷 값 확인>

import numpy as np
print(np.uniquue(train_target, return_counts=True))
```
```
<reshape() 메서드>

    ㅇ 첫번째 차원은 변하기 않고, 두 번쨰, 세 번째 차원이 1차원으로 합쳐짐

train_scaled = train_input / 255.0
train_scaled = train_scaled.reshape(-1,28*28)
```


**인공신경망으로 모델 만들기**
```
인공 신경망에서는 교차 검증을 잘 사용하지 않고 검증 세트를 별도로 덜어내어 사용
    -> 딥러닝 분야의 데이터셋은 충분히 크기 때문에 검증 점수가 안정적이고
    -> 교차 검증을 수행하기에는 훈련 시간이 너무 오래 걸림
```
```
<dense 클래스>

    ㅇ 밀집층 생성
    ㅇ 뉴런은 타킷 변수 숫자로

dense = keras.layers.Dense(10, activation='softmax',input_shape=(784,))

model = keras.Sequential(dense)
```
```
<활성화 함수>

    ㅇ 소프트맥스와 같이 뉴런의 선형 방정식 계산 결과에 적용되는 함수
```
**분류하기**
```
<케라스 모델>

    ㅇ 케라스 모델은 훈련하기 전에 설정 단계가 있음
    ㅇ model 객체의 compile() 메서드에서 수행
    ㅇ 꼭 지정해야 할 것은 손실 함수의 종류
    ㅇ 음성샘플은 0이기에 어떤 곱이든 0이 되므로 1로 바꿔서 계산 (1-타깃값)
    ㅇ 각 클래스에 대한 확률이 모두 출력되기 때문에 타깃에 해당하지 않는 나머지 확률에는 0을 곱함 (다중)

model.compile(loss='sparse_categorical_coressentropy',metrics='accuracy')
```

## 7-2 심층 신경망

**2개의 층**

```
<은닉층>

    ㅇ 입력층과 출력층 사이에 있는 모든 층
    ㅇ 활성화 함수 : 신경망 층의 선형 방정식의 계산 값에 적용하는 함수
    ㅇ 이진분류 : 시그모이드, 다중분류 : 소프트맥스
```
```
<시그모이드 함수>

    ㅇ 뉴런의 값을 0과 1 사이로 압축

dense1 = keras.layers.Dense(100, activation='sigmoid', input_shape=(784,))
dense2 = keras.layers.Dense(10, activation='softmax')

-> dense1은 은닉층이고 100개의 뉴런을 가진 밀집층
    뉴런 개수를 정할때는 적어도 출력층의 뉴런보다는 많게 해야함
-> dense2는 출력층, 10개의 클래스를 분류하므로 10개의 뉴런을 둠
    활성화함수는 소프트맥스 함수로 지정
```
**심층 신경망 만들기**
```
model = keras.Sequential([dense1, dense2])
```
인공신경만의 강력한 성능은 층을 추가하여 입력 데이터에 대해 연속적인 학습을 진행하는 능력에서 나옴
```
model = keras.Sequential([
    keras.layers.Dense(100, activation='sigmoid', input_shape=(784,),
    name='hidden')
    keras.layers.Dense(10, activation='softmax',name='output')
    ], names='패션 MNIST 모델')
```
**렐루 함수**
```
초창기 인공 신경망의 은닉층에 많이 사용된 활성화 함수는 시그모이드
시그모이드의 단점은 끝으로 갈수록 그래프가 누워있기 때문에 올바른 출력을 만드는데 신속하게 대응하지 못함
그 대안으로 나온것이 렐루 함수
    -> 음수=0, 양수-> 입력 통과 , max(0,z)
```
**옵티마이저**
```
케라스는 다양한 종류의 경사 하강법 알고리즘을 제공하는데 이를 옵티마이저라고 부름
가장 기본 옵티마이저 : 확률적 경사 하강법 SGD
```
**적응적 학습률**
```
모델의 최적점에 가까이 갈수록 학습률을 낮출 수 있음
학습률 매개변수를 튜닝하는 수고를 덜 수 있는것이 장점
대표적인 옵티마이저는 Adagrad와 RMSprop
```