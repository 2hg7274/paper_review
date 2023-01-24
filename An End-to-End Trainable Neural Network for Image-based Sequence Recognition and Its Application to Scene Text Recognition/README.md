# An End-to-End Trainable Neural Network for Image-based Sequence Recognition and Its Application to Scene Text Recognition  

## Abstract
Image-based sequence recognition은 computer vision 분야에서 오랜 연구 주제였다.  
본 논문에서는 이미지 기반 시퀀스 인식에서 가장 중요하고 어려운 작업 중 하나인 scene text recognition(장면 텍스트 인식)문제를 조사한다.  
Feature Extraction, Sequence Modeling 및 전사를 통합 프레임워크로 통합하는 새로운 신경망 아키텍처가 제안된다.  

Scene Text Recognition을 위한 이전 시스템과 비교하여 제안된 아키텍처는 다음과 같은 네 가지 고유한 속성을 가진다.  
1. 구성 요소가 별도로 훈련되고 조정되는 대부분의 기존 알고리즘과 달리 End-to-End 훈련이 가능하다.  

2. 문자 분할이나 수평 스케일 정규화를 포함하지 않고 임의 길이의 시퀀스를 자연스럽게 처리한다.  

3. 미리 정의된 사전에 국한되지 않고 lexicon-free 및 lexicon-based 장면 텍스트 인식 작업 모두에서 놀라운 성능을 달성한다.  

4. 실제 애플리케이션 시나리오에 더 실용적일 뿐만 아니라 효율적인 작은 모델을 생성한다.  

## The Proposed Networkd Architecture  
<img width="400" alt="image" src="https://user-images.githubusercontent.com/94213374/214222679-a3bc460f-1e0e-4f43-ae2c-1f002c8d58e0.png">  

CRNN 네트워크 구조는 convolutional layer, recurrent layer, transcription layer의 세 가지로 구성되어 있다.  

CRNN의 하단에서 convolutional layer는 각 입력 이미지에서 자동으로 feature sequence를 추출한다.  
Convolutional Network 위에는 convolitional layer에서 출력되는 feature sequence의 각 프레임에 대한 예측을 위해 Reurrent Network가 구축된다.  
CRNN의 상단에 있는 transcription layer는 반복 레이어의 프레임별 예측을 label sequence로 변환하기 위해 채택된다.  
CRNN은 여러 종류의 네트워크 아키텍처(CNN 및 RNN)로 구성되어 있지만 하나의 손실 함수로 공동으로 학습할 수 있다.  

## Feature Sequence Extraction  
CRNN 모델에서 convolutional layer의 구성 요소는 Standard CNN 모델에서 convolutional layer와 max-pooling layer를 가져와서 구성된다. (fully-connected layer는 제거)  
이러한 구성 요소는 입력 이미지에서 sequential feature representation을 추출하는 데 사용된다.  

네트워크에 입력되기 전에 모든 이미지는 동일한 높이로 조정되어야 한다. 그런 다음 recurrent layer의 입력인 convolutional layer의 구성 요소에 의해 생성된 feature map에서 일련의 feature vectors가 추출된다.  
구체적으로 feature sequence의 각 feature vector는 feature map에서 열별로 왼쪽에서 오른쪽으로 생성된다. 즉, i번째 특징 벡터는 모든 map의 i번째 열을 연결한 것이다. 설정에서 각 열의 너비는 단일 픽셀로 고정된다.  
convolutional, max-pooling layer 및 elementwise activation function은 로컬 영역에서 작동하므로 불변이다. 
<img width="163" alt="image" src="https://user-images.githubusercontent.com/94213374/214226038-4be4a61f-4113-4ae2-8c57-47dc50120cc0.png">  

## Sequence Labeling  
Deep bidirectional recurrent neural network은 순환 계층으로 convolutional layer의 맨 위에 구축된다. Recurrent layer는 feature sequence $x = \left\{x_{1}, \cdots, x_{T} \right\}$ 에서 각 프레임 $x_{T}$ 에 대한 레이블 분포 $y_{t}$ 를 예측한다.  

Recurrent layer의 장점은 세 가지이다.  
1. RNN은 시퀀스 내에 context 정보를 캡쳐하는 강력한 기능을 가지고 있다. 이미지 기반 시퀀스 인식에 상황별 단서를 사용하는 것이 각 기호를 독립적으로 처리하는 것보다 더 안정적이고 유용하다. 장면 텍스트 인식을 예로 들면 와이드 문자는 완전히 설명하기 위해 여러 연속 프레임이 필요할 수 있다. 게다가 일부 모호한 문자는 문맥을 관찰할 때 구별하기가 더 쉽다.  

2. RNN은 convolutional layer로 역전파할 수 있으므로 통합 네트워크에서 recurrent layer와 convolutional layer를 함께 훈련할 수 있다.  

3. RNN은 시작부터 끝까지 순회하는 임의 길이의 sequence에서 작동할 수 있다.  

Convolutional layer와 recurrent layer 사이의 다리 역할을 하는 "Map-to-Sequence"라는 사용자 지정 network layer를 만든다.  


## Transcription  
Transcription은 RNN이 만든 프레임별 예측을 레이블 시퀀스로 변환하는 프로세스이다. 수학적으로 trascription은 프레임당 예측에 따라 가장 높은 확률로 레이블 시퀀스를 찾는 것이다. 실제로는 두 가지 transcription 방식 즉, lexicon-free 와 lexicon-based trascription이 있다.  
Lexicon은 예측이 제한되는 레이블 시퀀스 집합이다. 
Lexicon-free 모드에서는 lexicon 없이 예측이 이루어진다.  
Lexicon-based 모드에서는 확률이 가장 높은 레이블 시퀀스를 선택하여 예측을 수행한다.  


## Probability of label sequence  
Graves 등이 제안한 CTC(Connectionist Teporal Classification) 계층에서 정의도니 조건부 확률을 채택한다.  

## Model  
<img width="290" alt="image" src="https://user-images.githubusercontent.com/94213374/214229808-1958c1fa-d63d-4921-94e6-89aea9e10355.png">  

