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

|모델|Loss|Accuracy|weighted f1|macro f1|
|:---:|---|---|---|:---:|
|CNN|0.4469|**0.8560**|**0.8557**|**0.8579**|
|BiLSTM|0.6785|0.8133|0.8138|0.8158|
|BiLSTM + LSTM|0.7841|0.8054|0.8060|0.8087|
|CNN + BiLSTM + LSTM|0.7143|0.8259|0.8264|0.8275|

* 이후 테스트는 가장 성능이 좋았던 CNN으로 계속 진행

# try 1

`Klue-Bert` 모델의 `cls_embedding`값을 input으로 가지는 MLP 모델을 사용

|모델|Loss|Accuracy|
|:---:|---|:---:|
|Klue-Bert + MLP|0.4844|0.8481|
