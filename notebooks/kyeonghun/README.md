# Data Augmentation

## 1. [EDA: Easy Data Augmentation Techniques for Boosting Performance on Text Classification Tasks](https://github.com/jasonwei20/eda_nlp)

> wordnet은 KAIST에서 만든 [Korean WordNet(KWN)](http://wordnet.kaist.ac.kr/) 을 사용

|type|description|
|:---:|:---:|
|SR|특정 단어를 유의어로 교체하는 방식.|
|RI|임의의 단어를 삽입(Insertion)하는 방식.|
|RD|임의의 단어를 삭제(Deletion)하는 방식.|
|RS|문장 내 임의의 두 단어의 위치를 바꾸는 방식.|

* 안전하게 데이터 증강을 하고 싶다면 RD, RS만을 사용 하는것을 추천 - 이 프로젝트에서도 RD, RS만 사용

----------

참조 링크 
* [KorEDA](github.com/catSirup/KorEDA/blob/master/eda.pygithub.com/catSirup/KorEDA/tree/master)

# base model

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
