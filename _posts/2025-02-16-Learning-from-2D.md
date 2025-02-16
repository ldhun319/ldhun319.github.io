---
layout: post
title: '[CVPR 2023] Learning from 2D: Contrastive Pixel-to-Point Knowledge Transfer for 3D Pretraining'
date: 2025-02-16 15:00 +0800
last_modified_at: 2025-02-16 15:00 +0800
tags: [jekyll theme, jekyll, tutorial]
toc:  true
---

## Introduction
- 대부분의 3D 신경망은 처음부터 훈련됨. 3D 데이터 세트를 수집하기 위해 많은 노력이 이루어지고 있지만, 값비싼 라벨링 비용으로 대규모 데이터 세트를 구축하기가 어렵고, 3D에서 감독된 사전 학습이 어려움.
- 최근에는 레이블이 지정되지 않은 대규모 데이터를 사용하여 신경망을 사전 훈련함으로써 NLP 및 2D 비전에서 자가 감독 사전 훈련이 성공적인 것으로 입증됨. 이 방향은 사람이 주석을 달 필요 없이 쉽게 확장할 수 있기 때문에 유망함.
- 이전 연구는 2D 자기지도 학습 패러다임을 3D로 성공적으로 가져왔지만 2D CNN에서 추출한 의미 단서와 같은 2D 의미 정보를 무시함.
- 엄청난 양의 2D 레이블이 지정된 데이터 세트는 3D 데이터 세트보다 훨씬 더 크고 다양하며, 2D 네트워크 아키텍처는 지난 몇 년 동안 널리 연구되고 잘 개발됨.
- 따라서 우리는 잘 훈련된 2D 네트워크에 대한 지식이 가치 있고 유익하다고 믿음. 이를 통해 추가 레이블 데이터 없이 좋은 초기 가중치를 학습할 수 있는 3D 네트워크 신호를 제공할 수 있음.
- 본 논문의 아이디어는 이전의 2D-3D 융합 연구들과도 관련이 있으며, 이는 2D 네트워크에서 추출된 기능이 3D 대응물을 보완할 수 있음을 시사함.
- 이 발견은 사전 훈련으로 2D에서 학습하면 3D 데이터만으로는 쉽게 학습할 수 없는 추가 정보를 가져올 수 있다는 동기를 부여.
- 이 논문의 핵심 문제 : 데이터 속성과 이종 네트워크 구조의 차이를 고려할 때 추가 레이블 데이터를 사용하지 않고 사전 훈련된 2D 네트워크를 3D로 효과적으로 전송하는 방법은 무엇입니까? ⇒ 그 해답은 Knowledge distillation이라고 밝힘.
-이를 위해 2D 신경망을 교사로 보고 3D 모델을 학생으로 취급.
그러나 2D 이미지 및 깊이 맵의 경우와 달리 2D 이미지 데이터와 3D 포인트 클라우드는 자연스럽게 정렬되지 않음.
이런 다른 네트워크 구조로 인해 2D와 3D 간에 중간 네트워크 표현을 정렬하는 것이 어려워, 이에 최적화된 프레임워크 PPKT(novel contrastive pixel-to point knowledge transfer) 제안


## Related work

###  3D Deep Neural Networks
- 다양한 방식 소개(point, voxel, pillar based)

### Self-supervised Representation Learning
- BERT & NLP의 사전 훈련으로 인해 자기 지도 표현 학습이 컴퓨터 비전에 관심을 갖게 됨. 
- 자기 지도 표현 학습을 3D 비전에서는 단일하고 깨끗한 3D 개체를 입력으로 사용하므로 복잡한 실제 3D 데이터에는 적용할 수 없습니다. 
- 이를 해결하고자 Pointcontrast는 대조 학습으로 해결하고자 시도

### Knowledge Distillation and Cross-modal Pretraining
- 네트워크의 원래 성능을 높이기 위한 도구로 KD를 활용
- 여기서의 증류 프로세스는 쌍을 이루지만 레이블이 지정되지 않은 데이터에 의존
- 교차 모달 지식 증류에도 불구하고 많은 연구에서는 ImageNet으로 사전 훈련된 CNN을 활용하여 다양한 접근 방식을 사용하여 대상 도메인의 신경망을 초기화

## LEARNING FROM 2D FOR 3D PRETRAINING

### Motivation: 2D Pretraining on 3D Tasks is Effective
- 3D 작업을 위한 심층 신경망 사전 훈련의 동기와 기회를 보여주는 간단한 파일럿 연구를 수행
- ScanNet에서 3D 의미론적 분할을 수행하도록 2D 의미론적 분할 네트워크를 훈련


(표 이미지 삽입)

- 적절한 3D 사전 훈련을 사용하면 3D 신경망이 더 잘 수행되며 2D 사전 훈련 지식은 3D 장면 이해 작업에 도움이 될 수 있음.

### Contrastive Pixel-to-point Knowledge Transfer
- 2D 신경망이 대규모 2D 이미지 데이터세트(예: ImageNet)에 의해 사전 훈련되었다고 가정
- 레이블이 없는 데이터세트를 사용하여 3D 네트워크에 대한 일반적인 초기 가중치를 학습하는 것을 목표
- 파인 튜닝 단계에서 다양한 3D 다운스트림 작업을 위해 지도학습 방식으로 3D 네트워크를 파인 튜닝
설명을 위해 방법을 (1) the pixel-to-point design, (2) the upsampling feature projection layer, (3) point-pixel NCE loss 이렇게 세 부분으로 나눔.

(모델 사진)

- PPKT 사전 학습을 위해 저비용 RGB-D 카메라로 수집된 라벨 없는 RGB-D 이미지 데이터셋을 사용하며, 이때 RGB-D 데이터셋은 정렬된 RGB 이미지와 깊이 맵으로 구성
- 카메라 내부 파라미터를 이용해 역투영 함수를 통해 2D-3D 매핑을 수행하여 포인트 클라우드를 생성
- 역투영 함수는 입력에 따라 RGB 값을 가진 포인트 클라우드 또는 픽셀 특징을 가진 포인트 클라우드를 생성
- 2D 신경망은 이미지를 입력으로 받아 특징 맵을 출력하고, 3D 신경망은 포인트 클라우드를 입력으로 받아 각 포인트의 특징을 출력

###  Knowledge transfer from pixels to points

#### 기존 방법들의 문제
- 단순 KD는 2D-3D간에는 작동이 어려움.
- 3D 데이터셋이 많은 포인트를 가지지만 인스턴스 수가 적어 글로벌 표현 학습이 어려움을 겪음
- 글로벌 풀링 연산은 공간 정보 손실을 야기하며, 일반적인 3D 인코더-디코더 네트워크 백본에서는 인코더만 사전 학습되고 디코더는 무시
- 실내 환경의 RGB-D 프레임은 유사한 글로벌 컨텍스트를 포함하여 특징 공간에서 변별력이 떨어짐
#### 제안 방법
- 2D 이미지의 픽셀 수준과 3D 포인트 클라우드의 포인트 수준 간의 지식 전이를 제안하며, 네트워크로 인코딩된 2D 및 3D 특징 표현을 정렬
- RGB 이미지 x와 깊이 맵 d가 주어졌을 때, 3D 특징 표현 z_3D와 2D-3D 매핑된 2D 표현 z_2D를 정의하고 동일한 임베딩 공간으로 매핑

(수식 추가)

### Upsampling feature projection layer (UPL)

(그림 추가)
- ImageNet 분류 네트워크의 특징 맵의 낮은 공간 해상도로 인해 대조적인 픽셀 간 지식 전달이 어려움.
- 따라서 이 문제를 해결하기 위해 2D용 업샘플링 기능 투영 레이어 g를 사용
- 구체적으로, 2D CNN의 마지막 레이어의 특징 맵에 1×1 컨볼루션과 이중선형 업샘플링을 적용(마치 디코더 헤드 처럼)
- 간단하면서도 다양한 2D 네트워크 아키텍처의 공간 해상도 차이와 채널 크기를 유연하게 처리할 수 있어 효과적.

### Point-pixel NCE loss
- Point-pixel NCE 손실(PPNCE)을 사용하여 대응되는 픽셀과 포인트 표현 간의 상대적 거리를 최소화하는 학습 목표를 설정
- PP-NCE 손실은 동일한 3D 좌표를 공유하는 픽셀과 포인트를 끌어당기고, 다른 2D 특징으로부터 3D 특징을 분리하여 특징 공간을 형성
- 메모리 문제를 고려하여 고정된 수의 포인트와 픽셀을 샘플링하여 손실을 계산하며, 메모리 뱅크는 사용하지 않음.
(수식 추가)

## EXPERIMENT

- 평가는 세 단계로 나뉨 
1) 사전 훈련된 2D 신경망이 주어지면 레이블이 지정되지 않은 RGB-D 데이터 세트를 사용하여 PPKT로 3D 신경망을 사전 훈련함.
2) 3D 의미론적 분할 및 3D 객체 감지를 포함하여 감독 방식으로 대상 다운스트림 작업에 대한 모델을 파인 튜닝
3) 테스트 데이터에 대한 파인 튜닝 성능을 평가

#### Experimental Setup of 3D Pretraining
- 사전 훈련된 2D 신경망 : ResNet [27]을 2D 백본으로 선택
- 3D 네트워크 : Sparse Residual 3D U-Net 34(SR-UNet34) 를 채택
- VoteNet [80] 모듈을 부착하여 3D 객체 감지도 수행
#### 데이터셋
- ScanNet 데이터세트의 RGB-D 이미지를 사용
- 707개의 개별 공간에 대한 1513개의 실내 스캔이 포함
- 순차적 RGB-D 데이터에서 25프레임마다 하위 샘플링을 수행하여 총 약 100,000프레임을 생성
#### Baseline

<table>
  <thead>
    <tr>
      <th>Method</th>
      <th>Objective</th>
      <th>Pretrained 3D network</th>
      <th>Loss function</th>
    </tr>
  </thead>
  <tfoot>
    <tr>
      <td>PPKT(ours)</td>
      <td>2D->3D</td>
      <td>encoder-decoder</td>
      <td>Local contrastive</td>
    </tr>
  </tfoot>
  <tbody>
    <tr>
      <td>Global KD</td>
      <td>2D->3D</td>
      <td>encoder</td>
      <td>Global KL-div</td>
    </tr>
    <tr>
      <td>CRD[69]</td>
      <td>2D->3D</td>
      <td>encoder</td>
      <td>Global contrastive</td>
    <tr>
      <td>Gupta et al.[26]</td>
      <td>2D->3D</td>
      <td>encoder</td>
      <td>Global L2</td>
    </tr>
    <tr>
      <td>PointContrast[12]</td>
      <td>3D<->3D</td>
      <td>encoder-decoder</td>
      <td>Local contrastive</td>
    </tr>
  </tbody>
</table>

#### Training details of PPKT
- upsampling feature projection layer : 2D ResNet50 layer4 output
- 3D feature projection layer: the last layer feature of SR-Unet
- projected feature dimension : 128
- temperature of NCE loss is 0.04
- 배치 크기가 24인 하나의 V100 GPU를 사용, 60k iterations으로 3D 네트워크를 사전 훈련


### Fine-tune on S3DIS Semantic Segmentation

(표, 또는 그림)

- 2D 베이스라인(Global KD, CRD, Gupta et al.)을 통한 전역 학습 성능은 처음부터 훈련하는 것과 유사하며, 이는 이러한 기준선의 사전 훈련 효과가 제한적임. 
- 인코더-디코더를 사전 훈련하는 것이 도움이 됨. 
- 이러한 기준선과 방법은 모두 동일한 ImageNet 사전 훈련된 2D 교사 네트워크를 사용하므로 2D 지식을 효과적으로 전달하는 데 픽셀 간 설계가 중요

### Fine-tune on SUN RGB-D Object Detection

(표, 또는 그림)

### Ablation study

