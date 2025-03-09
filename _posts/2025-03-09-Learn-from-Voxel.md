---
layout: post
title: '[IEEE Transactions on Intelligent Vehicles] Learn From Voxels: Knowledge Distillation for Pillar-Based 3D Object Detection With LiDAR Point Clouds in Autonomous Driving'
date: 2025-03-02 15:00 +0800
last_modified_at: 2025-03-02 15:00 +0800
tags: [jekyll theme, jekyll, tutorial]
toc:  true
---

## Abstract
- 3D 객체 감지에서 높은 정확성과 빠른 추론 사이의 모순은 중요한 문제이며, 이는 voxel 기반 네트워크와 pillar 기반 네트워크에서 특히 두드러짐. 
- voxel 기반 네트워크는 높은 정확도를 달성할 수 있지만 voxel 기반 네트워크의 3D 희소 컨볼루션 백본은 실시간 추론 및 모델 배포를 차단함. 
- pillar 기반 네트워크는 배포하기 쉽고 실시간 추론을 달성할 수 있지만 voxel 기반 네트워크만큼 정확하게 작동할 수는 없음. 
- 이 두 가지 유형의 네트워크 간의 정확도 차이를 줄이고 pillar 기반 네트워크의 추론 속도를 유지하기 위해 본 논문에서는 효과적인 voxel-to-pillar 지식 증류 프레임워크인 Learn from Voxel Knowledge Distillation(LVKD)을 제안.
- voxel 기반 교사 네트워크의 풍부한 지식을 학생 네트워크로 전송하기 위해  Sparse Convolution to Pillar Knowledge Distillation (SCP KD)를 설계. 
- 또한 교사와 학생 네트워크 간의 표현 차이를 완화하고 증류 성능을 향상시키기 위해 pillar 기반 네트워크가 각 voxel의 점유를 예측하도록 장려하는 플러그 앤 플레이 작업인 Voxel Occupancy Prediction 모듈을 제안

## Introduction
- LiDAR 포인트 클라우드는 풍부한 기하학적 정보와 형상 정보를 제공하지만, 희박하고 불규칙한 특성으로 인해 CNN을 직접 적용하기 어려움.
- 이를 해결하기 위해 포인트 기반, 그리드 기반, 투영 기반 등의 다양한 표현 방법이 제안
- 그리드 기반 네트워크는 포인트 클라우드를 voxel화하여 2D 또는 3D CNN을 활용할 수 있으며, pillar 기반과 voxel 기반 두 가지 유형으로 구분됨..
- pillar 기반 네트워크는 구현이 용이하지만 정확도가 낮고, voxel 기반 네트워크는 정확도가 높지만 구현이 복잡함.
- 따라서 계산 부담을 늘리지 않으면서도 pillar 기반 네트워크의 정확도를 향상시키는 방법이 필요함.
- pillar 기반 감지 네트워크와 voxel 기반 감지 네트워크 간의 정확도 격차를 줄이기 위해 지식 증류(KD)를 활용하는 방법이 제안됨.
- 그러나 교사 네트워크와 학생 네트워크 간의 표현 불일치가 KD 작업에서 중요한 문제가 되며, 이는 voxel-pillar 증류에서 특히 두드러짐.
- 표현 불균형의 부정적인 영향을 줄이고자 LVKD(Voxel Knowledge Distillation에서 학습)라는 새로운 프레임워크를 제안
- LVKD 프레임워크에서는 SCP KD(Sparse Convolution to Pillar Knowledge Distillation)와 VOP(Voxel Occupancy Prediction)라는 두 가지 새로운 모듈을 개발
- SCP KD는 전경 영역의 풍부한 구조 및 기하학 정보를 전송하고 배경 영역의 중복 정보를 차단합니다.
- VOP는 pillar 기반 네트워크가 각 voxel의 점유를 예측하도록 하여 구조 및 공간 정보를 재구성할 수 있게 합니다.
- 이 프레임워크를 통해 pillar 기반 네트워크가 voxel 기반 네트워크에서 구조 및 기하학적 정보에 대한 지식을 성공적으로 학습할 수 있음.
- 추가 컴퓨팅 비용 없이 pillar 기반 3D 포인트 클라우드 객체 감지 모델의 정확도를 향상시킬 수 있음.


## Related work

###  LiDAR-Based 3D Object Detection
- 3D 객체 탐지 기술은 point-based 방법과 grid-based 방법으로 구분됨.
- VoxelNet은 초기의 대표적인 grid-based 방법으로, Voxel Feature Encoding (VFE) 모듈과 3D 컨볼루션을 사용했지만 계산 자원 낭비가 큰 문제가 있음.
- SECOND와 PointPillars 등의 후속 연구들은 3D 스파스 컨볼루션, pillar-based 접근법 등을 도입하여 계산 속도와 정확도를 개선함.
- CenterPoint는 anchor-free 방식으로 탐지 결과를 예측하는 방법을 제안했고, 이는 네트워크 설계에 큰 장점을 제공함.
- 이 논문에서는 voxel-based CenterPoint를 teacher 네트워크로, pillar-based CenterPoint를 student 네트워크로 사용하여 지식 증류 (Knowledge Distillation) 기법을 적용

### wledge Distillation
- KD는 큰 규모의 교사 모델(teacher model)로부터 경량이면서 빠르고 배포에 적합한 학생 네트워크(student network)로 지식을 전달하는 모델 압축 방법입니다.
- 2D 객체 탐지 분야에서는 FitNet, FG, GID, label KD 등의 방법이 제안
- 3D 객체 탐지 분야에서는 BEVDistill, UniDistill, ItKD, SparseKD 등의 방법이 제안
- 이전 연구들은 주로 교사와 학생 네트워크가 동일한 아키텍처를 가지고 있거나, 너비, 깊이, 해상도 등이 다른 경우를 다루었습니다.
- 본 논문에서는 voxel 기반 교사 네트워크로부터 pillar 기반 학생 네트워크로 지식을 전달하는 방법을 제안하고자 합니다. 이는 표현력 차이와 도메인 차이가 존재하는 경우에 적용할 수 있습니다. 

## METHODOLOGY

(그림 삽입)

### Preliminaries
- 교사와 학생 네트워크로 사용되는 네트워크 : , CP-Pillar(학생)와 CP-Voxel(교사)
- Centerpoint Backbone, neck , detection head 으로 구성
backbone
- Teacher 네트워크 CP-Voxel에서 입력 포인트 클라우드는 먼저 정의된 voxel 크기(vx, vy, vz)에 따라 3D voxel로 나뉨. 그런 다음 백본 ​​네트워크는 여러 3D 희소 컨볼루션[3]을 활용하여 voxel의 특징을 추출
- 학생 네트워크 CP-Pillar에서 입력 포인트 클라우드는 Z축 크기가 공간 규모와 동일한 pillar으로 분할된 후 분할된 포인트 클라우드가 다음 모듈의 pillar 특징으로 직접 인코딩
Neck
- 인코딩된 voxel 또는 pillar 특징은 Z축을 따라 압축됩니다. 넥 네트워크는 BEV 표현에서 특징 맵의 다운샘플링 및 업샘플링을 달성하기 위해 2D 컨볼루션 및 디컨볼루션으로 구성된 FPN 네트워크입니다. 
- 목에 있는 교사와 학생 네트워크의 입력 특징은 BEV 표현에서 크기와 해상도가 다릅니다. 
- 다양한 다운샘플링 및 업샘플링 후에 교사와 학생의 출력 특징은 BEV 표현에서 크기가 동일합니다.
Detection head
- 목 네트워크의 특징 맵은 히트맵, 물체 크기, 하위 voxel 위치 개선 및 장면 내 물체의 회전을 예측하기 위해 감지 헤드로 처리됩니다. 해당 정보로 탐지 결과가 생성됩니다.

### Sparse Convolution to Pillar Knowledge Distillation
- 특징 표현에서 학생 네트워크의 기능을 향상시키기 위한 간단하고 명확한 접근 방식은 교사 네트워크의 압축된 voxel 특징을 사용하여 BEV 관점에서 학생 네트워크의 주요 특징을 감독하는 것
- 그러나 교사와 학생 네트워크의 출력 간 기능 맵의 크기와 기능 차원에는 불일치가 있음.
- 또한 희박하고 대규모의 3D 장면에는 배경 영역이 많고 중복된 정보는 KD의 효율성에 큰 영향을 미칠 수 있음.
- 우리는 GT(Ground Truth) 영역과 신뢰도가 높은 교사 예측을 활용하여 증류를 안내하는 SCP KD를 제안. 
- 첫째, 장면의 포인트 클라우드와 GT 세트가 주어지면 사전 훈련된 Teacher 검출기는 예측 및 신뢰도 점수를 생성할 수 있음.
- 임계값 τ로 예측을 필터링한 후 신뢰도가 높은 교사 예측 세트를 얻을 수 있음.

- 여기서 N'은 필터링 후 예측 상자의 수입니다. yht를 y와 결합하고 다음과 같이 새로운 전경 세트 y'를 생성함.

- 교사와 학생 특성이 모두 BEV 평면에 투영되지만 교사 네트워크와 학생 네트워크 간의 특성 맵 해상도, 특성 차원 및 수용 필드의 차이는 여전히 증류의 추가 적용을 방해함.
- 또한, 교사와 학생 네트워크 간의 표현 차이로 인해 학생 네트워크의 성능이 저하됨.
- 교사와 학생 네트워크의 기능에 대한 수용 필드, 채널 수 및 해상도를 정렬하기 위해 Pillar-Feature-Adapt 블록 ψ를 도입(배치 정규화 [35] 및 ReLU [36] 블록을 사용하는 1계층 컨볼루션 네트워크)

- Inference에는 제거

- 대규모 배경 영역의 정보 효과를 필터링하기 위해 SCP KD에서는 전경 개체의 특징을 정렬합니다. 
- y'의 각 경계 상자에 대해 BEV 기능 맵에 투영된 경계 상자의 그리드 점을 선택. 
- 그런 다음 Fˆb s와 F b t에서 선택된 그리드 점의 특징 간의 차이를 사용하여 SCP 증류 손실을  계산

- 여기서 ф는 관심 영역(ROI) 정렬[37]으로 전경 상자에서 증류를 위한 특징을 생성합니다. y'의 전경 상자에서 - - 여러 그리드 점을 선택하고 이중선형 보간을 통해 이러한 그리드 점을 사용하여 증류를 위한 특징을 생성

## Voxel Occupancy Prediction
- Teacher 네트워크는 3D Sparse Convolution을 사용하여 객체의 공간 정보를 더 효과적으로 추출할 수 있으며 특히 구조 및 기하학적 정보를 최대한 활용할 수 있음.
- 구조 및 기하학적 정보 활용 부족은 pillar 기반 네트워크의 성능이 만족스럽지 못한 주요 요인
- SCP KD는 교사 네트워크에서 지식을 전달하여 상향식 스타일로 공간 정보를 활용
- 따라서 다중 카메라 3D 객체 감지의 점유 네트워크에 영감을 얻어 구조 및 기하학적 정보 활용을 위한 pillar 기반 네트워크의 기능을 향상시키기 위해 VOP라는 새로운 감독 작업을 제안

- 포인트 클라우드의 공간정보를 재구성하여 공간정보와 구조정보를 활용하는 Top-down 방식
- 여기서 "GT Label"은 voxel 점유에 대한 Ground Truth이고 "Prediction"은 VOP 모듈의 voxel 점유에 대한 예측
- pillar을 Z축을 따라 D개의 voxel로 나눔.
- 여기서 D는 pillar당 들어 올려진 voxel의 수
- 출력 pillar의 경우 Neck에서 Fh ∈ R C×H×W를 특징으로 함. 
- 여기서 C는 Fh의 특징 채널 수입니다. 
- H와 W는 Fh에 있는 특징 맵의 크기입니다. 
- pillar 특징을 Fˆ h ∈ RC ′×D×H×W로 변환
- 여기서 C ′ = C/D

- 입력 포인트 클라우드에 대해 pillar를 Z축 방향으로 D개의 voxel로 나누고, 이를 이용해 라벨 V = {Vi}, i ∈ D × H × W를 생성한다.
- 두 가지 종류의 라벨을 사용:
- 기본적인 voxel 속성으로, voxel이 점유되었는지 여부를 나타내는 이진 라벨
- GT 박스를 이용해 점유된 voxel을 전경과 배경으로 구분하는 3상태 one-hot 라벨
- 실험 결과, 모든 방식이 pillar 기반 네트워크에서 유의미한 성능 향상을 보였으며, one-hot 라벨이 이진 라벨보다 더 좋은 성능을 보였다.
- voxel의 점유 여부가 GT Gpred ∈ {0, 1}^(D×H×W×3)로 사용된다.

- 그 후, 우리는 예측 디코더를 사용하여 2D 컨볼루션 레이어로 구성된 voxel 점유를 생성하고 최종 적응 레이어는 각 voxel의 예측을 제공함.
- 소프트맥스 함수 이후 디코더의 출력은 Fpred ∈ R D×H×W×3 로 표시될 수 있음.
- 점유 예측을 분류 작업으로 전환하면 필연적으로 카테고리 불균형 문제에 직면하게 되는데, 이 경우 이는 많은 수의 빈 voxel의 영향에 반영됩니다. 
- 이 경우 VOP 모듈의 손실 함수로 focal loss을 활용

## EXPERIMENT

- NuScenes Dataset, KITTI Dataset
- 8 NVIDIA RTX 3090 GPUs, batch size is set to 4 per GPU
- nuScenes dataset for 20 epochs, the KITTI dataset for 80 epochs
- inferenced on 1 NVIDIA RTX 3090 GPU, and the batch size is set to 1.
- Network Architecture : voxel 기반 교사 네트워크와 pillar 기반 학생 네트워크의 backbone과 neck은 각각 SECOND [3] 및 PointPillars [2]의 설계를 따름. Detection head는 CenterPoint [27]의 아키텍처를 따름.
