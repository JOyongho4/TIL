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



