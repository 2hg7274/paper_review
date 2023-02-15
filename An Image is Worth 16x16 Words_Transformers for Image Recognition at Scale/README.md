# An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale  

## Abstract  
Transformer 구조는 자연어 처리 작업에서는 표준이 되었지만 Computer Vision에 대한 적용은 여전히 제한적이다. Vision에서 Attention은 Convolutional Network와 함께 적용되거나 전체 구조를 제자리에 유지하면서 Convolution Network의 특정 요소를 대체하는데 사용된다.  
CNN에 대한 이러한 의존이 필요하지 않으며 Sequences of Image Patches에 직접 적용된 순수한 변환기가 이미지 분류 작업에서 매우 잘 수행할 수 있음을 보여준다.  
ViT(Vision Transformer)는 대용량 데이터에 대해 Pre-Training되어 여러 개의 중소형 이미지 인식 벤치 마크(ImageNet, CIFAR-100, VTAB 등)로 전송될 때 훈련하는데 훨씬 더 적은 계산 리소스를 필요로 하면서 Convolution Network를 구축한다.  

## Vistion Trnaformer (ViT)  
<img width="400" alt="image" src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F7yg4q%2Fbtq8A9nxSKk%2F7Mcrjwtm2qxkkaEDgLcvk1%2Fimg.png">  

이미지를 텍스트 sequence처럼 사용하는 구조를 가지고 있다. 먼저, 이미지를 고정된 크기의 patch로 나눠준다. 각각의 patch를 Linearly Embedding하고 Position Embedding을 하여, Transformer 인코더에 Input 값으로 넣는다.  

## Linear Projection of Flattend Patches  
Transformer의 Input값은 1차원 시퀀스이다. 따라서, 고정된 크기의 patch로 나눠준 이미지를 1차원 시퀀스로 Flattend한다.  
(C, H, W) 크기의 이미지를 크기가 (P, P)인 patch N개로 자른 후, N개의 1-D 벡터($P^2 \cdot c$ 차원)로 Flattend한다.  

($N = HW / P^{2}$ 로 계산되며, P는 하이퍼파라미터이다.)  

이렇게 1차원으로 바꾼 이미지를 Transformer에 사용할 수 있는 D차원의 벡터로 바꿔준다. 이걸로 Transformer Input 형식에 맞춰서 이미지를 바꾼다.  

Hybrid Architecture로 CNN의 feature map을 Input값으로 사용할 수도 있다.  

> Inductive bias  

CNN(Locality 가정)이나 RNN(Sequentiality 가정)경우, Global한 영역의 처리는 어렵다.  
ViT는 일반적인 CNN과 다르게 공간에 대한 Inductive bias가 없다. 그러므로 ViT는 더 많은 데이터를 통해 원초적인 관계를 Robust하게 학습시켜야 한다.  
- ViT는 MLP Layer에서만 Local 및 Translation Equivariance 하다.  
- Self-Attention 매커니즘은 Global  

> Hybrid Architecture  

Image Patch의 대안으로, CNN의 feature map의 sequence를 사용할 수 있다.  
- CNN의 feature는 Spatial size가 1 X 1이 될 수 있음.  
- CNN으로 feature 추출 -> Flatten -> Embedding Projection 적용  


## ViT 사용에 유의할 점  
ViT도 Transformer답게 많은 데이터로 Pre-Training하지 않으면, 좋은 성능을 기대하기 어렵다. 
Fine-Tuning 시에는 Pre-Training head를 제거하고 0으로 초기화된 D x K 차원의 feedforward layer를 연결하여 사용하면 된다.  
여기서 K는 우리가 찾고자 하는 Task의 Class 개수이다.   

참고 사이트: [https://kmhana.tistory.com/27](https://kmhana.tistory.com/27)
