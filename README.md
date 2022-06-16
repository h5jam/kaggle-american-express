# American Express - Default Prediction
Predict if a customer will default in the future

https://www.kaggle.com/competitions/amex-default-prediction

## Overview
Credit default prediction은 소비자 대출 사업의 위험 관리에 핵심적이다. 이는 대출 결정을 최적화할 수 있게 해주며, 더 나은 고객 경험과 건전한 비즈니스 경제로 이어진다. 따라서, ML 기술을 적용하여 월별 고객 프로필을 기반으로 credit default (미래에 신용 카드 잔액을 갚지 않을 확률)을 예측하는 모델을 구축함으로써 위험 관리를 지원한다. 산업 규모 dataset을 활용하며, Training, validation, testing datasets 에는 시계열 행동 데이터 및 익명화된 고객 프로파일 정보가 포함된다.


## Data Description
Dataset에는 각 명세서 날짜에 각 고객에 대해 집계된 profile feature가 포함되어 있다. feature는 익명화, 정규화되며 다음과 같은 범주로 분류된다.

- D_* = Delinquency variables
- S_* = Spend variables
- P_* = Payment variables
- B_* = Balance variables
- R_* = Risk variables


## EDA

- **Down Sampling**
    
    데이터의 분포가 높은 값을 낮은 값으로 맞춰주는 Down Sampling을 통해 Imbalanced data 문제를 해결하여 유의미한 데이터만을 남긴다.
    
- **Correlation Matrix**
    
    Dataset의 feature가 매우 많으므로, Correlation Matrix를 통해 feature간 상관관계를 살펴봄으로써 redundancies 를 찾는다. 상관관계가 높은 경우 두 가지 feature 중 하나를 골라 dimension을 축소한다.
    
- **PCA (Principal component analysis)**
    
    데이터의 특성과 다양성, 분산을 가장 보전하는 성분을 추출하여 dimension을 축소한다.


## Models

- **LightGBM(Gradiant Boosting Macghine)**
    
    LGBM은가볍고(light), 틀린 부분에 가중치를 두는 학습 방법(GBM)에 기반한다. Tree 구조가 수평적으로 확장하는 Level-wise 방식을 이용하는 기존의 tree based 방법과 달리, Tree가 수직적으로 확장하는 Leaf-wise 알고리즘이다.
    
    일반적인 GBM 계열의 트리 분할 방식은 균형을 맞추기 위한 시간이 소모된다는 단점을 가지는 반면, LightGBM의 경우 속도가 빠르기 때문에 사이즈의 데이터를 다룰 수 있고 실행시킬 때 적은 메모리를 차지한다.


## Training Strategy

- **Hyperparameter parameter tuning**

  Optuna를 사용하여 최적의 parameter를 찾는다. Optuna는 hyperparameter ****최적화 태스크를 도와주는 프레임워크로, parameter의 범위를 지정해주거나, parameter가 될 수 있는 목록을 설정하여 매 Trial 마다 parameter를 변경하면서, 최적의 parameter를 찾는다.

- ****Stratified Cross validation****

  K-Fold는 사전에 지정된 k 값 만큼 학습 세트와 검증 세트를 나눠서 k번의 학습과 평가를 반복적으로 진행한다. 이때, 데이터가 편항되어 있을 경우 단순 K-Fold cross validation을 사용하면 성능 평가가 잘 되지 않을 수 있으므로, target에 속성값의 개수를 동일하게 하게 나누어 데이터가 한 곳에 몰림을 방지하는 stratified K-Fold cross validation을 사용한다.

- **Evaluation Metric**
    
    $M = 0.5 * (G + D)$
    
    두 개의 순위 측정 metric의 평균을 사용
    
    G: Normalized Gini Coefficient
    
    D: Default rate captured at 4% 
