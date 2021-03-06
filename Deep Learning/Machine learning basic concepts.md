# 01. Machine learning basic concepts

## 1. Machine learning
- 프로그램 자체가 데이터를 보고 스스로 학습해서 배우는 프로그램
- 일종의 소프트웨어
- explicit programming(사용자의 입력에 따른 출력이 정해져 있는 경우)의 한계 ex) Spam filter, Automatic driving => rule들이 너무 많아 복잡하기 때문에 explicit하게 프로그래밍하기 힘든 경우가 존재
- 프로그램이 학습하는 방법에 의해 두 가지로 나누어진다. (_Supervised_/_Unsupervised_)
- __Supervised learning__: labeled examples (training set)을 가지고 학습
![supervised example](https://github.com/jionchu/Study/blob/master/Deep%20Learning/images/supervised%20learning%20ex.png)
- __Unsupervised learning__: un-labeled data를 가지고 학습 ex) Google news grouping, Word clustering

## 2. Supervised learning
- Most common problem type in ML
- Image labeling: learning from tagged images
- Email spam filter: learning from labeled (spam or ham) email
- Predicting exam score: learning from previous exam score and time spent
- training data set이 반드시 필요하다.
- ex) 알파고도 training set을 학습시킨 것이다.

## 3. Types of supervised learning
- __regression__: 범위가 넓을 때 (ex 시험 점수 예측 1~100)
- __binary classification__: 두 개 중 하나 고를 때 (ex P/N)
- __multi-label classification__: 여러 개 중 하나 고를 때 (ex A, B, C, D, F)
![training set](https://github.com/jionchu/Study/blob/master/Deep%20Learning/images/training%20sets.png)
