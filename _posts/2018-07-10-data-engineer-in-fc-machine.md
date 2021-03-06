---
layout: post
title:  "rnn 로 단어예측 모델 만들기"
subtitle:   "rnn 로 단어예측 모델 만들기"
description : "rnn "
keywords : "rnn , rnn model, python numpy, machine learning"
categories: data
tags: tensorflow
comments: true
---

실습

나무위키 에서 글자소를 따서 사전을 만든다

**머신러닝 할땐 shape 를 찍어서 어떤 모양으로 이루어져 있는지 자주 확인해줘야 한다**

>size 별로 나누어서 처리 한다 <br>
>지금 실습에선 40개, 60개 덩어리를 나누어서 한다<br>
```x축, y축 으로 나누는건가??```

일정 단위 만큼 짤라서 덩어리를 만들어서 나누어서 학습한다

x축, y축은 rnn data 모델에서 형태소를 하나씩 시프트 해서 저장하므로
x축은 사전으로 만든 데이터가
y축은 하나하나 시프트 된 데이터가 들어간다


```
data[0:20]

'(ㅅㅣㄴᴥ ㅅㅔᴥㄱㅖᴥㅅㅜᴥㅇㅢᴥ ㅁ'

tensor[0:20]

array([ 14, 198, 228, 181, 177,   6, 198, 213, 177, 178, 215, 177, 198,
       221, 177, 200, 227, 177,   6, 194])

xdata = 14, 198, 228, 181, 177...
ydata = 198, 228, 181, 177,   
```   

numpy 에 reshape 를 사용하여 재조정한다

reshape 가 사용하기 까탈스러움 많이 써서 익숙해져라

```
np.array(range(30)).reshape(2, -1) 을 실행하면

array([[ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14],
       [15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29]])

np.split(np.array(range(30)).reshape(2, -1), 3, 1) 을 실행하면

[array([[ 0,  1,  2,  3,  4],
        [15, 16, 17, 18, 19]]), array([[ 5,  6,  7,  8,  9],
        [20, 21, 22, 23, 24]]), array([[10, 11, 12, 13, 14],
        [25, 26, 27, 28, 29]])]
```

**아래 코드를 실행해서 학습 데이터를 만든다**

```
x_batches = np.split(xdata.reshape(batch_size, -1), num_batches, 1)
y_batches = np.split(ydata.reshape(batch_size, -1), num_batches, 1)
```

프로덕트를 만들기 위해선 좀 더 해야하지만 실습이고 어렵지 않게 하기 위해 적정선 짤라내고 진행한다

이제 만들어진 학습데이터가 만들어졌으니 학습 시켜보자
rnn model 은 한층으로 구성되어있지 않고 여러층으로 구성되어있다.***(왜??찾아보던가 하자...또 영어겠지..)***

placeholder 를 사용할때
```
tf.placeholder(tf.int32, [none, none], 'input_data')
```

>none, none 을 넣는 이유는 batch_size, seq_length 를 넣어주면 되지만 차후 유용성을 위해 none, none 으로 넣어준다.

>initial_state 는 기존 rnn model 에 state(기억력)을 담당하는 놈이다. 기억하게 넣어준다(lstm을 사용하기에 rnn 보다 많은 내용을 state에 담을수 있다)

다음 학습을 위해서 rnn에서는 결과물인 outputs 과 final_state 로 두가지를 가진다.
요청한 결과물이 output 이고 학습된 내용을 final_state에서 가지고 있어서 차후 학습시 final_state에서 부터 시작한다.


softmax 를 사용해서 ***(창세기전 만든 소맥 아니다 복잡한 녀석이다...모른다..여튼 쓴다)***

rnn을 사용하기 위해 cost를 만든다

>gradient clipping 은 중요한 녀석이지만 지금 당장 설명하기엔 어려운 내용
>AdamOptimizer를 사용한다, gradientOptimize ?? 보다 업글된 녀석이라함


###### 이거....전에 한 수업내용 정리한거 부터 정리해야 할 텐데...어이할꼬......허허허
###### 자신감 어디갔나요...ㅠㅠ


>중간에 취소하고 다른 테스트 소스를 돌려서 보면
말이 안되는게 나온다.. gpu 있는 컴터로 2일을 테스트 했을때 맞춤법은 맞는 한글로 나오지만..<br>
중간에 취소 해서 한 10분 정도 학습한게 날라갔다..이번에는 중간에 멈췄을 경우?<br> 여튼 중간에 멈춰질 때를 대비하여 모델 저장, 저장된 모델을 불러서 다시 학습재개 하는 실습을 해보자

test 환경은 초기 제시어를 주고 다음 단어를 예측 하여 만든다. <br>
테스트 코드는 <br>
약간이나마 학습된 데이터 에서 rnn이 다음 글자를 뱉어주는 녀석이니  초기어를 가지고 그 다음 글자를 만들어서 붙여준다  <br>
반복문으로 <br>
feed_dic 에 단어, state를 넣고
나올 확률, state를 구해서 단어를 붙인다

집에서 학습시킬때는 embed_dim 을 512로 바꿔봐도 된다

### 데.꿀.멍 의 시간....



로그를 볼때는 로그 디렉토리에 가서 **tensorboard --logdir=. **


## 수업 정리

rnn 은 언어모델 과 유사한...
