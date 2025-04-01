# finance-forecasting-xrpusdt-lstm

## 개요

2018년부터 2024년까지 XRP/USDT 데이터셋을 사용

일봉 기준(daily timeframe)으로 기록되었으며, 시가(open), 종가(close), 고가(high), 저가(low), 거래량(volume), 변동률(percentage change) 등의 정보가 포함

SMA (단순 이동 평균), 볼린저 밴드 등의 다양한 기술적 지표를 활용

- SMA,  볼린저 밴드 란?
```
SMA : 단순 이동 평균선, 일정 기간 동안(최근 5일 등)의 종가 등의 평균을 계산한 값
볼린저 밴드 : SMA를 기준으로 표준편차를 활용해 상단 밴드와 하단 밴드를 만들어 가격의 변동범위를 시각화하는 지표
```

- SMA가 주는 핵심 인사이트
```
기술적 분석 도구 : 금융 시장의 추세와 전환점을 파악하기 위해 널리 사용됨
단기적인 가격 변동을 평준화하여 자산의 전반적인 방향성을 이해하는데 도움을 줌
시각적 추세 파악 : 가격 차트에 SMA를 함께 표시하면, 자산이 상승 추세, 하락 추세, 또는 횡보 구간에 있는지 판단할 수 있음
```


- LSTM 이란?
```
다양한 시계열 예측 문제에서 효과적인 성능을 보여주는 딥러닝 기반 모델
과거 데이터의 복잡한 패턴과 의존성을 학습할 수 있어, 방향성 예측의 정확도를 높이는 데 도움
```

- PyTorch 란?
```
딥러닝을 코드로 쉽게 구현할 수 있게 해주는 도구
LSTM 모델을 유연하고 효율적으로 설계하고 학습할 수 있는 딥러닝 프레임워크
```


## 코드




```py
df.drop(['Change %'], axis=1, inplace=True)
```
-> Change % 제거, 열기준 삭제, df 원본을 직접 수정
<w/>






```py
df = df.iloc[::-1]
df.reset_index(inplace=True)
```
-> 데이터 뒤집기, 인덱스 다시 0부터 설정

-> 이걸 하는 이유? LSTM은 과거 -> 현재 -> 미래로 학습하기 때문
<w/>

```py
df['Date'] = pd.to_datetime(df['Date'], dayfirst=True)
```
-> datetime으로 변환, DD/MM/YYYY로 해석
<w/>






```py
fig = make_subplots(rows=2, cols=1, shared_xaxes=True,
               vertical_spacing=0.03, subplot_titles=('Ripple', 'Volume'),
               row_width=[0.2, 0.7])

fig.add_trace(go.Candlestick(x=df["Date"], open=df["Open"], high=df["High"],
                low=df["Low"], close=df["Close"], name="Ripple"),
                row=1, col=1
)

fig.update_layout(
    yaxis_title='XRPUSDT',
    shapes = [dict(
        x0='2018-05-22', x1='2024-05-22', y0=0, y1=1, xref='x', yref='paper',
        line_width=2)],
)

fig.add_trace(go.Bar(x=df['Date'], y=df['Volume'], showlegend=False), row=2, col=1)

fig.update_layout(xaxis_rangeslider_visible=False)
fig.update(layout_xaxis_rangeslider_visible=False)

fig.show()
```
-> 출력이 안됨, 다시 공부
<w/>





```py
df["5d_sma"] = df["Close"].rolling(5).mean()
df["9d_sma"] = df["Close"].rolling(9).mean()
df["17d_sma"] = df["Close"].rolling(17).mean()
```
-> SMA를 계산하는 방법

- df["5d_sma"] = df["Close"].rolling(5).mean() : 최근 5일간 평균값 계산 등등

-> rolling(N)은 N일치 윈도우를 만들어서 계산
<w/>




```py
df['5d_sma'] = df['5d_sma'].fillna(df['Close'])
df['9d_sma'] = df['9d_sma'].fillna(df['Close'])
df['17d_sma'] = df['17d_sma'].fillna(df['Close'])
```
-> 결측치 (가장 앞에 값들)는 해당 종가로 대체





```py
plt.figure(figsize=(20,10), dpi=200)
plt.plot(df['Date'], df['Close'], label='XRPUSDT')
plt.plot(df['Date'], df['5d_sma'], label='SMA 5')
plt.plot(df['Date'], df['9d_sma'], label='SMA 9')
plt.plot(df['Date'], df['17d_sma'], label='SMA 17')
plt.title('XRPUSDT')
plt.ylabel('Price')
plt.xlabel('Date')
plt.legend(loc='upper right')
plt.show()
```
-> Matplotlib 기반 시계열 시각화

-> 실제 종가와 3개의 SMA를 한 그래프에 시각화

-> dpi는 해상도개념



```py
plt.plot(df['Date'], df['Close'], label='XRPUSDT')
plt.plot(df['Date'], df['5d_sma'], label='SMA 5')
plt.plot(df['Date'], df['9d_sma'], label='SMA 9')
plt.plot(df['Date'], df['17d_sma'], label='SMA 17')
```
-> date별 각각의 가격을 시각화함




```py
fig = go.Figure(data=go.Candlestick(x=df["Date"], open=df["Open"], high=df["High"],
                low=df["Low"], close=df["Close"], name="XRP"))
fig.update_layout(xaxis_rangeslider_visible=False)

fig.add_trace(go.Scatter(x=df['Date'],y=df['5d_sma'], line_color='#7295ee', name="SMA5",mode='lines'))
fig.add_trace(go.Scatter(x=df['Date'],y=df['9d_sma'], line_color='#fcb539', name="SMA9",mode='lines'))
fig.add_trace(go.Scatter(x=df['Date'],y=df['17d_sma'], line_color='#bd39fc', name="SMA17",mode='lines'))

fig.update_layout(
    autosize=False,
    width=1200,
    height=600,
)

fig.show()
```
-> XRP 캔들차트 위에 SMA 5일, 9일, 17일 이동 평균선을 덧그려서 시각적 추세를 보여줌

```py
fig = go.Figure(data=go.Candlestick(
    x=df["Date"], open=df["Open"], high=df["High"],
    low=df["Low"], close=df["Close"], name="XRP"
))
```
-> 가격의 고가/저가/시가/종가를 시각화

-> name = 'XRP' 는 Candlestick이 어떤 자산을 나타내는지 명시해주는 이름
<w/>






```py
# Define parameters
window_size = 15
num_std = 4
```
-> 볼린저 밴드 계산에 쓸 구간 = 15, 표준편차 4배 범위로 밴드를 그리기

```py
# Calculate rolling mean and standard deviation 5d
rolling_mean_5 = np.convolve(df['5d_sma'], np.ones(window_size)/window_size, mode='valid')
```
-> window_size 만큼 평균 계산, 끝부분 NaN 없이 잘라냄

-> np.convolve : 합성곱 연산

-> np.ones(window_size)/window_size : 각 구간의 값을 동일한 가중치로 평균 냄

```py
rolling_std_5 = np.std([df['5d_sma'][i:i+window_size] for i in range(len(df['5d_sma'])-window_size+1)], axis=1)
```
-> 각 구간별로 15일치 표준편차를 만들고 np.std로 계산

```py
# Calculate Bollinger Bands 5d
upper_band_5 = rolling_mean_5 + num_std * rolling_std_5
lower_band_5 = rolling_mean_5 - num_std * rolling_std_5
```
-> 상단/하단 밴드 만들기

```py
# Calculate rolling mean and standard deviation 9d
rolling_mean_9 = np.convolve(df['9d_sma'], np.ones(window_size)/window_size, mode='valid')
rolling_std_9 = np.std([df['9d_sma'][i:i+window_size] for i in range(len(df['9d_sma'])-window_size+1)], axis=1)

# Calculate Bollinger Bands 9d
upper_band_9 = rolling_mean_9 + num_std * rolling_std_9
lower_band_9 = rolling_mean_9 - num_std * rolling_std_9

# Calculate rolling mean and standard deviation 17d
rolling_mean_17 = np.convolve(df['17d_sma'], np.ones(window_size)/window_size, mode='valid')
rolling_std_17 = np.std([df['17d_sma'][i:i+window_size] for i in range(len(df['17d_sma'])-window_size+1)], axis=1)

# Calculate Bollinger Bands 17d
upper_band_17 = rolling_mean_17 + num_std * rolling_std_17
lower_band_17 = rolling_mean_17 - num_std * rolling_std_17
```
-> 9일 17일 동일한 수행
<w/>




```py
# Plotting 5d
plt.figure(figsize=(20,7))
plt.plot(df['Close'], label='Stock Price')
plt.plot(rolling_mean_5, label='SMA5', color='red')
plt.plot(upper_band_5, label='Upper Bollinger Band', color='green')
plt.plot(lower_band_5, label='Lower Bollinger Band', color='green')
plt.fill_between(np.arange(window_size-1, len(df['5d_sma'])), lower_band_5, upper_band_5, color='grey', alpha=0.2)
plt.title('Bollinger Bands (5d)')
plt.xlabel('Days')
plt.ylabel('Price')
plt.legend()
plt.grid(True)
plt.show()
```
-> SMA5를 기준으로 한 볼린저 밴드
-> 실제 가격 흐름(close), 5일 이동 평균선(SMA5), 위아래 볼린저 밴드(upper, lower_band5)





```py
df['Days'] = df.index + 1
```
-> 날짜 칼럼 추가



```py
_5d = df[['Open','High','Low','Days', '5d_sma','Volume','Close']].copy(deep=True)
_9d = df[['Open','High','Low','Days', '9d_sma','Volume','Close']].copy(deep=True)
_17d = df[['Open','High','Low','Days', '17d_sma','Volume','Close']].copy(deep=True)
_all = df[['Open','High','Low','Days', '5d_sma', '9d_sma', '17d_sma','Volume','Close']].copy(deep=True)
```
-> 5d_SAM, 9d_SMA, 17d_SMA에 따라 새로운 df생성, .copy(deep=True) : 원본과 독립적





```py
scaler = MinMaxScaler(feature_range=(0,2)).fit(_5d.Low.values.reshape(-1,1))
_5d['Open'] = scaler.transform(_5d.Open.values.reshape(-1,1))
_5d['High'] = scaler.transform(_5d.High.values.reshape(-1,1))
_5d['Low'] = scaler.transform(_5d.Low.values.reshape(-1,1))
_5d['Close'] = scaler.transform(_5d.Close.values.reshape(-1,1))
_5d['Volume'] = scaler.transform(_5d.Volume.values.reshape(-1,1))
_5d['Days'] = scaler.transform(_5d.Days.values.reshape(-1,1))
_5d['5d_sma'] = scaler.transform(_5d['5d_sma'].values.reshape(-1,1))
```
-> 다양한 범위의 숫자값들을 0~2로 맞춰주는 작업

-> reshape(-1,1) -> ID 벡터를 2차원 배열로 변환 (필수)

-> 1차원 배열을 2차월 '열 벡터' 형태로 바꾸는 것, 입력 데이터를 항상 2차원 형태로 해야함





```py
data_5d_all = _5d[['Open','High','Low','Close', '5d_sma', 'Volume', 'Days']].values
```
-> _5d 데이터프레임에서 7개의 칼럼만 선택하고, 넘파이 배열로 변환



- 잠깐!! 넘파이와 판다스?
넘파이는 행렬 집합/계산 느낌, 판다스는 엑셀 시트 느낌


```py
seq_len= 11
sequences_5d_all=[]
for index in range(len(data_5d_all) - seq_len + 1):
    sequences_5d_all.append(data_5d_all[index: index + seq_len])
sequences_5d_all = np.array(sequences_5d_all)
print(sequences_5d_all.shape)
```
-> 원래 데이터(data_5d_all)에서 길이 11짜리 시퀸스를 한 칸씩 밀면서 생성
```예제
[[1]
[2]
[3]
[4]
[5]
[6]
[7]]

에서
seq_len = 4, for i in range(len(data) - seq_len + 1) 이면

[[1]
 [2]
 [3]
 [4]]

[[2]
 [3]
 [4]
 [5]]

[[3]
 [4]
 [5]
 [6]]
이런식으로
```




```py
valid_set_size_percentage = 10
test_set_size_percentage = 10
```
-> 검증 10%, 테스트 10% 사용, 나머지는 80%




```py
valid_set_size_5d_all = int(np.round(valid_set_size_percentage/100*sequences_5d_all.shape[0]))
test_set_size_5d_all  = int(np.round(test_set_size_percentage/100*sequences_5d_all.shape[0]))
train_set_size_5d_all = sequences_5d_all.shape[0] - (valid_set_size_5d_all + test_set_size_5d_all)
```
-> 훈련, 검증, 테스트를 일정 비율로 데이터 크기 계산



```py
x_train_5d_all = sequences_5d_all[:train_set_size_5d_all,:-1,:]
y_train_5d_all = sequences_5d_all[:train_set_size_5d_all,-1,:]
```
-> x_train : 10일치 입력, y_train : 11번째 날의 출력

```py
x_valid_5d_all = sequences_5d_all[train_set_size_5d_all:train_set_size_5d_all+valid_set_size_5d_all,
                                  :-1,:]
y_valid_5d_all = sequences_5d_all[train_set_size_5d_all:train_set_size_5d_all+valid_set_size_5d_all
                                  ,-1,:]
```
-> x_valid : 10일치 입력, y_train : 11번째 날 타겟

```py
x_test_5d_all = sequences_5d_all[train_set_size_5d_all+valid_set_size_5d_all:,:-1,:]
y_test_5d_all = sequences_5d_all[train_set_size_5d_all+valid_set_size_5d_all:,-1,:]
```
->

train_set_size_5d_all+valid_set_size_5d_all:  :  학습 + 검증 세트를 제외한 마지막 부분


:-1   : 시퀀스에서 마지막 시점 제외


-1   : 시퀀스의 마지막 시점 -> 예측 타겟 y





```py
x_train_5d_all = torch.tensor(x_train_5d_all).float()
y_train_5d_all = torch.tensor(y_train_5d_all).float()

x_valid_5d_all = torch.tensor(x_valid_5d_all).float()
y_valid_5d_all = torch.tensor(y_valid_5d_all).float()
```
-> 넘파이를 Pytorch Tensor로 변환


```py
train_dataset_5d_all = TensorDataset(x_train_5d_all,y_train_5d_all)
```
-> 두 개 이상의 텐서를 묶어서 샘플 단위로 관리하는 Pytorch 도구


```py
train_dataloader_5d_all = DataLoader(train_dataset_5d_all, batch_size=32, shuffle=False)
```
-> 모델 학습 시 데이터를 미니배치 단위로 자동 분할해서 공급


```py
valid_dataset_5d_all = TensorDataset(x_valid_5d_all,y_valid_5d_all)
valid_dataloader_5d_all = DataLoader(valid_dataset_5d_all, batch_size=32, shuffle=False)
```





```py
class NeuralNetwork(nn.Module):
    def __init__(self, num_feature):
        super(NeuralNetwork, self).__init__()
        self.lstm  = nn.LSTM(num_feature,64,batch_first=True)
        self.fc    = nn.Linear(64,num_feature)


    def forward(self, x):
        output, (hidden, cell) = self.lstm(x)
        x = self.fc(hidden)
        return x
```
-> PyTorch 기반의 LSTM을 이용한 시계열 예측 모델


```py
model_5d_all = NeuralNetwork(7)
```
-> 입력 피쳐 수가 7개인 LSTM 모델을 생성하는 코드

```py
optimizer = optim.Adam(model_5d_all.parameters())
mse = nn.MSELoss()
```
-> 옵티마이저 설정(Adam 최적화 알고리즘), 손실 함수 설정(MSELoss)




```py
def train(dataloader):
    epoch_loss = 0
    model_5d_all.train()

    for batch in dataloader:
        optimizer.zero_grad()
        x,y= batch
        pred = model_5d_all(x)
        loss = mse(pred[0],y)
        loss.backward()
        optimizer.step()
        epoch_loss += loss.item()

    return epoch_loss
```
-> PyTorch 모델 학습을 한 epoch 동안 수행하는 함수



```py
def evaluate(dataloader):
    epoch_loss = 0
    model_5d_all.eval()

    with torch.no_grad():
        for batch in dataloader:
            x,y= batch
            pred = model_5d_all(x)
            loss = mse(pred[0],y)
            epoch_loss += loss.item()

    return epoch_loss / len(dataloader)
```
-> evaluate() 함수는 모델의 검증 또는 테스트용 손실을 계산하는 함수





```py
n_epochs = 100
best_valid_loss_5d_all = float('inf')

for epoch in range(1, n_epochs + 1):

    train_loss_5d_all = train(train_dataloader_5d_all)
    valid_loss_5d_all = evaluate(valid_dataloader_5d_all)

    #save the best model
    if valid_loss_5d_all < best_valid_loss_5d_all:
        best_valid_loss_5d_all = valid_loss_5d_all
        torch.save(model_5d_all, 'saved_weights_5d_all.pt')

    # print("Epoch ",epoch+1)
    print(f'\tTrain Loss: {train_loss_5d_all:.5f} | ' + f'\tVal Loss: {valid_loss_5d_all:.5f}\n')
```
-> 총 100 epoch 동안 반복 학습

-> 현재까지의 최저 검증 손실을 저장할 변수

-> 반복 루프 : 매 epoch 마다 학습, 검증