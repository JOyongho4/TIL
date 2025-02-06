# 트리 알고리즘

## 5-1
<br/>

**메서드**

```
<.info()>
    ㅇ 각 열의 데이터 타입 확인에 용이
    ㅇ 결측치 확인가능

<.describe()> 
    ㅇ 간략한 통계 (최소, 최대, 평균 등등)
    
```

**스케일링**
```
<train, test 분할>

from sklearn.model_selection import train_test_split
train_input, test_input, train_target, test_target = train_test_split(data,target, test_size=0.2, random_state=42)
```
```
<StandardScaler 사용>

from sklearn.preprocessing import StandardScaler
ss= StandardScaler()
ss.fit(train_input)
train_scaled=ss.transform(train_input)
test_scaled=ss.transform(test_input)
```
**결정트리**
```
<DecisionTreeClassifier 사용>

from sklearn.tree import DecisionTreeClassifier
dt= DecisionTreeClassifier(random_state=42)
dt.fit(train_scaled, train_target)
print(dt.score(trained_scaled, train_target))
print(dt.score(test_scaled, test_target))
```
```
<결정트리 시각화>

import matplotlib.pylot as plt
from sklearn.tree import plot_tree
plt.figure(figsize=(10,7))
plot_tree(dt)
plt.show()
```
```
<제한을 둔 결정트리 시각화>

    ㅇ max_depth로 깊이 조절
    ㅇ filled로 노드 색 지정
    ㅇ feature_names로 특성의 이름 전달
    ㅇ 노드엔 테스트 조건, 불순도, 총 샘플 수, 클래스별 샘플 수 적혀있음

plt.figure(figsize=(10,7))
plot_tree(dt, max_depth=1, filled=True, feature_names=['alchhol','sugar','pH'])
plt.show()
```
```
<지니 불순도>

    ㅇ 지니 불순도 = 1-(음성클래스 비율^2 + 양성 클래스 비율^2)
    ㅇ max=0.5, min=0
    ㅇ 부모노드와 자식 노드의 불순도 차이가 가능한 클수록 트리는 성장

<엔트로피>

    ㅇ -음성 클래스 비율 * log2(음성클래스 비율) - 양성 클래스 비율 * log2(양성 클래스 비율)

<부모, 자식노드 불순도 차이>

    ㅇ 부모의 불순도 - (왼쪽 노드 샘플 수 / 부모의 샘플 수) * 왼쪽 노드 불순도
                 - (오른쪽 노드 샘플 수 / 부모의 샘플 수) * 오른쪽 노드 불순도 = 정보이득
```
```
<가지치기>

    ㅇ 과적합 방지용
    ㅇ max_depth 지정하여 사용

dt = DecisionTreeClassifier(max_depth=3, random_state=42) 이런식으로
```


## 5-2


**테스트 일반화?**
```
테스트 데이터를 계속해서 사용하다보면 일반화할 수 있는 성능이 아닌,
그 테스트 데이터에 잘 맞는 모델이 만들어 질 수 있음

-> 검증 세트 사용
```
```
<검증 세트>

    ㅇ 트레인 세트와 테스트 세트 사이에 검증 세트(validation set)를 따로 만듦
    ㅇ train_test_split을 두번 사용
```