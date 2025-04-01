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