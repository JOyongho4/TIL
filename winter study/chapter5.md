# 트리 알고리즘

## 결정 트리
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
