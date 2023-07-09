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

### 1. 프리트레인된 모델을 사용하기 전에 먼저 작은 모델로 테스트

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

|모델|Loss|Accuracy|weighted f1|macro f1|
|:---:|---|---|---|:---:|
|CNN|0.4469|**0.8560**|**0.8557**|**0.8579**|
|BiLSTM|0.6785|0.8133|0.8138|0.8158|
|BiLSTM + LSTM|0.7841|0.8054|0.8060|0.8087|
|CNN + BiLSTM + LSTM|0.7143|0.8259|0.8264|0.8275|

* 이후 테스트는 가장 성능이 좋았던 CNN으로 계속 진행

### 1. 베이스 모델에 `Data Augmentation`을 얼마나 추가해야 성능이 잘 나오는지 테스트

* test 1

|Data|Accuracy|weighted f1|macro f1|
|:---:|---|---|:---:|
|Origin Data + RS(25%)|0.8759|0.8753|0.8763|
|Origin Data + RS(50%)|0.8998|0.8995|0.8994|
|Origin Data + RS(75%)|0.8933|0.8935|0.8936|
|Origin Data + RS(100%)|0.9098|0.9099|0.9098|
|Origin Data + RD(25%)|0.8506|0.8510|0.8527|
|Origin Data + RD(50%)|0.8745|0.8748|0.8759|
|Origin Data + RD(75%)|0.8770|0.8771|0.8754|
|Origin Data + RD(100%)|0.9161|0.9161|0.9151|
|Origin Data + BT-EN(25%)|0.8519|0.8517|0.8525|
|Origin Data + BT-EN(50%)|0.8470|0.8475|0.8468|
|Origin Data + BT-EN(75%)|0.8462|0.8477|0.8459|
|Origin Data + BT-EN(100%)|0.8528|0.8523|0.8510|
|Origin Data + BT-JA(25%)|0.8278|0.8277|0.8272|
|Origin Data + BT-JA(50%)|0.8259|0.8262|0.8268|
|Origin Data + BT-JA(75%)|0.8092|0.8097|0.8091|
|Origin Data + BT-JA(100%)|0.8164|0.8155|0.8164|

* `EDA`방법으로 증강한 데이터는 추가 할 수록 정확도가 높아지진다
* `Back Translation`방법으로 증강한 데이터는 50%를 넘어가면 오히려 정확도가 낮아지는 경우도 생김

# try 1

`Klue-Bert` 모델의 `cls_embedding`값을 input으로 가지는 MLP 모델을 사용

|모델|Loss|Accuracy|
|:---:|---|:---:|
|Klue-Bert + MLP|0.4844|0.8481|
