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
