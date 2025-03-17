---
title: "[논문 리뷰] Overall Quality Prediction for HTTP Adaptive Streaming Using LSTM Network"
published: False
excerpt: "Huyen T. T. Tran, Duc V. Nguyen, Duong D. Nguyen, Nam Pham Ngoc and Truong Cong Thang (IEEE TRANSACTIONS ON CIRCUITS AND SYSTEMS FOR VIDEO TECHNOLOGY • AUGUST 2021)"
---

---

# 논문 리뷰를 시작하며

오랜만에 블로그를 작성한다. 학부를 졸업하기 전에 논문을 한 편 써보겠다는 것을 목표로 한참을 달리다 보니 그 간의 일들은 블로그에 담지 못했다.  
돌아보면, 남들보다 뛰어나게 성적을 받고, 성과를 내며 학교를 다니지는 못했지만, 목표가 있는 삶을 살아 왔고, 또 그것들을 모두 이루어 보았다는 점에서 스스로 위안해 본다.

이번 논문은 일전에 리뷰하였던 논문의 저자들이 이어서 투고한 논문이다.


**[Overall Quality Prediction for HTTP Adaptive Streaming Using LSTM Network](https://ieeexplore.ieee.org/abstract/document/9247981)** - Huyen T. T. Tran, Duc V. Nguyen, Duong D. Nguyen, Nam Pham Ngoc and Truong Cong Thang (IEEE TRANSACTIONS ON CIRCUITS AND SYSTEMS FOR VIDEO TECHNOLOGY • AUGUST 2021)
> 본 게시글은 위 논문을 스스로 번역, 재정리한 내용을 포함합니다.

---

# 한글 번역 및 코멘트
## Abstract

HAS는 현대 멀티미디어 전송을 위한 가장 인기 있는 솔루션이다.

하지만 네트워크 대역폭의 변동으로 인해 스트리밍 중에 비디오 품질은 크게 달라진다.

그러므로, HAS에서의 핵심 과제는 스트리밍 세션의 overall quality를 어떻게 평가할지에 있다.

본 논문에서는 스트리밍 세션에서의 각 세그먼트가 set of features로 표현되는 overall quality 예측을 위한 머신 러닝 접근 방식을 제안한다.

feature set의 두 가지 옵션을 조사하였다. 첫 번째 옵션에서 우리는 **segment quality, content characteristics, stalling duration, padding**, 네 가지 feature를 사용하였다.

두 번째 옵션에서는 **bitstream-level parameters, stalling duration, padding**, 세 가지 feature를 사용하였다.

각 옵션에서 features를 stalling event와 품질 변동의 발생 간의 temporal relations를 탐구할 수 있는 LSTM 네트워크에 입력하였다.

overall quality는 선형 회귀 모듈을 사용하는 LSTM 네트워크의 출력으로부터 예측되었다.

실험 결과에 따르면, 제안된 접근법은 높은 예측 성능을 보였으며, 기존의 일곱 가지 접근법을 능가하였다.

---

### Notes

제안된 접근법
* LSTM using Linear Regression Module

입출력
* input
  * option 1  
    Segment quality content characteristics, stalling duration, padding
  * option 2  
    bitstream-level parameters, stalling duration, padding
* output
  * overall quality

---

## I. Introduction
HAS에서 비디오는 상이한 품질 수준의 여러 버전으로 인코딩 되며, 각 인코딩 버전은 세그먼트 시퀀스로 나뉜다.

세그먼트는 공용 웹 서버에서 호스팅 되는데, 네트워크 상태에 따라서 클라이언트는 적합한 세그먼트의 버전을 결정하고, 웹 서버에 해당 버전을 요청하기 위한 HTTP requests를 전송한다.

선택된 세그먼트 버전은 네트워크 대역폭의 변동에 따라서 스트리밍 세션 중에 크게 변할 수 있으며, 이는 품질 변동을 야기한다.

또한 playback deadline 전에 세그먼트가 도착하지 못한 경우 stalling event가 발생한다.

HAS에서 핵심 과제는 품질 변화와 stalling event의 영향을 고려하여 스트리밍 세션의 전체 품질을 평가하는 방법에 있다.

스트리밍 세션의 품질을 측정하는 일반적인 방법으로는 Recommendations ITU-R BT.500-13, ITU-T P.880 두 가지 방법이 있다.

* 순간적인 품질(Instantaneous quality)는 스트리밍 세션 중에 순간적으로 인식되는 품질을 의미한다.
* 전반적인 품질(Overall quality)는 스트리밍 세션의 끝에서 측정되는 전체 세션의 품질을 의미한다.

서비스 프로바이더의 경우 사용자에게 최고의 품질 경험을 제공하기 위해 이 두 가지 품질 모두 측정하는 것이 중요하다.

순간적인 품질의 측정은 impairment 이벤트에 대한 사용자의 즉각적인 반응을 이해하는 데에 유의미하다(i.e., 품질 변동, stalling event).

현재까지 순간적인 품질 평가 및 모델링과 관련된 몇 가지의 연구가 있었다.

한편, 전반적인 품질의 측정은 서비스 프로바이더가 제공하는 서비스의 품질을 이해하는 데에 중요하다. 본 연구에서는, 전반적인 품질 측정에 초점을 두었다.

전반적인 품질을 예측하기 위한 대부분의 기존 접근법들은 분석 모델 기반의 접근법이다.

이러한 접근법들에서 품질 변동의 영향은 품질 변동의 횟수, 평균, 최소값, 세그먼트 품질 값의 히스토그램과 같은 일부 통계값을 사용하여 모델링된다.

stalling event의 영향에 대해서는 stalling event의 수와 합, stalling duration 히스토그램이 일반적으로 사용된다.

전반적인 품질을 예측하기 위해 이러한 통계값들은 선형 함수와 로그 함수같은 분석 함수를 사용하여 풀링된다.

기존 접근법 중 일부는 품질 변동과 stalling event의 영향을 함께 고려하였다.

문헌에 따르면, 전반적인 품질 예측을 위한 고급 머신 러닝 접근법은 일부 존재한다.

이는 부분적으로 학습을 위한 충분한 데이터셋이 부족하기 때문이다.

특히, 공적으로 사용 가능한 데이터 셋의 대부분은 두 가지의 주요 문제가 있다.

첫 째, 데이터 셋의 사이즈가 작아 기계 학습 접근법의 학습을 할 때에 오버 피팅을 초래할 것으로 보인다.

둘 째, 기존의 데이터셋은 실제 스트리밍 시나리오를 반영하지 않는데 이는 아래로 인한 결과이다.

1) 데이터셋에 포함된 세션은 일반적으로 20초 이내의 길이로 매우 짧다.  
2) 오직 품질 변동 또는 stalling event만을 고려한다.

전반적인 품질 예측을 위한 최초의 고급 머신 러닝 접근법은 [15]의 연구이며, 랜덤 신경망을 사용하였다.

해당 접근법의 입력으로는 모든 비디오 프레임의 모든 매크로 블록에 대한 양자화 파라미터 값의 평균, stalling event 수, stalling duration의 평균 및 최대값으로 구성된다.

해당 접근법은 16초 길이의 118개의 스트리밍 세션이 포함된 데이터셋으로 측정하였다.

[16]의 저자는 전반적인 품질을 예측하기 위해 회귀 모델을 사용하는 다른 고급 머신 러닝 접근법을 제안하였다.

그들의 접근법에서 각 세션은 세그먼트 품질 값의 평균, 세그먼트 품질이 감소한 시간, 마지막 impairment 이벤트가 발생한 이후의 시간(ie., stalling event 또는 세그먼트 품질 저하), stalling duration 총합, stalling event 수 5개의 통계값으로 특정된다.

이를 위해 선형 휘귀, SVR, 앙상블 기법 세 가지의 회귀 모델이 고려된다.

대략 72초 길이의 세션 112개로 구성된 데이터셋을 사용하여 SVR이 최고의 평균 예측 성능을 보였다.

데이터셋의 세션은 8개의 직접 구성한 품질 변동 패턴을 가지며, 각 패턴은 오직 하나의 세그먼트 품질 저하와 최대 두 번의 stalling event를 가진다.

ITU-T P.1203.3 표준의 최신 단계에서는 스트리밍 세션의 전반적인 품질을 사전에 결정하기 위한 고급 머신 러닝 접근 방식이 제안되었다.

이 접근법은 세그먼트 품질 값의 첫 번째, 다섯 번째, 열 번째 백분위 수, 지연 기간의 합, stalling event 등 다양한 통계를 포함하는 랜덤 포레스트 모델을 사용한다.

[22]에 따르면, 이 접근법은 60개의 60초 분량의 긴 세션과 240초 분량의 긴 세션 15개를 사용하여 검증되었다.

대부분의 분석 모델 기반, 고급 머신러닝 기반 접근법을 사용하는 기존 접근법에서는 입력은 위에서 설명한 세션의 일부 통계값만을 다룬다.

그러나 이러한 통계값들은 impairment 이벤트들 간의 시간적 관계를 놓치기에 스트리밍 세션에서 발생할 수 있는 impairment 이벤트를 충분히 반영하지 못한다.

가령, 같은 횟수의 stalling event, stalling durations의 합, 연속적인 stalling event들이 간헐적으로 발생하는 그것들과 비교하여 야기할 수 있는 부정적인 영향을 반영하지 못한다.

그러므로, 스트리밍 세션의 impairment 이벤트 간의 시간적 관계를 충분히 추출할 수 있는 접근법을 개발하는 것이 필수적이며, 이것이 본 연구의 핵심 동기이다.

본 연구에서는 HAS 세션의 전반적인 품질 예측을 위한 새로운 머신 러닝 접근법을 제안한다. 제안된 접근법에서는 세션 기반의 통계값 대신 세그먼트 기반의 값들을 입력으로 사용한다.

또한, memory를 사용하여 impairment 이벤트 간 시간적 관계를 효과적으로 추출하는 LSTM을 입력을 풀링하기 위해 사용한다.

문헌에 따르면, LSTM은 instantaneous 품질 예측과 서비스의 예측 응답 시간 예측과 같은 품질 예측의 일부 작업에 응용된다.

그러나 현재까지 LSTM을 HAS의 전반적인 품질 예측을 위해 사용한 연구는 없다.

본 연구의 핵심 기여는 아래와 같이 요약된다.

* 첫 째, LSTM을 사용하여 HAS 세션의 전반적인 품질을 예측하는 새로운 머신 러닝 접근법을 제안한다.  
  각 세그먼트는 몇 가지 세그먼트 features를 통해 표현되며, LSTM의 입력으로 세그먼트 기반의 값을 사용하고 풀링하여 전반적인 품질을 예측하는 최초의 연구이다.
* 둘 째, 기본 LSTM, 고급 LSTM 두 개의 상이한 LSTM 네트워크를 조사한다.  
  특히, 제안된 접근법의 성능에 패딩이 미치는 영향을 시험한다.  
  또한 입력 features를 complex와 simple 두 가지의 옵션으로 제안하고 분석한다.  
  complex 옵션은 세그먼트 품질, 컨텐츠 특성, stalling duration, 패딩으로 구성된다.
* 셋 째, 제안된 접근법은 세 가지의 데이터셋을 사용하여 평가되었다.  
  1) 60-76초 길이의 세션 515개, 그 중 332개는 실제 스트리밍 테스트베드에서 얻은 데이터셋  
  2) 1분 길이의 세션 120개로 구성된 데이터셋  
  3) 8초 길이의 세션 588개로 구성된 데이터셋  
  실험 결과에서 제안된 접근법은 매우 높은 성능을 보였으며, 제안된 접근법과 기존 일곱가지 접근법의 예측 성능을 비교하였으며, 제안된 접근법은 가장 좋은 성능을 보였다.

위 작업의 일부는 [25]에 제시되어 있다. 이전 연구와 비교할 때에, 본 연구는 새로운 점을 가진다.  
  1) 고급 LSTM을 사용하여 제안된 접근법의 성능을 향상 시켰다.    
  2) 효율적이고 효과적인 feature 셋의 새로운 옵션(simple option)을 제안하였다.    
  3) 세그먼트 품질 feature 표현에 사용되는 다섯 가지 추가 메트릭을 포함하였다.    
  4) LSTM 입력에 스위칭 주파수 특징을 추가하는 것이 제안된 접근법의 성능을 향상시킬 수 있는지 조사한다.  
  5) 패딩 옵션과 히든 유닛 수를 포함하여 LSTM과 관련된 이슈에 대한 광범위한 평가를 수행하였다. 또한 LSTM과 단순한 회귀 모델인 SVR과 LSTM을 비교하였다.  
  6) [7], [12], [14]에서 제안된 세 개의 기존 접근법을 구현하였으며, 제안된 접근법과 비교하였다.  
  7) 제안된 접근법의 성능을 평가하기 위해 두 개의 데이터셋을 사용하였다.

이후 구성은 다음과 같다.  
섹션 2에서는 제안된 접근법의 아키텍처를 설명한다.  
섹션 3에서 feature 셋의 옵션을 설명하였다.   
섹션 4는 데이터셋과 제안된 접근법의 성능을 평가하기 위한 실험 환경을 설명한다.   
섹션 5는 입력 feature 옵션을 분석한다.  
섹션 6에서는 LSTM 종류에 따른 성능과 세팅을 설명한다.  
섹션 7은 제안된 접근법과 7개의 기존 접근법을 비교한다.  
섹션 8은 제안된 접근법의 일부 discussions을 제시한다.  
섹션 9는 결론을 제시한다.  

---
### Notes

Quality in HAS
* Instantaneous quality  
  스트리밍 세션 중에 순간적으로 인식되는 품질, 품질 변동, stalling event와 같은 impairment 이벤트에 대한 사용자의 즉각적인 반응을 이해하는 데에 유의미함.
* Overall quality  
  스트리밍 세션의 긑에서 측정되는 전체 세션의 품질, 서비스 자체에 대한 품질을 이해하는 데에 유의미함.

Overall quality 평가 방법
* 일반적인 방법  
  - Recommendations ITU-R BT.500-13  
  - ITU-T P.880
* 기존 방법
  - 분석 모델 기반의 접근법으로, 품질 변동의 횟수, 평균, 세그먼트 품질 값의 히스토그램 등 일부 통계값을 사용하여 품질 변동의 영향을 모델링함.  
    stalling event: stalling event의 수와 합, stalling duration 히스토그램 사용이 일반적임.  
    Overall quality 예측을 위해 통계값들을 선형 함수, 로그 함수 등으로 풀링    
  - 고급 머신 러닝 접근법은 적음  
    공적으로 사용 가능한 데이터셋을 사용하나 아래 문제가 있음.
    1) 데이터셋이 작아 과적합될 수 있음.
    2) 20초 이내 짧은 길이를 가지며, 품질 변동 또는 stalling event만을 고려하므로, 실제 스트리밍 시나리오를 반영하지 못함.
  - Overall quality 예측을 위한 최초의 고급 머신러닝 접근법은 [15], 랜덤 신경망을 사용함. 이후에도 몇 가지 접근법이 제안되었으나, SVR이 가장 좋았음.
  - ITU-T P.1203.3 표준 최신 단계에서는 랜덤 포레스트 모델을 사용하는 고급 머신러닝 접근법이 제안 되었음.

대부분의 분석 모델 기반, 고급 머신러닝 기반 접근법은 입력으로 일부 통계값만을 사용하지만, 이들은 **impairment 이벤트들 간 시간적 관계를 놓쳐 실제 스트리밍 세션에서 발생할 수 있는 문제를 반영하지 못함.**  
e.g., 동일한 횟수의 impairment 이벤트, 연속적인 이벤트 - 간헐적으로 발생하는 이벤트 간 영향의 차이  
**Motivation => 스트리밍 세션의 impairment 이벤트 간 시간적 관계를 충분히 반영할 수 있는 접근법 개발이 필수적!**

* 제안된 접근법
  - 세그먼트 기반의 값들을 입력으로 사용함.
  - LSTM을 사용하여 입력에서 시간적 관계를 효과적으로 추출함.
  - 핵심 기여
    1) Overall quality를 예측하기 위해 LSTM을 사용하는 최초의 연구
    2) 기본, 고급 LSTM 두 개의 상이한 LSTM을 조사  
      패딩이 제안된 접근법에 미치는 영향을 조사  
      입력 features를 complex(세그먼트 품질, 커넽ㄴ츠 특성, stalling duraion, 패딩), simple 두 가지 옵션으로 제안, 분석함.
    3) 세 가지 데이터셋을 사용하여 다양하게 평가
  - 이전 연구와의 차이
    - 고급 LSTM을 사용한 성능향상
    - 효율적이고 효과적인 simple option feature 셋 제안
    - 세그먼트 품질 feature 표현에 다섯 가지 메트릭 추가
    - LSTM 입력에 스위칭 주파수 특징을 추가하는 것이 성능 향상에 효과적인지 조사
    - 패딩 옵션, 히든 유닛 수를 포함한 광범위한 평가, LSTM-SVR 비교
    - [7], [12], [14] 세 개의 기존 접근법 구현 및 비교
    - 제안된 접근법 성능 평가를 위해 두 개의 데이터셋 사용

---
## II. Architecture of the Proposed Approach

본 섹션에서는 제안된 접근법의 일반적인 구조를 설명한다. 연구에 사용된 두 타입을 상세히 설명한다.

**A. General Architecture**  

스트리밍 세션은 세그먼트의 연속으로 구성되며, 가각은 features 셋으로 표현된다.

feature들은 LSTM에 입력에 사용된다. 출력은 선형 회귀 모듈을 거쳐 스트리밍 세션의 전반적인 품질을 예측한다.  

본 연구에서는 baLSTM(basic LSTM), adLSTM(advanced LSTM) 두 가지 타입의 LSTM을 사용하였다. 두 서브 섹션에서는 해당 네트워크 타입의 입출력 간 관계를 상세히 설명한다.

**B. Basic LSTM Network (baLSTM)**  
![baLSTM network](https://github.com/user-attachments/assets/704518e6-01da-4735-b2be-7e2fc5382014)

**C. Advanced LSTM Network (adLSTM)**  
![adLSTM Network](https://github.com/user-attachments/assets/26d3d244-c3dc-4b7c-840d-7fd2fa087e95)

Overall quality는 impairment event 간의 시간적 관계에 크게 의존한다 [27]. 따라서 시간적 관계의 학습 능력을 향상시키기 위해 양방향과 어텐션 메커니즘을 적용하였다.

전방향 레이어로만 구성된 baLSTM과 달리, 양방향 LSTM은 그림과 같이 전방 레이어와 후방 레이어 모두 포함한다.

이를 통해 현재 이벤트가 과거 및 미래 이벤트와 관련하여 미치는 영향을 파악할 수 있다. 

어텐션 메커니즘과 관련하여 히든 스테이트의 가중치를 계산하여 어텐션이 높은 주요 이벤트를 결정하는 것이 중요하다.

---
### Notes

adLSTM에서 어텐션 적용한 걸 보니, 막연하지만 바다나우 어텐션 등 어텐션, LLM 쪽 시도해봐도 괜찮을듯

---
## III. Input Features

본 섹션에서는 complex option과 simple option feature set 두 가지의 입력 옵션을 제안한다.

각 옵션에서 features는 stalling duration, padding, quality variation 세 가지 그룹으로 구분할 수 있다.

stalling duration과 padding의 features는 각 옵션에서 동일하다. stalling duration feature를 사용하는 것의 목적은 스트리밍 세션에서 발생하는 stalling event의 영향에 있다.

padding feature는 LSTM 네트워크의 학습 과정에서 모든 세션이 같은 길이를 가질 수 있도록 한다.

반면에 각 옵션에서 quality variation의 영향을 반영하기 위해서는 상이한 두 가지 feature sets을 고려하여야 한다.

특히 complex option은 세그먼트 품질과 컨텐츠 특성의 두 가지 feature가 모두 포함되어 있으며, 이를 얻는 데에 많은 비용이 들 수 있다.

비트스트림 레벨 파라미터에 기반하는 simple option에서의 features는 이들을 쉽게 추출할 수 있다. 

features에 대한 설명은 아래와 같다.

**A. Complex Input Option**

각 세그먼트는 여러 features로 표현된다. 첫 번째 옵션은 세그먼트 퀄리티, 컨텐츠 특성, stalling duration, padding 네 가지 feature로 구성된다.

1) Segment Quality  
  ![Metircs Used to Represent the Segment Quality Feature](https://github.com/user-attachments/assets/d5a26fb9-9177-417e-8d3a-8eb837eed199)
  세그먼트 품질 feature는 다양한 quality 메트릭으로 표현될 수 있다. 본 연구에서는 S-MOS, Bitrate, PSNR, PSNR-HVS, PSNR-HVS-M, SSIM, MS-SSIM, VIF 8가지 메트릭을 고려하였다.  
  이 메트릭 중 S-MOS, PSNR-HVS, PSNR-HVS-M, SSIM, MS-SSIM, VIF 여섯 가지 메트릭만이 인간의 시각 시스템 특성을 고려한다.  
  일부 quality 메트릭은 측정하기 쉬운 반면, S-MOS는 이를 얻는 데에 시간이 매우 많이 든다.

2) Content Characteristics  
  비디오의 품질이 컨텐츠 특성에 영향을 받는다는 것은 잘 알려져 있다.  
  [35]와 유사하게, 제안된 접근법에서는 컨텐츠 특성은 spatial complexity, temporal complexity로 두 가지 차원을 모두 고려하였다.  
  각 세그먼트의 spatial complexity를 표현하기 위해 SV(Spatial Variance) 메트릭을 사용하였다. 이 메트릭은 MPEG-7 edge histogram algorithm으로부터 계산되었다.  
  특수적으로, 세그먼트의 각 프레임은 우선적으로 4x4의 서브 블록으로 나누어졌으며, 이후 각 블록에서 수직, 수평, 45도, 135도, non-direction 다섯 가지 엣지 유형의 히스토그램이 계산되었다.  
  각 세그먼트의 temporal complexity를 표현하기 위해 모션 벡터로부터 계산된 두 가지 메트릭이 사용되었다.  
  첫 번째 메트릭은 MMM(모션 벡터 크기의 평균)이다.  
  두 번째 메트릭은 SMM(모션 벡터 크기의 표준 편차)이다.  
  세 메트릭 모두 독립적이기에 모두 LSTM에 입력하였다.

3) Stalling Duration  
  세그먼트의 stalling duration feature는 사용자가 이전 세그먼트가 끝난 후 현재 세그먼트가 시작될 때까지 기다려야 하는 시간으로 표현된다.  
  현재 세그먼트가 이전 세그먼트의 재생이 끝나기 전에 클라이언트에 도착하면 Stalling Duration은 0으로 설정된다.  
  반면에, stalling event가 발생하면, Stalling Duration은 양수 값이 된다. stalling event는 SD가 0보다 큰 세그먼트의 수이다.

4) Padding  
  스트리밍 세션은 보통 서로 다른 길이를 가진다. 학습에 제로 패딩 기법을 적용하는 것은 세션이 같은 길이를 가지도록 한다.  
  padded segment라 하는 일부 세그먼트가 모든 세션의 시작에 추가되어 가장 긴 세션의 길이와 같은 길이를 가진다.  
  패딩된 세그먼트와 실제 세그먼트를 구분하기 위해, 1과 0으로 불린 변수 PS를 정의했다.  
  모든 padded 세그먼트는 segment quality, 컨텐츠 특성, stalling duration과 같은 features값을 0으로 가진다.  
  제안된 접근법의 성능에 패딩이 미치는 영향을 조사하기 위해 네 가지 상이한 패딩 케이스에 대해 VI-B 섹션에서 평가하였다.  
  ![An example of a streaming session](https://github.com/user-attachments/assets/a9befd37-fe6c-405a-b24e-a3270b09113b)
  그림 6은 스트리밍 세션의 정규화된 feature 값과 해당 대역폭 추적을 나타낸다. 처음 14개 세그먼트가 패딩된 세그먼트(PS=1)임을 알 수 있다.  
  나머지는 실제 세그먼트로, 대역폭 변동으로 인해 스트리밍 세션 중에 세그먼트 품질이 달라진다.  
  일반적으로 세그먼트 품질은 대역폭이 증가함에 따라 향상되며, 반대 역시 마찬가지이다. 이 세션에서는 SD>0인 27, 39, 39번째 세그먼트에서 Stalling event가 발생한다.  
  SV 값이 안정적이기에 이 세션에서는 Spatial Complexity에는 큰 변화가 없다. 한편, MMM, SMM 값을 보아 Temporal Complexity는 크게 변화한다.

**B. Simple Input Option**

실제로 세그먼트 품질과 컨텐츠 특성의 feature는 얻는 데에 시간이 많이 걸리고, 저장하는 데에 자원이 많이 소모되어 일반적으로 많은 비용이 든다.

따라서 더 간단하고 실제로 직접 사용할 수 있는 또 다른 옵션인 Simple Input Option을 추가로 제안한다. 이 옵션은 아래의 features를 포함한다.
* Bitstream-level parameters
* Stalling duration
* Padding  

여기서 Stalling Duration과 Padding features는 complex option과 동일하다. 비트스트림 레벨 파라미터는 양자화 매개변수(QP), Bitrate, 해상도, frame-rate를 포함한다.

QP는 세그먼트 내 모든 프레임의 모든 매크로 블록에 대한 양자화된 파라미터 값의 평균으로 계산된다. 나머지는 세그먼트의 Bitrate, 해상도, frame-rate이다.

이 파라미터들은 매우 기본적이고 비디오 비트스트림으로부터 쉽게 추출할 수 있다.  

이중 Bitrate는 complex option에서도 quality 메트릭으로 선택할 수 있다.

섹션 V에서 최고의 세그먼트 품질 선택과, 상이한 features 중 더 좋은 선택이 무엇인지를 중심으로 각 input 옵션의 성능을 평가하였다.

---
### Notes

Input Features Set option
  - Complex option
    + Stalling Duration  
      이전 세그먼트가 끝난 후 현재 세그먼트가 시작될 때까지 기다려야 하는 시간으로 표현  
      playback deadline을 넘기지 않으면 0, 아니면 해당 시간으로 설정됨. => Stalling Event는 SD가 0보다 큰 세그먼트의 수
    + Padding  
      학습 단계에서 각 세션이 같은 길이를 가지도록하기 위함.  
      padded segment가 모든 세션의 시작에 추가되어 가장 긴 세션의 길이와 같은 길이를 가지도록 함.  
      본 논문에서는 패딩된 세그먼트와 실제 세그먼트의 구분을 위해 1과 0으로 불린 변수 PS를 정의함.  
      패딩된 세그먼트는 다른 features에 대해 0의 값을 가짐.
    + Segment Quality
      - Metric: S-MOS, Bitrate, PSNR, PSNR-HVS, PSNR-HVS-M, SSIM, MS-SSIM, VIF  
        S-MOS, PSNR-HVS, PSNR-HVS-M, SSIM, MS-SSIM, VIF 여섯 가지 메트릭만이 인간의 시각 시스템 특성을 고려    
        S-MOS는 얻기에 시간이 많이 걸림.
    + Content Characteristics
      - Metric  
        Spatial Complexity: SV(Spatial Variance)  
        MPEG-7 edge histogram algorithm, 세그먼트의 각 프레임을 4x4 서브 블록으로 나눈 후 수직, 수평, 45도, 135도, non-direction 다섯 가지 엣지 유형 히스토그램을 계산  
        Temporal Complexity: MMM(Mean Motion vector Magnitude), SMM(Standard Deviation Motion vector Magnitude)
        SV, MMM, SMM 모두 LSTM에 입력으로 사용  
  - Simple option  
    **Motivation: Segment Quality와 Content Characteristics의 feature는 얻는 데에 시간이 많이 걸리고, 저장 자원이 많이 소모됨.**  
    => Simple Input Option 제안
    + Features
      + Stalling Duration
      + Padding
      + Bitstream-level Parameters  
        QP(Quatization Prameters), Bitrate, resolution, frame-rate  
        QP는 세그먼트 내 모든 프레임의 모든 매크로 블록에 대한 양자화된 파라미터 값의 평균으로 계산, 나머지는 세그먼트의 각 값임.  
        비디오 비트스틺으로부터 쉽게 구할 수 있음. Bitrate는 complex option에서 quality 메트릭으로도 선택 가능함.  

---
## IV. Datasets and Experiment Settings
 
본 섹션에서 데이터셋 생성에 대해 설명한다. 이후 제안된 접근법의 학습 파라미터, 입력 feature 예시, 연구에서 사용된 성능 평가 메트릭과 같은 실험 환경을 제시한다.

**A. Dataset**

학습 데이터 부족 문제를 해결하기 위해 본 연구에 사용된 데이터셋은 세 개의 데이터 셋을 결합하였다. 첫번째, 두번째 데이터셋은 [11], [37]의 이전 작업에서 가져왔다. 나머지 데이터셋은 이와 동일한 절차를 사용하여 주관적 테스트를 수행하여 새로 생성하였다.  

세번째 데이터셋에서는 앞선 데이터셋과는 다른 세션 총 144개가, 1분 길이 비디오에서 생성된다. 

세션 생성을 위해 개별 비디오를 1초 길이의 세그먼트로 나누고 frame-rate 24 fps로 H.264/AVC(libx64)를 사용하여 인코딩한다. 이후 각 비디오마다 42개의 수작업 세션과 30개의 실제 스트리밍 세션으로 구성된 72개의 세션이 생성된다.








