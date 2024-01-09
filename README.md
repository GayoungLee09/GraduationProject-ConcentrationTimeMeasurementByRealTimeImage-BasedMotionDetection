# GraduationProject-ConcentrationTimeMeasurementByRealTimeImage-BasedMotionDetection
# 실시간 영상기만 모션감지를 통한 집중시간 측정 시스템
## 1. 프로젝트 개요

### 1) 프로젝트 설명

 프로젝트의 주제는 캠스터디를 이용하여 사용자의 움직임을 실시간으로 분석하여 알고리즘을 통해 사용자의 집중시간 및 비
집중시간을 측정하는 것입니다. 이를 통해 사용자에게 일/주/월 별 공부시간을 확인시키고 5분 이상 비집중 행동을 할 시
어플리케이션을 통해 사용자에게 알람을 가게 하는 성능을 제공합니다.

## 2. 사용한 기술 및 방법 설명
<img width="530" alt="스크린샷 2024-01-09 오후 2 33 14" src="https://github.com/GayoungLee09/GraduationProject-ConcentrationTimeMeasurementByRealTimeImage-BasedMotionDetection/assets/81952512/6b6263d4-d22c-46a4-bf87-da69f911d9c5">

(프로젝트 프로세스)

### 1) 데이터 수집 및 전처리
 1차적으로 유튜브 Study with me 영상과 Zoom 독서실 영상을 수집하였습니다. 이후 Transfer Learning을 위해 추가적으로 Zoom 독서실 영상과 별하(협업기업)에서 제공 받은 데이터를 수집하였습니다. 수집한 행동들은 총 6가지의 항목으로 집중 행동은 Reading, Writing, Using computer이며 비집중 행동은 Leaving seat, Sleeping, Using smartphone입니다.
 모델의 입력값으로 이미지 데이터를 받기 때문에 수집한 영상 데이터를 frame으로 변경하였습니다. frame으로 변경된 이미지는 영상 별로 폴더를 생성해 저장하고 각 영상 마다 영상 제목, 영상 시작 프레임과 영상 종료 프레임, 라벨을 부여하였습니다.

### 2) Classification
#### - MobileNet V2+3D(제안하는 방법)
우리는 본 프로젝트에서 MobileNet V2 + 2D 모델의 ConvNet을 2D에서 3D로 확대하는 방법을 제안하였습니다. 이는 ConvNet을 3D로 확대하여 영상의 시공간적 특징을 살린 모델링이 가능합니다. 2D구조에서는 영상을 프레임단위로 쪼개어 모델이 한개의 프레임씩 입력 값으로 받았다면, 제안하는 방법인 3D구조에서는 여러 프레임을 한 번에 입력 받아 영상단위의 Classification이 가능하도록 하였습니다.   본 프로젝트에서는 16 프레임을 한 번에 입력 받아 영상단위의 분석을 하였고, 이는 MobileNet V2 + 2D 모델과 비교하여 정확도 향상 및 계산시간 단축 등의 차별점이 있었습니다.
#### - ResNet
ResNet은 기본적으로 VGG-19 의 구조를 뼈대로 하며, convolution 층들을 추가해 깊게 만든 후shortcut들을 추가하는 것입니다. 예를 들어 VGG-19 구조에 합성곱 층을 추가하여 깊이를 늘리고, shortcut connection을 추가하면 ResNet의 가장 간단한 구조가 됩니다. 이 구조가 점점 발전하여 19 층에서 256 층까지 늘어나게 되었습니다. ResNet은 VGG-19보다 훨씬 더 깊지만, 완전히 연결된 레이어보다는 글로벌 평균 풀링을 사용하기 떄문에 모델크기가 상당히 작아지는 장점이 있습니다.
#### - VGGNet
사용한 VGGNet은 16개 또는 19개의 층으로 구성된 모델입니다. VGGNet의 핵심은 네트워크의 깊이를 깊게 만드는 것이 성능에 어떤 영향을 미치는지를 확인하고자 한 것입니다. VGG 연구팀은 깊이의 영향만을 최대한 확인하고자 컨볼루션 필터 커널의 사이즈를 가장 작은 3x3 으로 고정하였습니다.

### 3) Transfer Learning
 실제 온라인 캠스터디 데이터로 Classification 모델을 테스트 하였을 때 낮은 정확도가 나타났는데, 이는 처음 학습시킨 YouTube study with me 영상 데이터의 조명과 화질이 실제 데이터와 큰 차이가 있기 때문이라고 판단하였습니다. 모델의 정확도를 높이기 위해서 실제 데이터와 유사한 Zoom독서실 영상과 별하(협업기업)에서 제공받은 실제 데이터를 추가수집하였고, 이를 Transfer Learning에 사용하였습니다.
 유튜브에서 수집한 YouTube study with me 영상 데이터를 MobileNet V2, ResNet, VGGNet, ShuffleNet 모델을 사용해 학습시켜 Transfer Learning을 위한 사전학습 된 모델을 만들었습니다. 이를 Zoom 독서실 캠스터디 영상 데이터를 타겟으로 해 Transfer Learning을 한 뒤 모델을 검증하였습니다. 사전 학습된 모델을 확보한 후, 적용할 문제가 데이터 크기-유사성 그래프에서 어떤 부분에 속하는지 확인하였습니다.

<img width="50%" alt="스크린샷 2024-01-09 오후 2 34 40" src="https://github.com/GayoungLee09/GraduationProject-ConcentrationTimeMeasurementByRealTimeImage-BasedMotionDetection/assets/81952512/b90887d8-7679-45d2-a219-1c52e06ca43e">

(좌: 데이터크기-유사성그래프, 우: 각 상황에 따른 Fine-tuning 방법)

 우리는 데이터의 크기가 크고 유사도가 높은 데이터셋에 Transfer Learning을 적용하려고 하므로, 제2사분면에 해당하는 것을 확인할 수 있었습니다. 이는 Convolutional base의 일부분은 고정한 상태로, 나머지 계층과 Classifier를 새로 학습시키는 방법입니다.
   따라서 Transfer Learning을 위해 Train dataset으로 YouTube study with me 영상과 Zoom 독서실 영상을 사용했으며 별하(협업기업) 영상 데이터와 직접 촬영한 데이터, YouTube study with me, Zoom 독서실 영상을 Test dataset으로 사용하였습니다.
전이학습 후 정확도는 Mobilenet V2+3D 모델의 성능이 가장 높아 최종 모델로 선정하였습니다.

### 4) 모델 구현
 실시간으로 영상을 처리가 가능한지, 어플에 연동시킬 능력이 있는지를 보여주기 위해 Kivy 인터페이스를 사용하여 앱을 개발해 모델을 구현하였습니다.
 노트북이나 스마트폰으로 공부하는 모습을 촬영하며 캠스터디를 시작하면 어플이 행동을 예측해 집중시간과 비집중시간을 측정하고, 비집중 시간이 지속될 경우 알람이 울리게 됩니다. 공부가 끝나면 연동된 모델로 결과를 분석해 일간/주간/월간 공부시간을 확인할 수 있으며 행동별로 시간을 확인할 수 있는 그래프를 제공하였습니다.
 <img width="40%" alt="스크린샷 2024-01-09 오후 2 36 34" src="https://github.com/GayoungLee09/GraduationProject-ConcentrationTimeMeasurementByRealTimeImage-BasedMotionDetection/assets/81952512/0bed64bc-cdeb-4aa6-9c10-48d205fd190b">

(앱 실행 화면)

## 3. 느낀점
 캡스톤 디자인 프로젝트를 진행하면서 규모가 크고 체계적인 프로젝트를 수행할 수 있는 기회를 가졌습니다. 데이터 수집부터 모델 구현, 앱 구축까지의 End-to-end 과정을 경험했습니다. Mobilenet V2, Vggnet, Resnet, Shufflenet 등 다양한 모델을 구현하고 사용자의 행동을 예측해 보았습니다. 데이터 수집 단계부터 전처리 및 분석 방향을 설계하는 중요성을 깨닫게 되었고, 초기 수집한 데이터와 실제 사용자 환경의 데이터 간의 차이로 인한 정확도 저하 문제를 Transfer Learning을 통해 극복할 수 있었습니다.

 이 프로젝트를 통해 프로그래밍 언어에 대한 이해뿐만 아니라 발표와 보고서 작성 방법을 배울 수 있었습니다. 팀으로 개발하면서 문제 해결과 소통의 중요성을 몸소 깨닫고, 책임감 있게 프로젝트에 참여하는 방법을 배웠습니다. 이 경험을 통해 더 넓은 시야를 갖추고 성장하는 기회를 얻었습니다.





 
