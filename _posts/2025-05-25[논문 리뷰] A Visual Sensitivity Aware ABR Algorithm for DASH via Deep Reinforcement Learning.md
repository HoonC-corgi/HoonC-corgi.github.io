---
title: "[논문 리뷰] A Visual Sensitivity Aware ABR Algorithm for DASH via Deep Reinforcement Learning"
published: False
excerpt: "저자"
---

---

# 논문 리뷰를 시작하며

[제목](링크)

> 본 게시글은 위 논문을 스스로 번역, 재정리한 내용을 포함합니다.

---

# 한글 번역 및 코멘트
## Abstract
ABR 알고리즘에 HVS를 제대로 반영해보자.  
시각적 민감도가 높으면? 비트레이트도 더 주자.  
왜냐? 비트레이트가 높음을 떠나서 비디오 컨텐츠가 HVS에 받아들여지는 것 자체가 QoE에 직접적인 영향을 미치기 때문이다.
비디오에서 컨텐츠 특징과 속성 특징 세트를 추출하고, HVS 시뮬레이션을 고려하여 시각적 민감도를 정확하게 반영하는 토탈 마스킹 효과 모델 확립, 이를 네트워크 상태, 버퍼 점유율, DRL 통해서 비트레이트 선택.
---
### Notes

---

## **I. Introduction**
기존 Content-agnostic ABR
- 미래 네트워크 처리량 예측, 현재 버퍼 점유율 관찰


기존 Content-aware ABR
- Visual attention [14-17]
- users' subjective preference [18-20]
  인간시각관점(HVS)의 관점에서 ABR을 추구해야..
  기존 알고리즘은 단일 특성(움직임 등), 다양하고 복잡한 분포를 가진 정보(하이라이트, 객체 등)만을 고려, 비디오 왜곡(video distortion)에 대한 HVS의 인식 능력을 고려하지 않음.

HVS는 visual masking effect로 인해 일정 수준의 품질 왜곡은 인식할 수 없음이 밝혀짐.
즉, QoE는 인지할 수 있을 정도의 품질 향상을 시켜야 가능함.
이를 모티베이션으로, HVS 특성과 비디오 컨텐츠 간의 관계를 측정하기 위해 시각적 민감성을 도입
이를 위해 HVS를 반영하는 비디오 컨텐츠 Total masking effect을 모델링, ABR에 반영

* 기존 visual sensitivity 모델들은 HVS 메커니즘을 반영하는 데 부족함.
  visual masking effect가 HVS 인식에 미치는 영향을 분석하여 여러 비디오 컨텐츠에 대한 total masking effect 모델을 제안함. 
  모델은 다양한 비디오 특징을 입력으로 채택, multi-stream deep CNN을 통해 특징을 훈련.
  인간 시각 피질에 널리 존재하는 HVS의 피드백 메커니즘을 모델에 통합, 정확도 향상을 달성함.

* total masking effect 모델에 기반한 visual sensitivity aware ABR 설계를위해 visual sensitivity 정의를 제시함.
  visual sensitivity와 강화 학습 알고리즘의 입력 state, 및 보상 함수를 결합.
  이러한 ABR을 통해서 QoE 향상에 더욱이 기여함

* 실제 네트워크 트레이스와 합성 네트워크 트레이스를 사용하여 평가 수행, 최신 visual sensitivity 예측 방법과 비교하여 제안한 total masking effect 모델이 예측 정확도가 더 높고 비디오 해상도 변화에 대해 일관된 성능을 보인다. 실제로 QoE도 주관적 시청 품질도 더 좋았음.

---
### Notes


---
## **II. Related Works**

### **2.1 Existing ABR Algorithms**
#### Content-aware
장면 유형과 모션 강도 정보 사용, 모션 강도가 높으면 QoE 향상
- VAS
- AMUI(Areas of Maximum User Interest)의 위치에 따라 비디오 품질 조정, 동일한 비디오 프레임에서 여러 관심 영역을 지원하는 관심 영역 기반
- 비디오를 객체로 분해, 배경 객체와 전경 객체의 품질 변화
- 비디오의 semantics와 사용자 선호도를 통해 다양한 비디오 장면에 대한 사용자의 관심을 식별, 관심 비디오를 더 높은 품질로 제공
- 사용자가 인식하는 중요한 컨텐츠의컨텐츠 descriptor에 따라 비트레이트 결정
- 사용자의 감정적 요구 분석, 개인화된 감정 선호도를 정량화하기 위해 효과적 관련성 측정하여 ABR

기존 Content-aware ABR 알고리즘에서는 video quality distortion을 고려하지 않음.  
"시각적으로 주목 받는 비디오 컨텐츠에 대해서 더 높은 비트레이트를 선택하더라도, 사용자가 이를 인지할 수 없으면 실제 지각적 품질 향상 체감에는 무의미하다."  
본 연구에서는 다른 품질 지표 제안 대신, QoE 최적화를 위해 video quality distortion에 대한 HVS의 인식을 고려한다는 점에서 차이가 있다.  
이를 통해 네트워크 리소스가 제한된 경우, 사용자가 비디오 품질 저하를 인지하지 못하는 선에서 더 낮은 비트레이트로 전환할 수 있음.

이 연구가 최초.


## **2.2 Modeling of Visual Sensitivity**
Visual masking effect는 자극 간의 상호작용이나 간섭으로 인해 발생하는 복잡한 시각적 인식 메커니즘이다. 이는 주로 휘도 마스킹, 및 시간 마스킹을 포함한 복잡한 공간적 또는 시간적 배경 하에서 왜곡, 가장자리 및 움직임과 같은 자극을 인식하는 데에 있어 HVS의 능력이 감소하는 것을 의미한다 [22].

휘도 마스킹 효과
: HVS가 더 어둡거나 밝은 영역에서 왜곡에 덜 민감함

공간 대비 마스킹 효과
: HVS가 매끄럽거나 구조화된 영역에서 품질 왜곡을 더 잘 인식할 가능성이 높음.

시간 마스킹 효과
: 빠르고 복잡하게 움직이는 물체가 있는 영역에서 품질 변화를 인식하기 어려움, 특정 임계값을 초과하는 속도는 시각적 민감도 손실로 이어질 수 있음.  

이처럼 Visual masking effect는 HVS의 고유한 특성을 효과적으로 반영할 수 있기 때문에 이미지/비디오 처리에서 중요한 역할을 함.  
최근 연구에서 HVS가 다양한 그림/비디오 컨텐츠의 total masking 효과 하에서 인식할 수 있는 최소 왜곡을 의미하는 JND(Just Noticable Difference)라는 개념을 제안함.

전통적인 JND 모델은 픽셀 도메인 모델, 서브밴드 도메인 모델로 나눌 수 있으며, 각각의 픽셀/서브밴드에 대한 JND 임계값을 계산함.
=> 이 방법들은 픽셀 간의 상호작용을 효과적으로 포착하기 어려움.
"인간은 개별 픽셀이 아닌, 그림/비디오를 전체적으로 인식하기 때문."
왜곡된 그림/비디오와 왜곡되지 않은 것들 사이의 JND 임계값을 나타내기 위해 PW-JND/VW-JND(Video Wise-JND)가 추가로 제안되었음.

PW-JND와 VW-JND를 주관적으로 측정하기 위해 많은 JND 기반의 그림/비디오 품질 데이터셋이 개발되었음.  

- MCL-JCI [28]
  : JPEG 압축 표준에 따른 그림 품질 데이터셋 [50개: 원본, 100개: 왜곡된 그림], 원본 JND 데이터를 분석하고 처리하여 계단 품질 함수
  SQF(Sequences Quality Function)을 계산함.

- VideoSet [30]
  : 220개 원본 비디오 시퀀스로 구성된 대규모 데이터셋, 5초 길이와 4개 해상도로 구성, 이진 검색으로 첫 세 개의 jnd 포인트 분포를 측정, 이 주관적인 점수는 JND로 간주할 수 있으나, 비용, 시간이 많이 듦.

객관적인 JND 예측 모델을 개발하기 위한 연구들
- [26]: PW-JND의 예측을 다중 클래스 분류 문제로 설명, 딥러닝으로 압축된 그림의 각 JND 포인트 예측, 원본 그림만을 입력으로 하며 다면 분석을 위한 특징 학습이 어려움.
- [27]: spatiotemporal sensitivity를 pixel by pixel으로 매핑하기 위해 다양한 특징을 제안함.
- [32]: SUR(Satisfied User Ratio) 곡선의 regression을 통해 다양한 JND 포인트를 예측함.
- [33]: GPR(Gaussian Process Regression)을 사용하여 SUR 곡선을 모델링, JND 포인트를 도출하지만, 간접 예측 방법이 직접 예측보다 더 어렵다..

기존 ABR 알고리즘의 단점을 해결하기 위해, 논문에서는 다양한 비디오 컨텐츠의 Total Masking Effect를 효과적으로 모델링하는데 집중함.
딥러닝과 신경망 기술은 이미지/비디오 처리에서 널리 사용되며, 특히 HVS의 응답 메커니즘(시각적 자극 처리 방식)을 시뮬레이션하는 데 효과적.
이를 바탕으로, 비디오 특성을 여러 측면에서 측정할 수 있는 데이터 기반의 mutli-stream CNN-based VW-JNDpredictor를 제안.
두 가지 manual featrues와 multi-stream CNN fusion Net을 결합, 모델은 각 특징을 최대한 활용하여 비디오 특성을 효과적으로 추출함.
또한, 제안된 모델은 Feedback connection을 포함함. 이는 visual cortex에서 중요한 역할, 품질 인지 형성에 필수적. (HVS 모방, 비디오의 맥락적 정보(장면 전환, 객체 지속성)를 강화해 FJND 예측을 정밀화)  

---
### Notes


---
## **III. Motivation**
### **3.1 Video Preprocessing**
[23, 27, 46]에 따르면, 휘도 마스킹 효과, 공간 대비 마스킹 효과, 시간적 마스킹 효과 및 집중도가 visual sensitivity에 영향이 큼이 나타남.  
각 비디오 청크에 대해 각 특징의 청크 수준 평균을 계산하였음. 
Fig.1 분포를 보면, 파란색으로 표시된 lower visual sensitivity 청크는 집중도(saliency)가 낮고 시각적 마스킹 특성이 높음.    
빨간색으로 표시된 higher visual sensitivity 청크는 집중도가 높고 시각적 마스킹 특성이 낮음.  
**즉, visual sensitivity가 낮음 => 시각적 마스킹 특성 높음 => 집중도 낮음 => 변화해도 HSV에서 인지하지 못할 확률 높음**  

### **3.2 Challenges for ABR**
3.1의 두 가지 유형의 비디오 청크를 선택하여 다른 ABR 전략을 비교함.  

VMAF [47]
: 사용자가 인지하는 비디오 품질 측정에 사용, 이는 S-MOS와 밀접한 연관이 있음. 0-100사이 점수 존재, 각 20을 구간으로 unacceptable, poor, fair, good, excellent quality로 구분.

Fig. 2는 [49] 데이터셋에서 Pensieve [11]와 visual sensitivity aware ABR(VS-ABR)의 VMAF, 비트레이트를 보여줌.
영역 1에서는 lower visual sensitivity 청크가 많은 반면, 영역 2에는 higher visual sensitivity 청크가 두 알고리즘에서 공통적으로 많음. (b)에서 VMAF에 대해 VS-ABR이 점수 범위를 더 부드럽게 구현하고 있음을 볼 수 있음.
이 실험을 통해 lower visual sensitivity 청크의 비트레이트를 신중하게 줄이고, 할당된 자원을 higher visual sensitivity 비디오 청크에 할당하면 더 큰 이점을 얻을 수 있음을 확인할 수 있다.
HVS는 lower visual sensitivity 청크의 비트레이트가 높더라도 이를 감지할 수 없기 때문에 특정 비트레이트 수준을 낮추는 것은 실제 지각되는 비디오 품질에 거의 영향을 미치지 않는다.
따라서 visual sensitivity를 기반으로 비트레이트 자원의 차등 할당을 수행하면 자원 활용도와 QoE 향상할 수 있다.
하지만, visual sensitivity를 대략적으로 분류할 뿐이기에, 더 큰 성능 향상을 위해서는 더 정확한 모델링이 요구된다.

---
### Notes


---
## **IV. System Design**
제안된 비디오 스트리밍 시스템은 1) Visual Sensitivity Model, (2) Visual Sensitivity Aware ABR 두 모듈로 구성됨.  

### **4.1 System Structure**
otal masking effect 분석을 기반으로 한 Visual Sensitivity 모델 제안, 이를 ABR에 사용함.
FIg. 3 VS-ABR은 DASH 클라이언트의 state info, 비디오 서버의 Visual Sensitivity 값을 통합, 다음 비디오 청크의 비트레이트를 출력.
이후 클라이언트는 CDN(Content Delivery Network)에 해당 비디오 청크 다운하도록 요청.

Visual Sensitivity Model
: 사전 학습된 pre-trained deep multi-stream CNN model과 HVS 피드백 메커니즘을 결합하여 total masking effect를 학습, 상대적인 visual sensitivity를 계산하기 위해 정규화 수행. 결과는 MPD 매니페스트 파일에 저장, 비디오 세션이 시작될 때 비디오 서버에서 직접 다운로드함.

Visual Sensitivity Aware ABR
: DRL 기반으로 ABR 디자인, 여러 비디오 청크의 throughput, buffer 점유율, visual sensitivity를 종합적으로 고려함. 이 시스템은 high-sensitivity 비디오 청크가 low-sensitivity 비디오 청크에 할당되었을 수 있을 비트레이트를 가져올 수 있도록 하여, visual sensitivity에 비례하여 품질을 최적화 할 수 있도록 한다.  

위 두 가지 구성 요소가 클라이언트 장치의 리소스 제약을 피하기 위해 서버 측에 배포된다는 것을 알 수 있으며, ABR 알고리즘의 성능은 클라이언트와 서버 간의 패킷 전환으로 인한 지연에 크게 영향을 받지 않음.


### **4.2 Visual Sensitivity Model**
total masking effect model, visual sensitivity의 계산에 대해 자세히 설명
(1) 최신 딥러닝과 HVS에 일반적으로 존재하는 피드백 메커니즘을 적용, 비디오 컨텐츠의 total masking effect를 효과적으로 표현할 방법을 얻는다. (2) total masking effect model의 예측 결과와 HVS의 왜곡 민감도 간의 관계를 분석, ABR 알고리즘에서의 상이한 비디오 청크의 visual sensitivity를 얻는다.

비디오 품질 왜곡에 대한 인식은 비디오 컨텐츠와 비디오 기본 속성 두 가지 측면과 밀접, 이 특징들을 모델링하기 위해 서로 다른 사전 정보를 계산할 수 있음. 구체적으로 비디오 프레임에서 서로 다른 visual masking effect를 반영하는 여러 manual 특징 맵을 추출, 비디오 컨텐츠의 다양한 특징을 효과적으로 포착하기 위한 multi-stream CNN 퓨전 네트워크 개발하였음. 딥 CNN에서 추출한 추상적 특징에 과도하게 의존하지 않고, 비디오 속성 특징을 abstract CNN-based 특징에 통합하여 피드백 메커니즘을 실현함. 이는 기존의 VW-JND에서는 충분히 활용되지 않았음


#### **4.2.1 Input of The Total Masking Effect Model**
제안된 모델의 입력은 visual masking effect를 반영하는 특징과 비디오의 고유 속성을 반영하는 특징 두 부분으로 구성된다.
논문에서는 각 비디오 청크의 framerate(FR), Resolution(RE), Bitrate(BR)를 포함한 세 가지 기본 속성 특징을 채택함. 
또 각 비디오 청크를 균일하게 생플링하여 비디오 프레임 그룹을 얻음. 
그다음 각 비디오 프레임에 대해 Spatial Randomness Map (SRM), luminance map (LM), Temporal map (TM), Saliency map (SM)을 포함한 네 가지 특징 맵을 계산. 비디오 프레임 k에 대해 위의 특징을 포함하는 특징 집합 F<sub>k</sub>를 정의함.   
* Spatial Regularity: 픽셀 (i, j)에 대한 Spatial Regularity는 픽셀이 주변 픽셀들과 얼마나 일관성 있는 패턴을 가지는지를 나타내는 neighborhood prediction error로 측정함
* _LM<sub>k</sub>(i, j)_ : 비디오 프레임 k에 대한 휘도 _L<sub>k</sub>(i, j)_를 표현함
* _TM<sub>k</sub>(i, j)_ : 샘플링된 비디오 프레임 간의 밝기 차이를 계산하여 motion intensity를 반영, _d_는 두 프레임 간의 간격을 나타냄
* _SM<sub>k</sub>(i, j)_ : frequency-tuned saliency detection method [51]를 사용함.

그림 4는 비디오셋의 청크 37, 98에 있는 두 프레임의 특징 맵  
4(a), 4(e) 비교하면 596번 프레임이 11보다 텍스처가 풍부, 공간적 무작위성이 높음. SRM이 높음.  
4(b), 4(f) 비교하면 11번 프레임이 픽셀 밝기가 더 낮음.  
4(c), 4(g) 비교하면, 11번 프레임이 검은 픽셀이 더 많아 프레임 간 밝기 차이가 작고 모션 intensity가 낮음.  
4(d)의 saliency 분포는 4(h)보다 집중되게 나타남.  
공간적 무작위성이 높을 수록 motion intensity가 높고, saliency가 낮을 수록 시각적 민감도가 낮아짐.  


#### **4.2.2 Output of The Total Masking Effect Model**
VW-JND 예측기로 제안된 모델의 출력은 First Just Noticeable Difference(FJND) 포인트를 예측하는 것임.

FJND point
: 최고 품질 수준(무손실 인식)과 2차 품질 수준(손실 인식) 사이의 전환점으로, 관찰자가 인식할 수 있는 가장 작은 왜곡 수준의 값을 가진다.

FJND 포인트에 해당하는 압축 비디오에서 HVS는 압축되지 않은 원본 비디오에 대한 왜곡을 인식할 수 없으므로, FJND 포인트는 비디오 컨텐츠의 total masking effect를 정확하게 반영할 수 있음.
이를 모티베이션으로, 비디오 컨텐츠의 total masking effect를 FJND 포인트의 예측기로 모델링하고, 입력 특징을 넣어 regression함.
FJND 포인트는 일반적으로 conding parameters(e.g., QP), 비트레이트, 품질 지표(e.g., PSNR, SSIM 등)로 표현함. QP는 비디오 코딩의 품질을 제어, 비트레이트는 단위 시간 당 비디오 데이터의 양, PSNR과 SSIM은 영상 및 비디오 처리에서 인기 있는 두 가지 객관적 품질 지표임.
왜곡 정도를 반영하는 데에 있어, 객관점 품질 지표의 효과와 SSIM이 압축 왜곡에 민감하지 않다는 점을 고려하여, 주어진 압축 비디오에서 샘플링된 비디오 프레임의 평균 PSNR을 FJND 포인트로 측정함.


#### **4.2.3 Training of The Total Masking Effect Model**
Fig 5는 제안된 total masking effect model의 구조적 설계를 나타냄. 
영장류 시각 피질의 조직에서 영감을 받은 고급 VGG 네트워크 [54]를 기반으로 함. 
네 개의 독립적인 CNN 서브넷을 사용하여 서로 다른 특징맵을 합성함. 각 서브네트워크는 _SRM, LM, TM, SM_을 입력으로 받아 세분화된 특징을 추출함. 
그후 네 개의 서브넷에서 특징맵과 속성 특징(BR, Resolution, Framerate)에서 추상적인 특징을 함께 연결하여 피드백 모듈로 전송함. 
피드백 후 처리된 특징을 Fully Connected Layer에 입력, FJND 포인트를 예측함. 
마지막으로 손실 값을 계산, 다음 목표 예측을 수행.  

_PSNR<sub>k</sub> = FB(Concat(sub<sub>1</sub>(SRM<sub>k</sub>(i, j)), sub<sub>2</sub>(LM<sub>k</sub>(i, j)), sub<sub>3</sub>(TM<sub>k</sub>(i, j)), sub<sub>4</sub>(SM<sub>k</sub>(i, j)), FR<sub>k</sub>, RE<sub>k</sub>, BR<sub>k</sub>))_  

_FB, Concat, sub<sub>i</sub>_ 는 각각 피드백, 연결 및 서브네트워크 i의 프로세스를 나타냄.  
딥 뉴럴 네트워크의 훈련에는 많은 샘플이 필요하고 사용가능한 JND 데이터셋의 수가 제한되기 때문에, 패치 기반 훈련 방법을 채택하여 데이터 샘플을 수동으로 증가시켰음.  
각 입력 특징 맵은 여러 패치로 나뉘며, 일정 수의 패치를 무작위로 선택하여 해당 비디오 청크의 FJND 포인트로 라벨링하여 훈련함. 입력 패치의 크기를 맞추기 위해 네트워크는 세 개의 레이어, Conv1, Conv2, Maxpool로 확장됨.  

주어진 모델의 훈련 과정은 주로 특징 융합, 피드백 루프, 스페이셜 풀링 세 부분으로 구성된다.  

특징 융합
: 각 서브넷에서 컨볼루션 레이어로 추출된 특징 벡터와 속성 특징을 결합하기 위해 ```concat()```사용

피드백 모듈
: 피드백 연결을 처리, 시각 피질의 high-level 영역이 low-level 영역에 영향을 미치도록 하는 gudiance function을 실현할 수 있도록 강력한 high-level representations를 생성함

후속 처리
: 처리된 특징은 네트워크의 후속 regression 부분으로 전달되어 추가 매핑이 수행됨.

가중 평균 패치 집계
: 서로 다른 공간적 분포(spatial distributions)와 가중치(weights)를 가진 지역 패치(local patches)를 집계하기 위해 가중 평균 패치 집계 전략(weighted-average patch aggregation strategy)을 사용함. 패치의 가중치는 ReLU 함수로 활성화, 항상 양수


#### **4.2.4 Calculation of Visual Sensitivity**  
Visual Sensitivity는 total masking effect model을 기반으로 FJND 포인트와 HVS의 왜곡 민감도 사이의 관계를 분석하여 정의됨.  
FJND 포인트가 클수록 비디오 품질이 높아지고, 해당 비디오의 왜곡 수준이 낮아짐. 하지만 고품질에서의 품질 왜곡 발생 시 체감이 커지므로 HVS가 비디오에 대한 왜곡을 인식할 가능성이 높음. 즉 total masking effct가 고품질 비디오에서 약하게 나타날 수 있음.
따라서 시각적 민감도는 FJND 포인트가 증가함에 따라 증가함(PSNR 메트릭 사용 기준).  
이 관계를 기반으로 비디오 청크의 PSNR은 상대적인 Visual Sensitivity를 나타내도록 정규화 됨.  

비디오 청크 t의 PSNR 값을 정규화하여 Visual Sensitivity를 구함.  
PSNR<sub>t</sub>는 청크 t의 PSNR 값으로, 프레임별 PSNR을 평균내어 얻은 값임.  
VS<sub>t</sub>는 0~1 사이로 정규화되며, PSNR이 클수록 더 큰 값을 가짐.


### **4.3 Visual Sensitivity Aware ABR**

본 논문에서 제안하는 Visual Sensitivity Aware ABR은 강화학습 알고리즘 A3C [21]을 기반으로 함.  
A3C 알고리즘은 기존 강화학습 알고리즘 구조에 Actor 네트워크와 critic network가 추가된다.  
액터 네트워크는 계산된 확률 분포에 따라 행동을 수행하는 전략 함수, 비평 네트워크는 현재 상태에 따라 행동을 평가, 가치를 예측하는 함수이다.  
RL 에이전트는 일반적으로 각 타임 스텝의 환경 정보를 기반으로 의사결정을 내리며 보상에 따라 모델을 업데이트 한다.  
에이전트는 환경과 상호 작용, 지속적인 학습으로 더 높은 누적 보상을 가진 행동을 취한다.

입력 상태  
_s<sub>1</sub>_: player environment features
_s<sub>2</sub>_: low visual sensitivity video chunk features  
_s<sub>3</sub>_: medium visual sensitivity video chunk features  
_s<sub>4</sub>_: high visual sensitivity video chunk features  

QoE 모델을 VS와 통합하여 Visual sensitivity와 일치하는 ABR 정책을 설계할 수 있도록 보상 함수를 설계함.  
알고리즘의 목표
1) HVS가 더 민감해질 때, 현재 청크의 품질을 신중하게 낮출 것
2) high visual sensitivity 비디오 청크가 더 높은 비디오 품질을 가질 것
3) 미래에 visual sensitivity가 떨어질 경우 최신 몇 개 청크의 비트레이트는 증가

**State**  
_s<sub>t</sub>_ = (_s<sub>1</sub>_, _s<sub>2</sub>_, _s<sub>3</sub>_, _s<sub>4</sub>_)  
에이전트는 비디오 청크 t를 다운로드 후, _s<sub>t</sub>_ 를 액터 네트워크와 크리틱 네트워크에 전달하며, 이 상태는 다음 비트레이트를 선택하는 데에 사용함.  
_s<sub>1</sub>_: player environment features  
  = (지난 K개 비디오 청크의 평균 **throughput**, 지난 K개 비디오 청크의 **다운로드 시간**, 현재 **버퍼 크기**, **마지막 비디오 청크의 지각 품질**)    

_s<sub>2</sub>_: low visual sensitivity video chunk features  
  = (다음 low VS chunk의 **각 비트레이트에 대한 지각 품질 VMAF 값**, next low VS chunk의 크기 벡터, 남아 있는 low VS chunk의 개수)   

_s<sub>3</sub>_: medium visual sensitivity video chunk features  
  = _s<sub>2</sub>_ 와 visual sensitivity 정도만 상이함.  

_s<sub>4</sub>_: medium visual sensitivity video chunk features    
= _s<sub>2</sub>_ 와 visual sensitivity 정도만 상이함.  

**Training**  
현재 상태를 바탕으로, 액터 네트워크는 특정 정책을 통해 액션 _a<sub>t</sub>_ 를 출력하며, 이는 다음 비디오 청크의 비트레이트 결정을 의미함.   
정책은 _s<sub>t</sub>_ 에서 행동 _a<sub>t</sub>_ 를 취할 확률을 나타냄.   
크리틱 네트워크는 이 정책을 기반으로 예측값을 출력함.   
행동 _a<sub>t</sub>_ 가 적용된 후, 환경은 에이전트에게 청크 _t_ 의 보상을 제공. 
RL 에이전트의 학습 목표는 누적 보상을 최대화하는 최적 정책 찾기의 과정임. 
8개 에이전트를 병렬로 생성하였음 [11].

**Reward** 

비디오 청크 t가 비트레이트 _a<sub>t</sub>_ 에 따라 다운로드된 후, 성능을 다음 보상 함수를 통해 평가함.

---

## **V. Experiment and Evaluation**

1. Total masking effect model - 기존 VW-JND 예측 방법의 성능 비교
2. Visual Sensitivity aware ABR과 여러 최신 연구의 시뮬레이션 및 실제 환경에서의 성능 평가


### **5.1 Evaluation of Total Masking Effect Model**
#### **5.1.1 Experimental Dataset and Setting**

실험 환경
데이터셋
- VideoSet [31]  
  H.264/AVC 압축 표준에 따른 JND 기반 비디오 품질 데이터셋  
  640×360(360p), 960×540(540p), 1280×720(720p) 및 1920×1080(1080p)인 220개의 원본 비디오 포함  
  각 원본 비디오마다 51개의 왜곡된 비디오가 서로 다른 QP 인코딩을 사용하여 생성됨.  
- 각 해상도와 계산된 FJND 포인트의 원본 비디오를 샘플로 사용, 램덤 셔플 6/2/2 비율 train/valid/test 셋
- 데이터 확장을 위해 랜덤 패치 선택 전략 채택
- 50만 개 이상의 데이터 샘플 보유

Specification
- NVIDIA GeForce RTX 2080ti, 16GB MEM
- Pycharm 소프트웨어와 Python 3.6.8 및 Pytorch 1.4.0.

학습 세부사항
- epochs = 3,000
- 배치: 4x32(패치) = 128패치 크기
- 활성화 함수: Adam
- lr = 1e-4
- dropout = 0.5

비교 대상
- GJND [27]  
  SVR을 통해 그룹 기반 FJND 포인트의 평균을 예측
- SUR-FJND[32]  
  SVR을 사용하여 여러 SUR 곡선을 예측, 예측된 SUR 곡선에서 FJND 포인트 도출
- GPR-SUR [33]  
  GPR을 사용하여 SUR 곡선을 모델링, 주어진 SUR 값 하에서 비트레이트를 FJND 포인트로 예측
- 비디오 컨텐츠 특징만을 multi-stream CNN network와 결합하는 예측 방법
- 비디오 컨텐츠 특징, 비디오 속성 특징을 결합하는 예측 방법을 비교함.


#### **5.1.2 Evaluation Results**
예측 정확도를 평가하기 위해 두 가지 일반적으로 사용되는 지표 MAE, Variance (VAR) 사용  
전체 평가를 위해 5-fold cross validation 사용.  

- GJND  
  manual-fused features의 부정확성 때문에 두 지표 중 가장 큰 예측 오차
- GPR-SUR  
  GJND보다 상대적으로 더 나은 성능을 달성
- Video content features with multi-stream CNN network  
  MAE, VAR 각각 최소 12.32%, 9.85% 감소시킬 수 있어 고려된 특징과 모델 구조의 효과를 누릴 수 있음.
- Video content features, HVS feedback module with multi-stream CNN network  
  예측 정확도 더 좋음, 예측 오차 0.9 미만으로 줄임.  
  네 해상도의 평균 MAE, VAR이 0.74, 0.75로, HVS 인식 메커니즘을 시뮬레이션하는 데 있어 견고함과 효과성을 더욱 입증함.  

소거 실험 진행  
네 가지 특징 맵의 효과성을 추가로 분석하기 위함  
비디오 프레임 k에 대해 무작위로 3개의 특징 맵으로 하위 집합을 정의, 이를 모든 특징 맵을 포함하는 집합과 테스트용 모델의 입력으로 사용.
1) only SRM
2) Containing SRM and LM
3) Containing SRM, LM, TM

Fig. 7에서 (a), (b), (c)와 비교했을 때 (d)의 대부분의 점들이 y=x 선 근처에 더 집중되어 있어 네 가지 특징의 결합이 최소 예측 오차를 달성할 수 있음을 보임.


### **5.2 Evaluation of ABR Algorithm**  
#### **5.2.1 Simulation Experiment**
Python 3.6.8, Tensorlow 1.5 및 [11]에서 제공하는 청크 레벨 시뮬레이터를 기반으로 VS-ABR을 구현 및 평가  

데이터셋
- G/HSDPA 모바일 데이터셋[49]  
  버스, 기차 등 여러 환경에서 비디오 전송하는 모바일 장치에서 생성된 30분 throughput 측정이 포함되어 있음.
- FCC 대역폭 데이터셋 [56]  
  100만 개 이상의 throughput 측정이 포함되어 있음  
  각 5초로 세분화된 평균 throughput을 2100초 이상 포함  
  비디오에는 EnvivoDash3 [45]와 VideoSet [31]의 8-18개의 비디오 시퀀스로 구성된 7개의 비디오가 포함됨.  

각 데이터 셋에 포함된 8개의 비디오에 대하여 VS-ABR과 RobustMPC [7], Pensieve[11]와 비교

각 비디오 세션마다 무작위 네트워크 트레이스의 80%가 VS-ABR과 Pensieve의 훈련 세트로 선택되고, 나머지 20%는 테스트 세트에 포함됨.  
Huang [12]이 제안한 QoE 모델이 평가에 사용되며, 이는 최근 연구들보다 우수한 것으로 입증됨.


**Comparison on real network traces**    
모든 비디오 청크의 Visual Sensitivity를 세 수준으로 구분  
* Low VS: [0.6, 1.5]
* Medium VS: [1.5, 2.0]
* High VS: [2.0, 2.6]

(12)의 µ, ξ를 각 2, 0.6으로 설정.  
테이블 3은 3G/HSDPA 데이터셋에서 각 VS 레벨에 따른 ABR 알고리즘의 평균 비트레이트를 나타냄.  
각 비디오 청크의 동등한 처리로 인해 RobustMPC, Pensieve에서 서로 다른 BS를 가진 비디오 청크 간의 평균 비트레이트가 가까운 반면, 
VS-ABR은 고감도 비디오 청크에서 더 높은 비트레이트를 전송하므로 비트레이트 표준 편차가 더 큼.
FCC에 대해서도 유사한 결과가 나타남.  

Fig 8은 각 비디오 세션에서 세 ABR 알고리즘의 VMAF, 리버퍼링 시간, quality smoothness 및 overall QoE 분포를 나타냄.  
8(a)에서 모든 비디오 세션에서 VS-ABR의 VMAF이 더 작고 높은 범위에 분포되어 있으며, 평균 VMAF이 2.08%-18.34% 향상됨이 나타남.  

8(b)는 0이 아닌 비디오 리버퍼링 시간의 분포만을 나타내며, 이는 대부분의 비디오 청크의 리버퍼링 시간은 0초로 유지될 수 있기 때문임.  
VS-ABR이 high VS 비디오 청크를 더 높은 비트레이트로 전송하는 것을 선호하기 때문에, 비디오 세션 4와 7과 같은 경우 리버퍼링이 증가할 수 있음.  
하지만, 세 알고리즘 모두 평균차이 0.5초 미만으로 유사하며, 더 큰 리버퍼링 시간을 유발하지 않고 다른 메트릭의 성능을 향상시킬 수 있음을 나타냄.

VMAF이 시간에 따라 변동될 때 그 변동이 더 부드럽게 이루어지도록 설계되었으므로, VS-ABR은 quality smoothness를 효과적으로 줄일 수 있음.  

VS-ABR의 overall average QoE는 2.44%-22.82% 증가함.
즉, VS-ABR은 리버퍼링 시간 최소화, video quality smoothness, 지각적 비디오 품질 극대화를 통한 사용자 QoE를 효과적으로 향상.


**Comparison on synthetic network traces**  
서로 다른 네트워크 조건을 나타내는 세 그룹의 네트워크 트레이스 합성
* Low Throughput: 0-2 Mbps
* Medium Throughput: 0-4 Mbps
* High Throughput: 0-9 Mbps

figure 9: 여러 ABR 알고리즘의 평균 normalized QoE  
9(a): VS-ABR은 low, medium throughput에서 최소 17.65%, 14.94% QoE 향상, high throughput은 비트레이트가 보통 더 높은 수준으로 유지. 
이를 더 개선하면 지각 비디오 품질이 약간 증가하면서도 낮은 품질을 쉽게 생성할 수 있음.

high throughput에서 VS-ABR의 성능을 더욱 향상시키기 위해 (12)의 매개변수 µ을 신중하게 1로 줄여 차등 비트레이트 할당 정도를 약화  
실험 결과 Fig 9(b)의 VS-ABR이 high throughput에서 9(a)보다 더 높은 QoE 향상을 이룸.


**Comparison for different VS prediction methods**  
Fig. 10(a)는 SUR-FJND [32] 및 GPR-SUR [33]의 예측 결과를 VS-ABR과 동일한 방식으로 ABR 알고리즘에 도입하여 비교한 예측 결과를 나타냄.  
예측된 PSNR은 VS로 정규화, 제안된 방법이 실제 값에 가장 가까움.  
10(b)는 동일한 네트워크 트레이스 하에서 다양한 ABR 알고리즘의 VMAF을 비교한 결과임.  
VS-ABR은 대부분의 경우 다른 알고리즘과 동일하거나 더 높은 VMAF을 보였으며, 평균 VMAF 개선률이 최소 2.93%임. 
비디오 청크 35, 15(high visual sensitivity)와 비디오 청크 7, 13, 22(medium visual sensitivity)에서는 SUR-FJND, GPR-SUR의 예측 오차로 인해 VMAF이 일정량 감소하였음.  


#### **5.2.2 Real Experiment**  
dash.js [57]을 기반으로 ABR 알고리즘을 구현하였음. 클라이언트 플레이어는 Google Chrome 브라우저, 
비디오 서버는 Apache 2.4.7, 우분투 12.04를 사용하는 클라이언트와 동일한 컴퓨터에서 실행됨.  
클라이언트 플레이어와 서버 간의 상호작용은 재생 상태로 요청을 보내고 비트레이트 결정으로 응답을 받는 XMLHttpRequests를 사용하여 이루어짐.  
DASH 플레이어는 60초의 버퍼 용량을 가지도록 구성됨.  
평가에 사용된 네트워크 트레이스와 비디오 트레이스는 5.2.1과 유사, Mahimahi(58)를 사용하여 다양한 네트워크 조건을 에뮬레이트, 비디오 트레이스를 만화, 스포츠, 자연 및 실내 장면을 포함하는 네 가지 비디오 유형으로 나눔.

주관적 평가  
- 피험자: 자대 정상 시력 16명 (M:F = 1:1)
- 테스트 환경: 테스트 비디오 지속 시간 30~50초
- [18]을 기반으로 한 the paired comparison method를 채택
- 각 피험자는 서로 다른 ABR 알고리즘에 따라 두 번 비디오를 시청하고, 더 낫다고 생각하는 비디오에 투표, 두 비디오에 무관심한 참가자는 투표에 포함하지 않음.

Table. 4는 VS-ABR을 Robust MPC, Pensieve, GPR-SUR-ABR과 비교한 결과를 나타냄.  
VS-ABR은 시청자의 시각적 민감도를 고려하여 주관적인 시청 경험을 개선함으로, RobustMPC 및 Pensieve보다 더 많은 표를 받으며, 만화 및 스포츠에 더 효과적임(temporal masking 효과가 더 강함).  
세 비교 그룹 중 VS-ABR과 GPR-SUR-ABR의 비교의 결과가 크게 차이 나지 않았는데 이는 둘 다 시각적 민감도 인식 메커니즘이기 때문임.  
그러나 VS-ABR이 더 정확한 예측 정보로 여전히 몇 표를 더 얻었음.  
비디오 컨텐츠에 대한 이해와 비디오 품질에 대한 판단에 영향을 받아 사용자는 시청 경험에 무관심할 수 있으나, 대부분의 경우 제안된 알고리즘이 더 좋은 결과를 보였다.

### **5.3 System Overhead**  
total masking effect model을 훈련, RL을 사용하여 ABR 알고리즘을 생성하는 오버헤드를 측정하였음.  
total masking effect model의 훈련을 위해 GPU 병렬 모드를 채택, 총 훈련 시간은 약 5시간이 소요됨.  
제안된 방법을 사용하여 ABR 알고리즘을 훈련하는 데는 각 반복마다 310ms가 소요, 이는 8개의 에이전트가 비동기적으로 매개변수를 업데이트하는 것과 연관됨.  
Pensieve [11]과 비교하여, VS-ABR 알고리즘의 런타임 오버헤드는 CPU 사이클과 RAM 사용량 측면에서 1% 미만임.  
본 논문에서 제안된 알고리즘은 오프라인 훈련 전략과 온라인 서버 배포를 채택하여 기존 아키텍처에 대규모 수정 없이 동작 가능함.


---
### Notes


---

## **VI. Conclusion**  


---
### Notes


---
## **VII.**


---
### Notes


---
## **VIII.**


---
### Notes

---

> ### Profile
>
>
> ***Seong Hun KIM***
>
>
> **M.S. course**  
> **Dept. of Computer Science Engineering | Yeungnam University, Repulic of Korea**
>
> ![yu signature](https://github.com/HoonC-corgi/Convolution_Filter_Application/assets/118245330/37c81d9e-cfb8-4aee-8497-ff1071b2458b)
>
> **Phone** [010 - 6685 - 1140](tel:010-6685-1140)  
> **Mail** [tgh7544@naver.com](mailto:tgh7544@naver.com)  
> **LinkTree** [https://linktr.ee/HoonC_corgi](https://linktr.ee/HoonC_corgi)














