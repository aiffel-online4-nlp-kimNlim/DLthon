# Data

### [DKTC (Dataset of Korean Threatening Conversations)](https://github.com/tunib-ai/DKTC)

데이터셋 구성

|클래스|Class No.|# Training|# Test |
|:----:|:------:|:------:|:------------:|
|협박|00|896|100|
|갈취|01|981|100|
|직장 내 괴롭힘|02|979|100|
|기타 괴롭힘|03|1,094|100|
|일반|04| - |100|

### Preprocess

#### 데이터 정제 (정규식 사용)

* 알파벳, 문장부호, 한글 제외는 제거
* 문장부호 양 옆에 공백 추가
* 불필요한 공백 제거

#### 토큰화

* `KoNLPy`의 `mecab`클래스를 사용

# Data Augmentation

### 1. [EDA: Easy Data Augmentation Techniques for Boosting Performance on Text Classification Tasks](https://github.com/jasonwei20/eda_nlp)

|구분|내용|예시|
|:---:|---|:---:|
|원본 문장|-|A sad, superior human comedy played out on the back roads of life.|
|Synonym Replacement|랜덤하게 유의어로 대치|A **lamentable**, superior human comedy played out on the **backward** road of life.|
|Random Insertion|랜덤한 위치에 랜덤한 단어를 삽입|A sad, superior human comedy played out on **funniness** the back roads of life.|
|Random Swap|랜덤하게 단어 순서 변경|A sad, superior human comedy played out on **roads** back **the** of life.|
|Random Deletion|랜덤하게 단어 삭제|A sad, superior human out on the roads of life.|

* wordnet은 KAIST에서 만든 [Korean WordNet(KWN)](http://wordnet.kaist.ac.kr/) 을 사용
* 한국어 데이터셋에서 안전하게 데이터 증강을 하고 싶다면 RD, RS만을 사용 하는것을 추천 - 이 프로젝트에서도 RD, RS만 사용
* 참조 - [KorEDA](github.com/catSirup/KorEDA/blob/master/eda.pygithub.com/catSirup/KorEDA/tree/master)

### 2. Synthetic Source Sentence (Back Translation)
> [Paper](https://arxiv.org/pdf/1511.06709.pdf)

* 문장을 특정 언어로 번역한 이후 번역된 문장을 다시 원래 언어로 다시 번역하는 방법
* 이 프로젝트에서는 영어와 일본어로만 테스트함
* `pororo`라이브러리를 사용해서 하려고 했지만 `torch`버전이 맞지않아서 설치 실패
* `deep-translator`라이브러리(구글번역) 사용해서 진행
* duplicate 검사 해본 결과 겹치게 생성된 문장은 10% 미만임

### 3. MixText

* 사전 학습된 `Masked Language Model`기반
* 문장의 랜덤한 위치에 마스킹을 하고, MLM 방식으로 학습한 모델을 이용하여 해당 마스크에 새로운 토큰을 생성해내는 방식으로 데이터를 증강하는 방식
* 참조 1 - [한국어 상호참조해결을 위한 BERT 기반 데이터 증강 기법](https://koreascience.kr/article/CFKO202030060835857.pub?&lang=ko&orgId=sighlt)
* 참조 2 - [MLM-data-augmentation](https://github.com/seoyeon9646/MLM-data-augmentation)
* 이건 너무 오래걸릴것 같아서 시간 남으면 해보겠음


# Base Model

### 프리트레인된 모델을 사용하기 전에 먼저 작은 모델로 테스트

#### 모델 종류

##### CNN
> * Embedding Layer
> * 2 One-Dimension Convolution Layers (Dropout: 50%)
> * 1-Dimension Global Max Pooling
> * Concatenate
> * Fully Connected Layer
> * 1-Dimension Fully Connected Layer
> * Softmax Activation

##### BiLSTM
> * Embedding Layer
> * Bidirectional LSTM (Dropout: 10%)
> * 1-Dimension Global Max Pooling
> * 1-Dimension Fully Connected Layer
> * Softmax Activation

##### BiLSTM + LSTM
> * Embedding Layer
> * Bidirectional LSTM + Residual Connection from Embedding Layer
> * Forward LSTM
> * 1-Dimension Global Max Pooling
> * 1-Dimension Fully Connected Layer (Dropout: 50%)
> * Softmax Activation

##### CNN + BiLSTM + LSTM
> * Embedding Layer
> * 1-Dimension Convolution Layer (Dropout: 50%)
> * Bidirectional LSTM + Residual Connection from Embedding Layer
> * Forward LSTM
> * 1-Dimension Global Max Pooling
> * 1-Dimension Fully Connected Layer (Dropout: 50%)
> * Softmax Activation

--------

|모델|Accuracy|weighted f1|macro f1|
|:---:|---|---|:---:|
|CNN|0.8367|0.8368|0.8369|
|BiLSTM|0.7911|0.7916|0.7912|
|BiLSTM + LSTM|0.8|0.7984|0.7984|
|CNN + BiLSTM + LSTM|0.7595|0.7611|0.7612|

* 이후 테스트는 가장 성능이 좋았던 CNN으로 계속 진행

# Data Set Test

### 1. 베이스 모델에 `Data Augmentation`을 얼마나 추가해야 성능이 잘 나오는지 테스트

|Data|Accuracy|weighted f1|macro f1|
|:---:|---|---|:---:|
|Origin Data|0.8354|0.8357|0.8359|
|Origin Data + RS(25%)|0.8468|0.8470|0.8472|
|Origin Data + RS(50%)|0.8341|0.8345|0.8349|
|Origin Data + RS(75%)|0.8455|0.8455|0.8459|
|Origin Data + RS(100%)|0.8329|0.8323|0.8326|
|Origin Data + RD(25%)|0.8430|0.8428|0.8432|
|Origin Data + RD(50%)|0.8417|0.8420|0.8423|
|Origin Data + RD(75%)|0.8417|0.8413|0.8415|
|Origin Data + RD(100%)|0.8367|0.8367|0.8370|
|Origin Data + BT-EN(25%)|0.8278|0.8293|0.8291|
|Origin Data + BT-EN(50%)|0.8354|0.8359|0.8360|
|Origin Data + BT-EN(75%)|0.8417|0.8425|0.8428|
|Origin Data + BT-EN(100%)|0.8177|0.8188|0.8184|
|Origin Data + BT-JA(25%)|0.8443|0.8446|0.8450|
|Origin Data + BT-JA(50%)|0.8380|0.8393|0.8393|
|Origin Data + BT-JA(75%)|0.8139|0.8146|0.8143|
|Origin Data + BT-JA(100%)|0.8302|0.8202|0.8204|

* 평균적으로 75%정도 증강시키는게 정확도가 가장 높은거 같음

### 2. `Data Augmentation`데이터 조합 테스트

|Data|Accuracy|weighted f1|macro f1|
|:---:|---|---|:---:|
|Origin Data|0.8354|0.8357|0.8359|
|Origin Data + RS(40%) + RD(35%)|0.8418|0.8419|0.8421|
|Origin Data + RS(40%) + BT-EN(35%)|0.8367|0.8364|0.8367|
|Origin Data + RS(40%) + BT-JA(35%)|0.8278|0.8282|0.8285|
|Origin Data + RD(40%) + BT-EN(35%)|0.8405|0.8406|0.8408|
|Origin Data + RD(40%) + BT-JA(35%)|0.8354|0.8361|0.8362|
|Origin Data + BT-EN(40%) + BT-JA(35%)|0.8379|0.8386|0.8387|
|Origin Data + RS(30%) + RD(25%) + BT-EN(20%)|0.8316|0.8321|0.8324|
|Origin Data + RS(30%) + RD(25%) + BT-JA(20%)|0.8265|0.8266|0.8268|
|Origin Data + RD(30%) + BT-EN(25%) + BT-JA(20%)|0.8379|0.8388|0.8390|
|Origin Data + RS(20%) + RD(20%) + BT-EN(20%) + BT-JA(15%)|0.8278|0.8286|0.8289|

* `EDA`방법으로 증강한 데이터만 추가하는 것이 정확도가 가장 높았음
* 이후 테스트는 Origin Data + RS(40%) + RD(35%) 로 진행


# Transfer Learning

### 1. `Bert` Model fine-tuning

* `Klue-Bert`로 시도했으나 OOM 에러, 다른 방법을 찾거나 분류기만 추가해야될듯


### 2. `cls embedding` 활용

> [CLS] 토큰은 문장의 시작 부분, [SEP] 토큰은 문장의 끝에 추가된다. BERT는 문맥을 이해하기 위해 문장의 각 단어(토큰)을 다른 모든 단어와 연결시켜 이해하는 계산 과정을 거치는데, 이 과정에서 [CLS] 토큰은 다른 모든 토큰의 집계 표현을 보유하고 있으므로 문장 전체에 대한 표현을 담고 있다.

* `Bert` 모델의 마지막 블록의 `[CLS]` 토큰을 사용해서 문장의 피쳐를 뽑고, 피쳐 데이터를 활용해서 분류기를 학습시킨다.

  
|Model|Accuracy|weighted f1|macro f1|
|:---:|---|---|:---:|
|`klue/bert-base` + MLP|0.8468|0.8471|0.8472|
|`beomi/kcbert-base` + MLP|0.8557|0.8554|0.8554|
|`beomi/KcELECTRA-base` + MLP|0.4253|0.3663|0.3684|
|`tunib/electra-ko-base` + MLP|0.2493|0.0995|0.0998|
|`klue/roberta-large` + MLP|0.2493|0.0995|0.0998|
|`klue/bert-base` + CNN|0.4468|0.4314|0.4325|
|`beomi/kcbert-base` + CNN|0.2646|0.1662|0.1670|

