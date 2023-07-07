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

### 3. MixText

* 사전 학습된 `Masked Language Model`기반
* 문장의 랜덤한 위치에 마스킹을 하고, MLM 방식으로 학습한 모델을 이용하여 해당 마스크에 새로운 토큰을 생성해내는 방식으로 데이터를 증강하는 방식
* 참조 1 - [한국어 상호참조해결을 위한 BERT 기반 데이터 증강 기법](https://koreascience.kr/article/CFKO202030060835857.pub?&lang=ko&orgId=sighlt)
* 참조 2 - [MLM-data-augmentation](https://github.com/seoyeon9646/MLM-data-augmentation)
* 이건 너무 오래걸릴것 같아서 시간 남으면 해보겠음


# Base Model

|모델|Loss|Accuracy|
|:---:|---|:---:|
|CNN|0.4975|0.8212|
|BiLSTM|0.7638|0.7627|
|BiLSTM + LSTM|0.6237|0.8291|
|CNN + BiLSTM + LSTM|0.6968|0.8038|

# try 1

`Klue-Bert` 모델의 `cls_embedding`값을 input으로 가지는 MLP 모델을 사용

|모델|Loss|Accuracy|
|:---:|---|:---:|
|Klue-Bert + MLP|0.4844|0.8481|
