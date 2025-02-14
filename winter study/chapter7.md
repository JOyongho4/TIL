# 딥러닝

## 7-1 인공 신경망
<br/>

**인공 신경망**

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

dense = keras.layers.Dense(10, activation='softmax',input_shape=(784,))

model = keras.Sequential(dense)
```
```
<활성화 함수>

    ㅇ 소프트맥스와 같이 뉴런의 선형 방정식 계산 결과에 적용되는 함수
```
**분류하기**
```
    ㅇ 케라스 모델은 훈련하기 전에 설정 단계가 있음
    ㅇ model 객체의 compile() 메서드에서 수행
    ㅇ 꼭 지정해야 할 것은 손실 함수의 종류

model.compile(loss='sparse_categorical_coressentropy',metrics='accuracy')
```
```
<인공 신경망 모델로 성능 향상>

    ㅇ 
```
