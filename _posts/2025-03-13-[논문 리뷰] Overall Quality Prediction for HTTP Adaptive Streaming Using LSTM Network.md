---
title: "[논문 리뷰] Overall quality Prediction for HTTP Adaptive Streaming Using LSTM Network"
published: True
excerpt: "Huyen T. T. Tran, Duc V. Nguyen, Duong D. Nguyen, Nam Pham Ngoc and Truong Cong Thang (IEEE TRANSACTIONS ON CIRCUITS AND SYSTEMS FOR VIDEO TECHNOLOGY • AUGUST 2021)"
---

<!-- TOC -->
* [논문 리뷰를 시작하며](#논문-리뷰를-시작하며)
* [한글 번역 및 코멘트](#한글-번역-및-코멘트)
  * [**Abstract**](#abstract)
    * [Notes](#notes)
  * [**I. Introduction**](#i-introduction)
    * [Notes](#notes-1)
  * [**II. Architecture of the Proposed Approach**](#ii-architecture-of-the-proposed-approach)
    * [Notes](#notes-2)
  * [**III. Input Features**](#iii-input-features)
    * [Notes](#notes-3)
  * [**IV. Datasets and Experiment Settings**](#iv-datasets-and-experiment-settings)
  * [**V. Analysis of the Input Feature Options**](#v-analysis-of-the-input-feature-options)
    * [Notes](#notes-4)
  * [**VI. Analysis of LSTM networks**](#vi-analysis-of-lstm-networks)
    * [Notes](#notes-5)
  * [**VII. Comparison with Existing Approaches**](#vii-comparison-with-existing-approaches)
    * [Notes](#notes-6)
  * [**VIII. Discussions**](#viii-discussions)
    * [Notes](#notes-7)
  * [**IX. Conclusion**](#ix-conclusion)
<!-- TOC -->

---

# 논문 리뷰를 시작하며

오랜만에 블로그를 작성한다. 학부를 졸업하기 전에 논문을 한 편 써보겠다는 것을 목표로 한참을 달리다 보니 그 간의 일들은 블로그에 담지 못했다.  
돌아보면, 남들보다 뛰어나게 성적을 받고, 성과를 내며 학교를 다니지는 못했지만, 목표가 있는 삶을 살아 왔고, 또 그것들을 모두 이루어 보았다는 점에서 스스로 위안해 본다.

이번 논문은 일전에 리뷰하였던 논문의 저자들이 이어서 투고한 논문이다.


**[Overall quality Prediction for HTTP Adaptive Streaming Using LSTM Network](https://ieeexplore.ieee.org/abstract/document/9247981)** - Huyen T. T. Tran, Duc V. Nguyen, Duong D. Nguyen, Nam Pham Ngoc and Truong Cong Thang (IEEE TRANSACTIONS ON CIRCUITS AND SYSTEMS FOR VIDEO TECHNOLOGY • AUGUST 2021)
> 본 게시글은 위 논문을 스스로 번역, 재정리한 내용을 포함합니다.

---

# 한글 번역 및 코멘트
## **Abstract**

HAS는 현대 멀티미디어 전송을 위한 가장 인기 있는 솔루션이다.

하지만 네트워크 대역폭의 변동으로 인해 스트리밍 중에 비디오 품질은 크게 달라진다.

그러므로, HAS에서의 핵심 과제는 스트리밍 세션의 overall quality를 어떻게 평가할지에 있다.

본 논문에서는 스트리밍 세션에서의 각 세그먼트가 set of features로 표현되는 overall quality 예측을 위한 머신 러닝 접근법을 제안한다.

feature set의 두 가지 옵션을 조사하였다. 첫 번째 옵션에서 우리는 **Segment Quality, Content Characteristics, Stalling Duration, Padding**, 네 가지 feature를 사용하였다.

두 번째 옵션에서는 **Bitstream-level parameters, Stalling Duration, Padding**, 세 가지 feature를 사용하였다.

각 옵션에서 features를 Stalling event와 Quality variations의 발생 간의 temporal relations를 탐구할 수 있는 LSTM 네트워크에 입력하였다.

overall quality는 선형 회귀 모듈을 사용하는 LSTM 네트워크의 출력으로부터 예측되었다.

실험 결과에 따르면, 제안된 접근법은 높은 예측 성능을 보였으며, 기존의 일곱 가지 접근법을 능가하였다.

---

### Notes

제안된 접근법
* LSTM using Linear Regression Module

입출력
* input
  * option 1  
    Segment Quality Content Characteristics, Stalling Duration, Padding
  * option 2  
    Bitstream-level parameters, Stalling Duration, Padding
* output
  * overall quality

---

## **I. Introduction**
HAS에서 비디오는 상이한 품질 수준의 여러 버전으로 인코딩 되며, 각 인코딩 버전은 세그먼트 시퀀스로 나뉜다.

세그먼트는 공용 웹 서버에서 호스팅 되는데, 네트워크 상태에 따라서 클라이언트는 적합한 세그먼트의 버전을 결정하고, 웹 서버에 해당 버전을 요청하기 위한 HTTP requests를 전송한다.

선택된 세그먼트 버전은 네트워크 대역폭의 변동에 따라서 스트리밍 세션 중에 크게 변할 수 있으며, 이는 Quality variations를 야기한다.

또한 playback deadline 전에 세그먼트가 도착하지 못한 경우 Stalling event가 발생한다.

HAS에서 핵심 과제는 Quality variations와 Stalling event의 영향을 고려하여 스트리밍 세션의 전체 품질을 평가하는 방법에 있다.

스트리밍 세션의 품질을 측정하는 일반적인 방법으로는 Recommendations ITU-R BT.500-13, ITU-T P.880 두 가지 방법이 있다.

* 순간적인 품질(Instantaneous quality)는 스트리밍 세션 중에 순간적으로 인식되는 품질을 의미한다.
* 전반적인 품질(Overall quality)는 스트리밍 세션의 끝에서 측정되는 전체 세션의 품질을 의미한다.

서비스 프로바이더의 경우 사용자에게 최고의 품질 경험을 제공하기 위해 이 두 가지 품질 모두 측정하는 것이 중요하다.

순간적인 품질의 측정은 impairment 이벤트에 대한 사용자의 즉각적인 반응을 이해하는 데에 유의미하다(i.e., Quality variations, Stalling event).

현재까지 순간적인 품질 평가 및 모델링과 관련된 몇 가지의 연구가 있었다.

한편, 전반적인 품질의 측정은 서비스 프로바이더가 제공하는 서비스의 품질을 이해하는 데에 중요하다. 본 연구에서는, 전반적인 품질 측정에 초점을 두었다.

전반적인 품질을 예측하기 위한 대부분의 기존 접근법들은 분석 모델 기반의 접근법이다.

이러한 접근법들에서 Quality variations의 영향은 Quality variations의 횟수, 평균, 최소값, 세그먼트 품질 값의 히스토그램과 같은 일부 통계값을 사용하여 모델링된다.

Stalling event의 영향에 대해서는 Stalling event의 수와 합, Stalling Duration 히스토그램이 일반적으로 사용된다.

전반적인 품질을 예측하기 위해 이러한 통계값들은 선형 함수와 로그 함수같은 분석 함수를 사용하여 풀링된다.

기존 접근법 중 일부는 Quality variations와 Stalling event의 영향을 함께 고려하였다.

문헌에 따르면, 전반적인 품질 예측을 위한 고급 머신 러닝 접근법은 일부 존재한다.

이는 부분적으로 학습을 위한 충분한 데이터셋이 부족하기 때문이다.

특히, 공적으로 사용 가능한 데이터 셋의 대부분은 두 가지의 주요 문제가 있다.

첫 째, 데이터 셋의 사이즈가 작아 기계 학습 접근법의 학습을 할 때에 오버 피팅을 초래할 것으로 보인다.

둘 째, 기존의 데이터셋은 실제 스트리밍 시나리오를 반영하지 않는데 이는 아래로 인한 결과이다.

1) 데이터셋에 포함된 세션은 일반적으로 20초 이내의 길이로 매우 짧다.  
2) 오직 Quality variations 또는 Stalling event만을 고려한다.

전반적인 품질 예측을 위한 최초의 고급 머신 러닝 접근법은 [15]의 연구이며, 랜덤 신경망을 사용하였다.

해당 접근법의 입력으로는 모든 비디오 프레임의 모든 매크로 블록에 대한 양자화 파라미터 값의 평균, Stalling event 수, Stalling Duration의 평균 및 최대값으로 구성된다.

해당 접근법은 16초 길이의 118개의 스트리밍 세션이 포함된 데이터셋으로 측정하였다.

[16]의 저자는 전반적인 품질을 예측하기 위해 회귀 모델을 사용하는 다른 고급 머신 러닝 접근법을 제안하였다.

그들의 접근법에서 각 세션은 세그먼트 품질 값의 평균, 세그먼트 품질이 감소한 시간, 마지막 impairment 이벤트가 발생한 이후의 시간(ie., Stalling event 또는 세그먼트 품질 저하), Stalling Duration 총합, Stalling event 수 5개의 통계값으로 특정된다.

이를 위해 선형 휘귀, SVR, 앙상블 기법 세 가지의 회귀 모델이 고려된다.

대략 72초 길이의 세션 112개로 구성된 데이터셋을 사용하여 SVR이 최고의 평균 예측 성능을 보였다.

데이터셋의 세션은 8개의 직접 구성한 Quality variations 패턴을 가지며, 각 패턴은 오직 하나의 세그먼트 품질 저하와 최대 두 번의 Stalling event를 가진다.

ITU-T P.1203.3 표준의 최신 단계에서는 스트리밍 세션의 전반적인 품질을 사전에 결정하기 위한 고급 머신 러닝 접근법이 제안되었다.

이 접근법은 세그먼트 품질 값의 첫 번째, 다섯 번째, 열 번째 백분위 수, 지연 기간의 합, Stalling event 등 다양한 통계를 포함하는 랜덤 포레스트 모델을 사용한다.

[22]에 따르면, 이 접근법은 60개의 60초 분량의 긴 세션과 240초 분량의 긴 세션 15개를 사용하여 검증되었다.

대부분의 분석 모델 기반, 고급 머신러닝 기반 접근법을 사용하는 기존 접근법에서는 입력은 위에서 설명한 세션의 일부 통계값만을 다룬다.

그러나 이러한 통계값들은 impairment 이벤트들 간의 시간적 관계를 놓치기에 스트리밍 세션에서 발생할 수 있는 impairment 이벤트를 충분히 반영하지 못한다.

가령, 같은 횟수의 Stalling event, Stalling Durations의 합, 연속적인 Stalling event들이 간헐적으로 발생하는 그것들과 비교하여 야기할 수 있는 부정적인 영향을 반영하지 못한다.

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
  complex 옵션은 Segment Quality, Content Characteristics, Stalling Duration, Padding으로 구성된다.
* 셋 째, 제안된 접근법은 세 가지의 데이터셋을 사용하여 평가되었다.  
  1) 60-76초 길이의 세션 515개, 그 중 332개는 실제 스트리밍 테스트베드에서 얻은 데이터셋  
  2) 1분 길이의 세션 120개로 구성된 데이터셋  
  3) 8초 길이의 세션 588개로 구성된 데이터셋  
  실험 결과에서 제안된 접근법은 매우 높은 성능을 보였으며, 제안된 접근법과 기존 일곱가지 접근법의 예측 성능을 비교하였으며, 제안된 접근법은 가장 좋은 성능을 보였다.

위 작업의 일부는 [25]에 제시되어 있다. 이전 연구와 비교할 때에, 본 연구는 새로운 점을 가진다.  
  1) 고급 LSTM을 사용하여 제안된 접근법의 성능을 향상 시켰다.    
  2) 효율적이고 효과적인 feature 셋의 새로운 옵션(simple option)을 제안하였다.    
  3) Segment Quality feature 표현에 사용되는 다섯 가지 추가 메트릭을 포함하였다.    
  4) LSTM 입력에 스위칭 빈도 feature를 추가하는 것이 제안된 접근법의 성능을 향상시킬 수 있는지 조사한다.  
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
  스트리밍 세션 중에 순간적으로 인식되는 품질, Quality variations, Stalling event와 같은 impairment 이벤트에 대한 사용자의 즉각적인 반응을 이해하는 데에 유의미함.
* Overall quality  
  스트리밍 세션의 긑에서 측정되는 전체 세션의 품질, 서비스 자체에 대한 품질을 이해하는 데에 유의미함.

Overall quality 평가 방법
* 일반적인 방법  
  - Recommendations ITU-R BT.500-13  
  - ITU-T P.880
* 기존 방법
  - 분석 모델 기반의 접근법으로, Quality variations의 횟수, 평균, 세그먼트 품질 값의 히스토그램 등 일부 통계값을 사용하여 Quality variations의 영향을 모델링함.  
    Stalling event: Stalling event의 수와 합, Stalling Duration 히스토그램 사용이 일반적임.  
    Overall quality 예측을 위해 통계값들을 선형 함수, 로그 함수 등으로 풀링    
  - 고급 머신 러닝 접근법은 적음  
    공적으로 사용 가능한 데이터셋을 사용하나 아래 문제가 있음.
    1) 데이터셋이 작아 과적합될 수 있음.
    2) 20초 이내 짧은 길이를 가지며, Quality variations 또는 Stalling event만을 고려하므로, 실제 스트리밍 시나리오를 반영하지 못함.
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
      입력 features를 complex(Segment Quality, Content Characteristics, stalling duraion, Padding), simple 두 가지 옵션으로 제안, 분석함.
    3) 세 가지 데이터셋을 사용하여 다양하게 평가
  - 이전 연구와의 차이
    - 고급 LSTM을 사용한 성능향상
    - 효율적이고 효과적인 simple option feature 셋 제안
    - Segment Quality feature 표현에 다섯 가지 메트릭 추가
    - LSTM 입력에 스위칭 빈도 feature를 추가하는 것이 성능 향상에 효과적인지 조사
    - 패딩 옵션, 히든 유닛 수를 포함한 광범위한 평가, LSTM-SVR 비교
    - [7], [12], [14] 세 개의 기존 접근법 구현 및 비교
    - 제안된 접근법 성능 평가를 위해 두 개의 데이터셋 사용

---
## **II. Architecture of the Proposed Approach**

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
## **III. Input Features**

본 섹션에서는 complex option과 simple option feature set 두 가지의 입력 옵션을 제안한다.

각 옵션에서 features는 Stalling Duration, Padding, quality variations 세 가지 그룹으로 구분할 수 있다.

Stalling Duration과 Padding의 features는 각 옵션에서 동일하다. Stalling Duration feature를 사용하는 것의 목적은 스트리밍 세션에서 발생하는 Stalling event의 영향에 있다.

Padding feature는 LSTM 네트워크의 학습 과정에서 모든 세션이 같은 길이를 가질 수 있도록 한다.

반면에 각 옵션에서 quality variations의 영향을 반영하기 위해서는 상이한 두 가지 feature sets을 고려하여야 한다.

특히 complex option은 Segment Quality와 Content Characteristics의 두 가지 feature가 모두 포함되어 있으며, 이를 얻는 데에 많은 비용이 들 수 있다.

Bitstream-level parameter에 기반하는 simple option에서의 features는 이들을 쉽게 추출할 수 있다. 

features에 대한 설명은 아래와 같다.

**A. Complex Input Option**

각 세그먼트는 여러 features로 표현된다. 첫 번째 옵션은 Segment Quality, Content Characteristics, Stalling Duration, Padding 네 가지 feature로 구성된다.

1) Segment Quality  
  ![Metircs Used to Represent the Segment Quality Feature](https://github.com/user-attachments/assets/d5a26fb9-9177-417e-8d3a-8eb837eed199)
  Segment Quality feature는 다양한 quality 메트릭으로 표현될 수 있다. 본 연구에서는 S-MOS, Bitrate, PSNR, PSNR-HVS, PSNR-HVS-M, SSIM, MS-SSIM, VIF 8가지 메트릭을 고려하였다.  
  이 메트릭 중 S-MOS, PSNR-HVS, PSNR-HVS-M, SSIM, MS-SSIM, VIF 여섯 가지 메트릭만이 인간의 시각 시스템 특성을 고려한다.  
  일부 quality 메트릭은 측정하기 쉬운 반면, S-MOS는 이를 얻는 데에 시간이 매우 많이 든다.

2) Content Characteristics  
  비디오의 품질이 컨텐츠 특성에 영향을 받는다는 것은 잘 알려져 있다.  
  [35]와 유사하게, 제안된 접근법에서는 Content Characteristics은 spatial complexity, temporal complexity로 두 가지 차원을 모두 고려하였다.  
  각 세그먼트의 spatial complexity를 표현하기 위해 SV(Spatial Variance) 메트릭을 사용하였다. 이 메트릭은 MPEG-7 edge histogram algorithm으로부터 계산되었다.  
  특수적으로, 세그먼트의 각 프레임은 우선적으로 4x4의 서브 블록으로 나누어졌으며, 이후 각 블록에서 수직, 수평, 45도, 135도, non-direction 다섯 가지 엣지 유형의 히스토그램이 계산되었다.  
  각 세그먼트의 temporal complexity를 표현하기 위해 모션 벡터로부터 계산된 두 가지 메트릭이 사용되었다.  
  첫 번째 메트릭은 MMM(모션 벡터 크기의 평균)이다.  
  두 번째 메트릭은 SMM(모션 벡터 크기의 표준 편차)이다.  
  세 메트릭 모두 독립적이기에 모두 LSTM에 입력하였다.

3) Stalling Duration  
  세그먼트의 Stalling Duration feature는 사용자가 이전 세그먼트가 끝난 후 현재 세그먼트가 시작될 때까지 기다려야 하는 시간으로 표현된다.  
  현재 세그먼트가 이전 세그먼트의 재생이 끝나기 전에 클라이언트에 도착하면 Stalling Duration은 0으로 설정된다.  
  반면에, Stalling event가 발생하면, Stalling Duration은 양수 값이 된다. Stalling event는 SD가 0보다 큰 세그먼트의 수이다.

4) Padding  
  스트리밍 세션은 보통 서로 다른 길이를 가진다. 학습에 제로 패딩 기법을 적용하는 것은 세션이 같은 길이를 가지도록 한다.  
  padded segment라 하는 일부 세그먼트가 모든 세션의 시작에 추가되어 가장 긴 세션의 길이와 같은 길이를 가진다.  
  패딩된 세그먼트와 실제 세그먼트를 구분하기 위해, 1과 0으로 불린 변수 PS를 정의했다.  
  모든 padded 세그먼트는 Segment Quality, Content Characteristics, Stalling Duration과 같은 features값을 0으로 가진다.  
  제안된 접근법의 성능에 패딩이 미치는 영향을 조사하기 위해 네 가지 상이한 패딩 케이스에 대해 VI-B 섹션에서 평가하였다.  
  ![An example of a streaming session](https://github.com/user-attachments/assets/a9befd37-fe6c-405a-b24e-a3270b09113b)
  그림 6은 스트리밍 세션의 정규화된 feature 값과 해당 대역폭 추적을 나타낸다. 처음 14개 세그먼트가 패딩된 세그먼트(PS=1)임을 알 수 있다.  
  나머지는 실제 세그먼트로, 대역폭 변동으로 인해 스트리밍 세션 중에 세그먼트 품질이 달라진다.  
  일반적으로 세그먼트 품질은 대역폭이 증가함에 따라 향상되며, 반대 역시 마찬가지이다. 이 세션에서는 SD>0인 27, 39, 39번째 세그먼트에서 Stalling event가 발생한다.  
  SV 값이 안정적이기에 이 세션에서는 Spatial Complexity에는 큰 변화가 없다. 한편, MMM, SMM 값을 보아 Temporal Complexity는 크게 변화한다.

**B. Simple Input Option**

실제로 Segment Quality와 Content characteristics feature는 얻는 데에 시간이 많이 걸리고, 저장하는 데에 자원이 많이 소모되어 일반적으로 많은 비용이 든다.

따라서 더 간단하고 실제로 직접 사용할 수 있는 또 다른 옵션인 Simple Input Option을 추가로 제안한다. 이 옵션은 아래의 features를 포함한다.
* Bitstream-level parameters
* Stalling duration
* Padding  

여기서 Stalling Duration과 Padding features는 complex option과 동일하다. Bitstream-level parameter는 양자화 매개변수(QP), Bitrate, 해상도, frame-rate를 포함한다.

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
      playback deadline을 넘기지 않으면 0, 아니면 해당 시간으로 설정됨. => Stalling event는 SD가 0보다 큰 세그먼트의 수
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
## **IV. Datasets and Experiment Settings**
 
본 섹션에서 데이터셋 생성에 대해 설명한다. 이후 제안된 접근법의 학습 파라미터, 입력 feature 예시, 연구에서 사용된 성능 평가 메트릭과 같은 실험 환경을 제시한다.

**A. Dataset**

학습 데이터 부족 문제를 해결하기 위해 본 연구에 사용된 데이터셋은 세 개의 데이터 셋을 결합하였다. 첫번째, 두번째 데이터셋은 [11], [37]의 이전 작업에서 가져왔다. 나머지 데이터셋은 이와 동일한 절차를 사용하여 주관적 테스트를 수행하여 새로 생성하였다.  

세번째 데이터셋에서는 앞선 데이터셋과는 다른 세션 총 144개가, 1분 길이 비디오에서 생성된다. 

세션 생성을 위해 개별 비디오를 1초 길이의 세그먼트로 나누고 frame-rate 24 fps로 H.264/AVC(libx64)를 사용하여 인코딩한다. 이후 각 비디오마다 42개의 수작업 세션과 30개의 실제 스트리밍 세션으로 구성된 72개의 세션이 생성된다.

![Settings of Hand-Crafted Sessions for the Newly Created Dataset](https://github.com/user-attachments/assets/14599068-282f-4e00-a227-cb24ee9f2991)

테이블 2는 수제작된 세션의 설정을 보인다.

Quality variations와 Stalling event가 없는 #1-#5, 10개의 세그먼트와 Stalling event는 없고, 주기적인 Quality variations은 있는 #6-#15, 0.25초, 0.5초, 1초, 2초, 3초, 4초의 Stalling event가 있고 Quality variations은 없는 #16-#42로 구성된다.

이러한 세션 생성을 위해 각 세그먼트는 표 2a에 나와 있는 QP와 해상도를 가진 5가지 버전으로 인코딩 된다. 또한 세션 #16-#42에는 Stalling event가 정기적으로 들어간다.

![Settings of Versions Used to Generate Real Streaming Sessions for the Newly Created Dataset](https://github.com/user-attachments/assets/f797a699-5680-4857-9266-cea6800f17ea)

실제 스트리밍 세션과 관련하여 각 세그먼트는 표3과 같이 서로 다른 QP 값과 해상도에 해당하는 10개의 버전으로 인코딩된다.

세그먼트 버전의 결정을 위해 [38], [39] 두 가지의 adaptation 방법을 사용하였다.

해당 adaptation 방법은 모바일 네트워크로의 대역폭 추적을 사용하여 스트리밍 테스트 베드에서 실행된다.

실제 스트리밍 세션은 Quality variations와 Stalling event로 구성된다.

테스트 조건은 Recommendation ITU-T P.913에 따라 설계되었다. 피험자의 피로를 최소화하기 위해 주관식 테스트는 서로 다른 날에 실시되는 네부분으로 나뉘어 진행되었다.

각 파트의 소요 시간은 약 50분이며, 20분마다 10분의 휴식을 제공하였다. 각 피험자는 최대 두 개의 테스트 파트에 참여한다.

실제 주관식 테스트를 하기 전에 피험자들은 평가 절차와 비디오 품질 점수 범위에 익숙해지도록 교육을 받는다. 세션은 14인치 화면과 검은색 배경에 무작위로 표시된다. 각 세션이 끝나면 피험자는 1점(최악)에서 5점(최고)까지의 점수 범위로 평가를 시행한다.

주관식 시험에는 18세에서 41세 사이의 총 53명의 피험자가 참여하였으며, 테스트의 총 시간은 약 78시간이 소요되었다. 테스트 결과에 대한 분석은 Recommendation ITU-T P.913에 따라 수행되었으며, 두 명의 피험자는 불합격 처리하였다.

거부된 피험자의 점수를 제거한 후, 각 세션은 21명의 유효한 피험자에 의해 평가되었다. 각 세션의 주관적인 Overall quality 값은 유효한 피험자의 평균 점수로 계산하였다.


**B. Training Parameters and Cases of Input Features**

제안된 접근법의 예측 성능을 평가하기 위해서 데이터셋은 412개의 세션으로 구성된 학습 세트와 나머지 103개의 세션으로 구성된 테스트 세트로 랜덤 스플릿하였다. 이 분할을 100회 반복하여 100쌍의 학습 및 테스트 세트가 생성되었다. 다음 섹션에 제시된 결과는 100쌍의 학습 및 테스트 세트에 대한 평균값이다.

학습 과정에서 손실함수는 예측된 품질 값과 주관적 품질 값 사이의 RMSE로 계산하였으며, Adam 옵티마이저 기반의 배치 경사 하강 방식을 사용하였다.

Adam 옵티마이저의 파라미터는 β1 = 0.9, β2 = 0.999, ϵ = 1e-08, 학습률 0.01로 설정하였다. 500-7000까지의 상이한 에포크들을 500 스텝마다 테스트 하였다. LSTM의 히든 유닛은 d ∈ {1,3,5} 세 가지 값으로 설정하였다.

제안된 접근법에서 features의 역할을 조사하기 위해 첫 번째 옵션인 Complex option에서의 입력 features를 네 가지 경우로 고려하였다.

1. Full  
   Segment Quality, Content Chracteristics, Stalling Duration, Padding
2. w/oCC  
   Segment Quality, Stalling Duration, Padding
3. w/oSQ  
   Content Chracteristics, Stalling Duration, Padding
4. w/oSD  
   Segment Quality, Content Chracteristics, Padding

이전 [21]의 연구에서는 스위칭 빈도, 즉 세그먼트 품질 스위칭의 수를 조사하여 스트리밍 세션의 Overall quality를 예측하는 데에 사용하였다.

본 조사에서는 complex 옵션의 네 가지 features 외 스위칭 빈도 feature를 추가적으로 고려하는 확장 옵션 exi1(Extended Complex Option)을 조사하였다.

세그먼트의 스위칭 빈도 feature는 세션 시작 이후 현재 세그먼트까지 누적된 세그먼트 품질 스위치 횟수로 표시된다.


**C. Evaluation Metrics**

성능 평가 메트릭으로는 100개의 테스트 세트에 대해 평균을 낸 PCC(Pearson Correlation Coefficient), RMSE(Root Mean Square Error), SROCC(Spearman rank-order correlation coefficient)를 사용한다.

PCC, RMSE, SROCC는 각각 접근법에서 예측된 Overall quality 값과 주관적 테스트에서 얻은 주관적 Overall quality 값 간의 선형 관계, 차이 및 순위 상관관계를 측정하는 데에 사용된다.

PCC가 높을 수록, RMSE가 낮을 수록, SROCC가 높을 수록 예측 성능이 더 좋음을 의미한다. 표 IV,V,VI,VII에서 PCC(↑), RMSE(↓), SROCC(↑)로 표기되어 있습니다.

---

## **V. Analysis of the Input Feature Options**

**A. Performance of the Complex Option I1**

본 서브섹션에서는 complex option I1에서 features, 특히 Content Characteristics 및 가장 좋은 Segment Quality 측정 메트릭의 이점이 미치는 영향을 조사할 예정이다.

이를 위해 앞선 네 가지 입력 feature 케이스를 평가한다. 여기서는 adLSTM을 사용하고 에포크는 500~7000, 히든 유닛은 5로 설정한다.

![PCC values averaged over the 100 sets for the Full case using the diffrent quality metrics of the option I1 and the adLSTM network](https://github.com/user-attachments/assets/15a02c4a-dd2f-425c-93a7-ffe8fd8fbb86)

그림 9는 여러 품질 메트릭을 사용하여 Full 케이스에서 100개의 테스트 세트에 대해 평균을 낸 PCC 값을 보인다. S-MOS가 일관되게 가장 높은 PCC 값을 나타내는 것을 볼 수 있다. 이는 S-MOS가 Segment Quality feature를 나타내는 가장 좋은 지표임을 보인다.

PSNR은 단순한 측정 항목임에도, PCC 값은 S-MOS보다 약간 낮다는 것이다. 특히 가장 높은 PCC 값을 보인 PSNR의 경우 0.942(epochs=4000), S-MOS의 경우 0.966(epochs=1500)이었다.

또한 PSNR-variants 및 VIF에 해당하는 PCC 값도 S-MOS에 비해 약간 낮았다. 따라서 Content Characteristics이 포함된 경우 Segment Quality feature를 표현하기 위해 S-MOS 대신 PSNR, PSNR-variant, VIF를 사용하여도 무방하다.

![Best performance of the proposed approach using the adLSTM network for the Full and w/oCC cases of the option I1 over the test sets](https://github.com/user-attachments/assets/c6417e22-f64f-4bf0-8c6a-150d13de5259)

그림 11은 서로 다른 Segment Quality 메트릭을 사용할 때 테스트 세트에 대한 Full 케이스, w/oCC 케이스의 최고 성능을 비교한 것이다. 

S-MOS를 제외한 모든 지표에서 Full 케이스가 w/oCC 케이스에 비해 훨씬 더 높은 성능을 보이는 것을 확인할 수 있다. 가장 큰 성능 차이는 PSNR과 VIF에서 보였다.

한편, S-MOS를 사용할 경우에 Full 케이스와 w/oCC 케이스의 성능이 비슷한데, 이는 Segment Quality feature를 표현하기 위해 S-MOS를 사용할 때에는 Content Characteristics feature를 추가로 사용해도 큰 개선이 없음을 의미한다. 반면, BR, PSNR, PSNR-variant, SSIM, MS-SSIM, VIF 메트릭에서는 Content Characteristics을 고려하는 것이 유리하다.

PSNR, PSNR-HVS, PSNR-HVS-M 세 가지 메트릭 중 w/oCC 케이스와 비교한 Full 케이스에서의 이득은 PSNR에서 가장 크고 PSNR-HVS-M이 가장 작았다. 이는 인간의 시각 시스템을 추가로 고려하는 PSNR-variants가 기존 PSNR보다 Content Characteristics에 덜 의존한다는 것을 나타낸다. SSIM과 그 변형인 MS-SSIM에서도 비슷한 결론을 찾을 수 있었다.


![Best Performance of the Proposed Approach for the Difference Input Cases of Full, w/oSD, w/oSQ, and exI1 Using S-MOS and the adLSTM Network for the Test Sets.png](https://github.com/user-attachments/assets/8272b2c7-5601-43a8-93c2-1bd958722a07)

표 4는 Full, w/oSD, w/oSQ 및 exI1의 다양한 입력 케이스에 대한 제안된 접근법의 최고 성능을 보여준다.

Stalling Duration feature와 Segment Quality feature를 입력에서 제외하면 제안한 접근법의 성능이 크게 저하되는 것을 볼 수 있다.

특히 Stalling Duration feature를 고려하지 않은 케이스(w/oSD)에서는 PCC와 SROCC 값이 0.966에서 0.838, 0.965에서 0.820으로 떨어지는 반면 RMSE값은 0.248에서 0.518로 증가하는 것을 볼 수 있다. 이는 Stalling event가 세션의 Overall quality에 상당한 영향을 미친다는 것을 나타낸다.

w/oSD의 경우와 비교하여 w/oSQ의 경우 PCC 값은 훨씬 낮고(0.734-0.518), SROCC 값은 현저히 낮다(0.584-0.820). 이는 제안된 접근법에서 Segment Quality feature가 Stalling Duration feature보다 더 중요한 역할을 한다는 것을 의미한다.


또한 스위칭 빈도 feature를 추가하면 제안한 접근법의 성능을 향상시킬 수 있는지 여부를 조사한다. 표 4에서 실제로 스위칭 빈도 feature가 큰 성능 향상을 가져올 수 없음을 보였다. 심지어 성능이 약소하게 저하되는 결과를 보였다.

특히 PCC, RMSE, SROCC 값은 Full 케이스에서 각각 0.966, 0.248, 0.965이고, exI1 옵션의 경우 0.963, 0.257, 0.963이다. 이는 스위칭 빈도 feature가 스위칭의 폭이 미치는 영향을 구분할 수 없다는 사실로 설명할 수 있다[37]. 따라서 스위칭 빈도가 Overall quality에 미치는 영향이 무시할 수 있는 수준이며, 이는 [42]의 결과와 일치한다.

논문의 이후 부분에서는 complex option I1에 최고의 성능을 보인 S-MOS를 사용하였다.


**B. Comparison of Input Options**

본 하위 섹션에서는 Simple option(I2), Complex option(I1), I1과 I2를 조합한 옵션의 성능 비교를 보인다.

![Prediction performance of the proposed approach using the simple, the complex, and the combination options](https://github.com/user-attachments/assets/b0ca28f9-7abb-484f-bde8-38c15738aef3)

그림 10은 각 옵션에 대해 adLSTM을 사용하여 얻은 결과이다.

Complex 및 조합 옵션에서는 Full 케이스와 S-MOS 품질 메트릭이 사용된다. 그림 10에서 complex 옵션과 조합 오션의 성능이 테스트 세트에서 동일하다는 것을 알 수 있다.

따라서 simple 옵션의 feature를 추가로 사용한다고 하여 성능이 크게 향상되지는 않는다. 즉, I1, I2 옵션의 조합은 중복됨을 의미한다.

또한 simple 옵션 I2의 성능이 매우 높게 나타난 것도 눈여겨 볼만 하다.

특히 에포크 수가 3500개일 때 PCC=0.963, RMSE=0.259, SROCC=0.963으로 최고 성능에 도달하는 것을 확인할 수 있다. 에포크 수가 3500개 이상으로 증가하면 학습 성능은 약소하게 증가하지만 테스트 성능도 감소한다.

1500개 이상의 높은 에포크에서는 simple 옵션과 복합 옵션의 성능이 매우 비슷하게 나타났다. 구체적으로 에포크 수가 1500개로 최고 성능 부근일 때 복합 옵션은 PCC, RMSE, SROCC에서 각 0.005, 0.017, 0.004의 이득을 보였다.

simple 옵션이 높은 성능을 보이면서도 복잡성에 있어 유리하기에 simple 옵션 I2가 효율적이고 효과적이라 할 수 있다.

또한 이는 Bitstream-level parameter가 Segment Quality와 Content Characteristics을 대체할 수 있음을 시사한다.


입력 옵션에 대한 심층적인 이해를 위해 LSTM 대신 단순 회귀 모델인 SVR을 사용하여 비교를 수행하였다.

결과는 표 5에서 확인할 수 있으며, 비슷한 결론을 도출할 수 있었다. 특히, complex 옵션 I1(Full case)와 조합 옵션의 성능이 비슷하고, 둘 다 simple 옵션 I2보다 약간의 높은 성능만을 보였다.

또한 w/oSQ, w/oSD의 경우 Full 케이스에 비해 성능이 상당히 낮게 나타난 것에서 Segment Quality와 Stalling Duration feature의 중요한 역할이 다시 한 번 확인할 수 있었다.

입력 옵션에 대한 자세한 이야기는 섹션 VIII에서 다룬다.

---
### Notes


**A. Performance of the Complex Option I1**

Full 케이스에서 100개의 테스트 세트에 대한 PCC 값 비교에서 S-MOS가 가장 좋은 결과를 보임으로, S-MOS가 Segment Quality feature를 가장 잘 나타내는 지표임

PSNR은 단순하지만 S-MOS 다음으로 좋음.

w/oCC가 아닌 Content Characteristics를 포함하는 경우에는 S-MOS 대신 PSNR, PSNR-variant, VIF를 사용해도 좋음.

S-MOS를 제외한 모든 메트릭에서 Full이 w/oCC보다 좋은 성능을 보였음. PSNR, VIF에서 가장 크게 나타남.

S-MOS를 사용하는 경우 Full과 w/oCC가 비슷한 성능을 보임. 즉, S-MOS에는 Content Characteristics를 반영하는 게 그닥 의미가 없음. 
BR, PSNR, PSNR-variant, SSIM, MS-SSIM, VIF 메트릭 쓸 때는 반영하는 것이 좋음.

인간의 시각 시스템을 고려하는 메트릭인 PSNR-variant, MS-SSIM이 그 원형보다 Content Characteristics에 덜 영향을 받음. "애초에 사람의 눈은 컨텐츠 특성을 고려하니까"

w/oSD-Full 비교를 통해서 Stalling event가 Overall quality에 미치는 영향이 큼을 알 수 있음.

w/oSD-w/oSQ 비교를 통해서 Segment Quality가 Stalling Duration보다 더 중요함을 알 수 있음.

스위칭 빈도 feature를 추가하여도 단순 발생 횟수 비교이기 때문에 스위칭의 폭을 반영하지 못해[37] 성능은 저하됨. 
스위칭의 빈도가 Overall quality에 미치는 영향은 미미하며, 이전 연구 [42]의 결과와도 일치함.


**B. Comparison of Input Options**

I1, I2, I1+I2 옵션 비교를 통해서 simple 옵션인 I2가 성능과 복잡성 측면에서 효율적임을 보였음. 
**이는 비트스트림 레벨의 파라미터가 Segment Quality와 Content Characteristics를 대체할 수 있음을 의미함.**

---

## **VI. Analysis of LSTM networks**

본 섹션에서는 입력 feature 옵션을 다르게 사용하는 두 종류의 LSTM의 성능을 평가한다. 또한, Padding manner, 에포크 수, 히든 유닛 수와 같은 LSTM 자체의 고려사항도 조사한다.


**A. Evaluation of baLSTM and adLSTM Networks**

본 하위 섹션에서는 complex(Segment Quality 메트릭으로 S-MOS를 사용, Full, w/oCC, w/oSD, w/oSQ 케이스 사용), simple, 조합 옵션으로 두 LSTM 타입과 baseline regression model(SVR)을 비교햐여 설명한다.

히든 유닛의 수는 5로 설정된다. 섹션 5와 같이 에포크 수는 500~7000일 때 가장 좋은 성능을 보였는데, 이는 LSTM 타입뿐만 아니라 feature set 옵션에 따라 최적의 에포크 수가 달라질 수 있기 때문이다.

![Best Performance of the Propsed Approach Using the adLSTM, baLSTM Networks, and SVR Model](https://github.com/user-attachments/assets/c66d648d-5432-4ff1-a875-2e84a51c7293)

표 5는 각 LSTM 타입과 SVR 모델에 대해 얻은 결과를 보인다.

동일한 입력 옵션에서 두 LSTM 모두 SVR 모델보다 훨씬 더 높은 성능을 달성한다는 것을 알 수 있다. 

PCC, RMSE, SROCC의 최대 이득은 adLSTM의 경우 각각 0.201, 0.333, 0.164, baLSTM의 경우 0.248, 0.392, 0.179

이 결과는 Overall quality를 예측하기 위해 세그먼트 feature를 풀링할 때 LSTM이 SVR 모델보다 훨씬 더 효과적이라는 것을 의미한다.


baLSTM과 비교했을 떄, adLSTM의 성능은 일반적으로 동등하거나 더 높다. 특히 Full, w/oCC 케이스가 포함된 complex, simple, 조합 옵션의 경우 그 차이가 작다.

하지만 w/oSD, w/oSQ 케이스의 complex 옵션에서는 그 차이가 컸다. adLSTM 네트워크의 이득은 baLSTM 네트워크와 비교하여 PCC의 경우 최대 0.029, RMSE의 경우 0.017, SROCC의 경우 0.033이었다.

그러나 adLSTM은 후방 레이어를 추가로 포함하기 때문에 파라미터 수가 baLSTM보다 약 2배 더 많으며, 특히 학습 과정에서의 학습 파라미터 수는 baLSTM 네트워크의 경우 d(4M + 4d + 5) + 1, adLSTM 네트워크의 경우 d(8M + 8d + 9) + 1이다.


따라서 입력 옵션에 무관하게 최고의 성능을 얻기 위해서는 adLSTM을 사용해야 한다. 한편, 간단한 계산의 경우 complex 옵션의 Full 케이스, 또는 simple 옵션으로 baLSTM을 사용할 수 있다.

LSTM 타입에 따른 자세한 설명은 섹션 8에서 다룰 예정이며, 아래에서는 기본적으로 adLSTM을 기반으로 분석한다.


**B. Impact of Padding**

네 가지의 패딩 케이스로 제안된 접근법의 성능을 조사한다. 
1) prePadd는 섹션 3-A.4에 제시된대로 모든 세션의 시작 부분에 패딩된 세그먼트를 추가한다.
2) postPadd에서는 추가된 세그먼트가 모든 세션의 끝에 추가된다.
3) addPadd10는 가장 긴 세션의 길이에 10개의 세그먼트를 더한 길이와 같을 때까지 모든 세션의 시작 부분에 추가
4) addPadd30은 30개의 세그먼트를 추가하며 과정은 3)과 같음.


테스트 세트의 여러 패딩 케이스에 해당하는 최고 성능은 표 6에 나와 있으며, 히든 유닛은 5로 설정되어 있다. 패딩 케이스 간 성능 차이는 미미한 수준이었다. 즉, 패딩된 세그먼트와 그 위치는 제안된 접근법의 성능에 큰 영향을 미치지 않는다.


**C. Impacts of Numbers of Epochs and Hidden Units**

![Prediction performance of the proposed approach at different numbers of epochs and numbers of hidden units using the option I1(with the Full case) and the option I2](https://github.com/user-attachments/assets/49a637a8-caf9-47e3-ba6a-9b5fbb741ee9)

에포크 수와 히든 유닛 수가 제안한 접근법의 성능에 미치는 영향을 조사한다. 그림 12는 옵션 I1(Full)과 옵션 I2를 사용하여 다양한 에포크 수와 히든 유닛 수에 따른 PCC, RMSE, SROCC의 값을 보인다.

일반적으로 에포크 수가 증가할 수록 PCC, SROCC 값은 빠르게 증가하고, RMSE는 급격히 감소하였다. 에포크 수가 더 증가하면 이 값들은 안정화되었으며, 히든 유닛의 수가 많을 수록 빠르게 안정화되었다.

특히 옵션 I1의 경우 Full 케이스에서 e ≥ 2500(d= 1), e ≥ 1500(d= 3), e ≥ 1500(d= 5) 성능이 거의 변하지 않았다. 옵션 I2의 경우 e ≥ 3000(d= 1), e ≥ 2500(d= 3), e ≥ 1500(d= 5)일 때 안정적인 상태를 나타냈다.


옵션 I1의 경우 e ≥ 3500, 옵션 I2의 경우 e ≥ 5000이 되면 테스트 성능이 점차 감소했다. 이는 학습 과정이 길어지면서 과적합되기 때문이다. 이 결과는 제안된 접근법을 학습할 때 d= 5, e ∈[1500, 3500]으로 설정하면 두 옵션 모두에서 안정적이고 우수한 성능을 제공한다는 것을 시사한다.

---
### Notes

baLSTM보다 adLSTM이 더 효과적이었으며, w/oSD, w/oSQ 케이스의 complex 옵션에서 차이가 컸음. >> 입력 옵션 무관하게 할 거면 adLSTM 쓰는 게 좋음. 

하지만, adLSTM은 후방 레이어의 존재로 인해 파라미터 수가 baLSTM보다 2배 가량 많음. >> 간단한 문제에서는 complex 옵션의 Full, simple 옵션으로 baLSTM 성능도 쓸만함.


패딩은 넣기만 하면 위치나 개수 등은 큰 성능 차를 만들지 않음.

에포크 수를 높일 수록 메트릭 값들이 빠르게 높아지고 안정화 됨. 하지만 과적합을 고려해야 함.

제안된 접근법 학습 시에는 d= 5, e ∈[1500, 3500]가 베스트.

---
## **VII. Comparison with Existing Approaches**

본 섹션에서는 제안한 접근법의 성능을 기존 7가지 접근법 Guo's [12], Liu's [14], Tran's [11], Singh's [15], ATLAS [16], P.1203.3 [21], Eswara's [7]와 비교한다.

참조한 접근법 중 처음 세 가지(Guo's, Liu's, Tran's)는 분석 모델 기반 접근법이다. 나머지(Singh's, ATLAS, P.1203.3, Eswara's)는 고급 머신 러닝 접근법이다.

Guo's에서는 Quality variations의 영향만 고려하는 반면 나머지 접근법은 Quality variations와 Stalling event 모두 고려한다.

본 논문에서는 [18], [44]와 유사하게 각 논문을 기반으로 Guo's, Liu's, Tran's, Singh's를 구현하였다. 이는 각 구현이 공개적으로 사용 가능하지 않기 때문이다. ATLAS [[7](http://live.ece.utexas.edu/research/quality/VideoATLAS_release.zip)]와 Eswara's [[16](https://github.com/lfovia/lstm_qoe)]의 경우 원저자가 공개한 구현을 사용하였다.

Overall quality 값을 얻기 위해 Eswara의 접근법은 Instantaneous quality 값의 mean pooling 전략을 사용한다 [7].

P.1203.3 접근법의 경우, 연구 목적으로 자유롭게 사용할 수 있는 [표준 구현](https://github.com/itu-p1203/itu-p1203/)을 사용하였다 [22], [45].


Guo’s, Liu’s, Tran’s, P.1203.3, Eswara’s 접근법의 파라미터는 재학습 없이 각 논문에 명시된 값으로 설정되었다. 따라서 이러한 접근법의 성능을 평가하기 전에 ITU-T P.1401 [46], Recommendation ITU-T P.1203 [21]에 따라 1차 선형 회귀를 사용하여 각 접근법에 대해 주관적인 테스트 조건의 차이에 대한 보정을 수행한다. 기울기 및 절편은 표 7에 기되어 있다.

제안된 접근법에서는 baLSTM, adLSTM이 simple, complex 옵션과 함께 사용된다. 에포크 수와 히든 유닛 수는 각각 1500, 5로 설정되었다.

고급 머신 러닝 접근법인 Singh's와 ATLAS의 성능을 평가하기 전에 제안된 접근법과 동일한 방식으로 파라미터를 재학습한다.


접근법들의 성능을 비교하기 위해 세 가지 데이터 세트를 사용한다. 첫 번째 데이터 세트는 하위 섹션 4-B에 기술한 100개의 테스트 세트에 대한 PCC, RMSE, SROCC 값의 평균을 구한 데이터 세트이다.

표 7의 두 번째, 세 번째, 네 번째 열은 데이터 세트를 사용하여 얻은 결과를 보인다.


두 번째는 TR04라는 학습 세트와 VL04라는 테스트 세트로 구성되며, 이는 [ITU-T P.1203 standardization(P.NATS)](https://github.com/itu-p1203/open-dataset)에서 공개된 것이다 [22].

각 세트는 1분 길이의 비디오 세 개에서 생성된 60개의 세션으로 구성된다. Singh's 접근법과 제안된 접근법과 관련하여, 데이터 세트의 515개 세션과 TR04 세트의 60개 세션을 모두 사용하여 데이터 분할의 의존성을 피하기 위해 파라미터를 재학습 한다.

또한 가중치 초기화의 영향을 고려하여 학습 과정을 100회 반복한다. 따라서 성능은 테스트 세트 VL04에 대해 평균화된다.

결과는 표 7의 7, 8, 9 열에 나와 있다. 입력 데이터가 부족하기 때문에 이 데이터 세트에 대해서는 Liu's, ATLAS, Eswara의 세 가지 접근법을 평가하지 않았다. 

또한 Content Characteristics feature는 공개적으로 사용할 수 없기 때문에 Full 케이스 대신 w/oCC 케이스가 포함된 옵션 I1을 평가한다.


최종 데이터 세트는 WaterlooSQoE-II이며, 8초 길이의 소스 비디오 12개에서 생성된 4초 길이의 세그먼트 588개와 다양한 화질, 해상도, frame-rate의 Quality variations 패턴이 포함된 588개의 세션을 포함한다.

접근법의 성능을 평가하기 위해 이 데이터 세트를 사용한 두 가지 케이스를 고려한다.

첫 번째 케이스(WaterlooSQoE-II-Case#1)에서는 학습 세트로 본 논문의 데이터 세트를 사용하였고, 테스트 세트로 전체 WaterlooSQoE-II 데이터 세트를 사용하였다.

두 번째 케이스(WaterlooSQoE-II-Case#2)에서는 데이터 세트 외에 학습 세트에는 WaterlooSQoE II 데이터 세트의 12개 소스 비디오 중 3개에서 생성된 147개 세션이 추가로 포함된다.

다른 비디오의 나머지 441개 세션은 해당 테스트 세트를 구성한다.

세 개의 학습 비디오의 선택을 100회 반복하여 100개의 서로 다른 쌍의 학습 및 테스트 세트를 생성하였다.

위의 두 데이터 세트를 사용한 평가와 마찬가지로, 100회의 서로 다른 테스트 횟수에 대해 평균 PCC, RMSE, SROCC 값을 계산하여 표 7에 나타냈다.

학습 세트에도 포함된 데이터 세트는 1초 길이의 세그먼트를 사용하여 구성하였으므로, WaterlooSQoE-II 데이터 세트의 각 4초 길이 세그먼트는 실험에서 1초 길이의 세그먼트 4개로 간주하였다.


표 7을 통해 네 가지 데이터셋/케이스 중 WaterlooSQoE-II-Case#1을 제외한 세 가지에 대해 옵션 I1이 포함된 adLSTM 네트워크를 사용하는 제안된 접근법이 항상 다른 접근법에 비해 가장 높은 성능을 달성한다는 것을 알 수 있다.

WaterlooSQoE-II-Case#1에서는 가장 높은 PCC와 가장 낮은 RMSE를 얻을 수 있었다.

또한 SROCC 값은 Tran's로 얻은 가장 높은 값보다 약간 낮았다. 따라서 본 논문의 접근법이 참조한 일곱 가지 접근법 모두를 능가함을 알 수 있다.


P.1203 데이터셋과 WaterlooSQoE-II-Case#1 케이스에서 동일한 입력 옵션을 사용할 경우 baLSTM의 성능은 adLSTM과 비교하였을 때 낮았다.

기존 접근법과 비교하여 WaterlooSQoE-II-Case#1을 제외한 대부분의 데이터셋/케이스에서 옵션 I1을 사용할 때 baLSTM의 성능은 상당히 높다.

옵션 I2의 경우 본 논문의 데이터셋에 대한 성능도 상당히 높지만, P.1203 데이터셋의 경우 P.1203.3 접근법보다는 약간 낮았다.

WaterlooSQoE-II-Case#1의 경우, 옵션 I2를 사용하면 매우 낮은 성능을 보였는데, 이는 본 논문의 학습 세트에 포함되지 않은 frame-rate의 Quality variations가 테스트 세트인 WaterlooSQoE-II 데이터 세트에 포함되기 때문이다.

그러나 WaterlooSQoE-II-Case#2에서는 다양한 frame-rate의 스트리밍 세션이 학습 세트에 추가될 때 상당한 성능 향상을 보였다.

옵션 I1을 사용하는 baLSTM은 WaterlooSQoE-II-Case#1을 제외한 대부분의 데이터셋/케이스에서 상당히 높은 성능을 달성하기 때문에 제안된 접근법에서도 baLSTM을 사용할 수 있다.


본 논문의 접근법과 Eswara's 접근법 모두 LSTM을 사용하지만, 제안된 접근법은 옵션 I1과 baLSTM, adLSTM을 사용하여 훨씬 높은 성능을 보였다. 이는 Eswara's가 실제로 Instantaneous quality를 예측하기 위해 제안되어 Overall quality는 예측하지 못할 수 있기 때문이다.

또한 mean pooling 전략은 Instantaneous quality를 Overall quality 값으로 집계하는 데에 효과적이지 않을 수 있다.


또한 Tran's와 P.1203.3의 성능이 상당히 우수함을 알 수 있었다. 이 결과는 이러한 접근법도 좋은 성능을 볼 수 있음을 의미한다.


또 Tran's가 Singh's, ATLAS보다 높은 성능을 보인 것에서 일반적으로 분석 모델 기반의 접근법이 고급 머신 러닝 접근법보다 예측 성능이 낮음을 알 수 있다.

이는 Quality variations와 Stalling event의 영향을 정량화하기 위해 Tran's에 사용된 품질 스위치와 Stalling Duration의 히스토그램이 Singh's, ATLAS에 사용된 통계보다 효과적임을 의미한다.

---
### Notes

기존 7가지 접근법과의 비교
제안된 접근법
  * baLSTM, adLSTM이 simple, complex 옵션 모두 활용, e=1500, d=5.

분석 모델 기반
  * Guo's [12] (논문 바탕으로 직접 구현)   
    유일하게 Stalling event는 함께 고려하지 않고 Quality variations의 영향만 고려함
  * Liu's [14] (논문 바탕으로 직접 구현)
  * Tran's [11] (논문 바탕으로 직접 구현)  
    Singh's, ATLAS보다 성능 좋음. 역시 고급 머신 러닝 접근법이 더 좋음.

고급 머신러닝 기반
  * Singh's [15] (논문 바탕으로 직접 구현)
  * ATLAS [16] ([오픈 소스 활용](http://live.ece.utexas.edu/research/quality/VideoATLAS_release.zip))
  * P.1203.3 [21] ([오픈 소스 활용](https://github.com/itu-p1203/itu-p1203/))  
    성능 좋음.
  * Eswara's [7] ([오픈 소스 활용](https://github.com/lfovia/lstm_qoe))    
    Overall quality 값을 얻기 위해 Instantaneous quality 값의 mean pooling 전략을 사용함.  
    Instantaneous quality를 예측하기 위해 제안 되어서 Overall quality는 예측하지 못할 수 있어 성능 낮았음..
    mean pooling 자체가 Overall quality 값 얻는 데에는 부적합할 수도 있음.

실험 이전에 ITU-T P.1401 [46], Recommendation ITU-T P.1203 [21]에 따라 1차 선형 회귀를 사용하여 테스트 조건에 맞추어 보정하였음. 표 7에 기울기 및 절편 기술함.  
고급 머신 러닝 접근법인 Singh's, ATLAS와 비교하기 전에 제안된 접근법이랑 동일하게 파라미터 재학습했음. => 좋은듯

WaterlooSQoE-II-Case#1를 제외하고는 대부분의 상황에서 complex 옵션 사용하였을 때 baLSTM이 효과적이었음.

simple 옵션 사용할 때에는 매우 낮은 성능을 보였음. => WaterlooSQoE-II 데이터 세트에 있는 frame-rate의 Quality variations가 본 논문의 학습 세트에 없기 때문임.

WaterlooSQoE-II-Case#2에서는 frame-rate 스트리밍 세션을 학습 세트에 추가하면 상당한 성능을 보임.

=> complex 옵션 사용 시에는 baLSTM 사용하면 웬만하면 다 좋음. 

---
## **VIII. Discussions**

위의 실험 결과에서 세그먼트, LSTM에서 수집한 feature를 입력으로 사용하면 HAS 세션의 Overall quality를 예측하는 데에 매우 효과적임이 분명히 알 수 있다.

**옵션 I1에서 고려한 feature 중 Segment Quality feature의 역할이 가장 중요하며, 다음으로 Stalling Duration feature의 역할이 중요하다. Content Characteristics의 역할은 Segment Quality feature를 나타내는 데에 사용되는 메트릭에 따라 달라진다.**


Segment Quality feature를 나타내기 위해 S-MOS가 가장 적합한 지표로 나타났다. 또한 PSNR 간단하지만 좋은 성능을 보였다(complex input option, Full case).

Content Characteristics의 활용이 본 연구에서 새로운 부분이며, Segment Quality feature에 S-MOS 메트릭을 사용할 때 Content Characteristics feature를 추가로 사용하여도 예측 성능은 크게 향상되지 않는다.

하지만, BR, PSNR, PSNR-variants, SSIM, MS-SSIM, VIF 메트릭의 경우 Content Characteristics feature를 포함하는 것이 유리했다.

따라서 S-MOS 메트릭에 이미 Content Characteristics가 반영되어 있다.


또 다른 이슈로는 Content Characteristics를 포함하지 않는 simple input option의 성능이 좋게 나타났다는 것이다.

이는 QP, 해상도, frame-rate의 특정 값이 주어지면 Content Characteristics가 더 복잡한 동영상은 일반적으로 비트레이트가 높아지기 때문이다.

즉, simple input option에 사용되는 Bitstream-level parameters, Stalling, Padding으로 구성된 simple option I2는 효율적이면서도 효과적이다.

향후 연구에서는 수동으로 선택하는 feature 대신 LSTM에 효과적인 input feature를 자동으로 얻기 위한 딥러닝 접근 방식에 초점을 맞출 것이다.


LSTM의 타입의 경우 adLSTM을 사용하면 baLSTM에 비해 더 높은 성능을 얻을 수 있다. adLSTM의 경우 d=5, e ∈ [1500,3500]일 때 가장 좋고 안정적인 성능을 보였다.

그러나 두 모델의 성능은 상당히 비슷하게 나타났다. 또한 baLSTM의 파라미터 수는 adLSTM의 그것에 비해 절반 수준에 불과했다. 특히 baLSTM의 핵심적인 장점은 현재와 과거 세그먼트만 사용하여 처리할 수 있다는 점이다.

이를 통해 세션이 끝날 때까지 기다릴 필요 없이 진행 중인 세션의 품질을 지속적으로 예측할 수 있다.


또한 LSTM은 temporal dimension의 모든 변화를 고려하는 데에 도움이 될 수 있으므로, 스위칭 주파수를 feature로 추가해도 성능이 향상되지 않으며, 이는 제안된 접근법이 기존 접근법을 능가하는 주요 이유일 수 있다.

이 연구에서 사용된 데이터셋에는 H.264/AVC를 사용하여 인코딩된 1초길이의 세그먼트와 4초 길이의 세그먼트가 포함된 약 1분 길이의 세션이 포함된다.

실제로 서비스 프로바이더는 서로 다른 세그먼트 지속 시간과 다른 비디오 코텍을 사용할 수 있다.

그러나 이러한 긴 세그먼트는 당연히 1초 길이의 여러 짧은 세그먼트로 나눌 수 있다. 이러한 방식으로 제안된 접근법은 짧은 세그먼트의 feature를 입력하여 적용할 수 있다.

향후 연구에서는 다양한 세그먼트 지속 시간과 세션 길이를 사용하여 우리의 접근법을 평가할 계획이다.

또한 다른 비디오 코덱을 사용하여 평가한다면, 제안된 접근법의 검증 및 개선을 시도할 수 있을 것이다.


계산 복잡도와 관련하여, 우리는 주어진 세션 duration 동안 Overall quality 값을 얻기 위한 평균 시간을 측정했다. 총 1223개의 세션을 8초에서 76초 길이로 사용하였으며, 이는 위 성능 평가에 사용된 세 가지 데이터셋의 모든 세션이다.

측정은 3.40GHz의 인텔 코어 i3-3240 프로세서와 8GB RAM 사양의 컴퓨터로 수행하였다.

adLSTM, baLSTM에 대한 모든 input option에서 8초, 60초, 76초의 세션 길이에 각각 1.5ms, 4.5ms, 5.5ms 미만의 계산 복잡도를 보였다. 일반적으로 제안된 접근법의 계산 복잡도는 세션 duration에 따라 선형적으로 증가한다.

향후 연구에서는 실시간 품질 모니터링을 위해 이 LSTM 기반 접근법의 시간 복잡도를 줄일 예정이다.

---
### Notes

Complex I1 option  
* Segment Quality -> Stalling Duration 순으로 영향이 큼.
* Content Characteristics는  Segment Quality feature의 측정 메트릭에 따라 달라짐.
* Segment Quality 측정 메트릭으로는 S-MOS가 가장 적합함.   
  Full 케이스에 한해 PSNR은 간단함에도 좋은 성능을 보임.  
  S-MOS를 메트릭으로 사용하면 Content Characteristics를 feature로 추가하여도 성능 향상이 크지 않음. => S-MOS가 이미 이를 포함하고 있음.


Simple I2 option
* Content Characteristics를 포함하지 않음에도 좋은 성능을 보이며, 효율적임.


LSTM
* adLSTM이 보다 좋은 성능을 보였으나, baLSTM도 비슷하게 좋은 성능을 보였음.
* baLSTM은 파라미터 수가 절반 수준으로, 가벼움.
* 현재와 과거 세그먼트만으로 처리가 가능하여 세션이 끝날 때까지 기다릴 필요 없이 진행 중인 세션의 품질을 지속적으로 예측할 수 있음.
* 스위칭 빈도를 feature로 추가하여도 성능이 향상되지 않는 것은 LSTM이 temporal relation을 잘 반영하기 때문이며, 기존 접근법과의 성능 차이의 주요 이유임.


복잡도
* 주어진 세션 duration 동안 Overall quality 값을 얻는 데에 소요되는 평균 시간을 측정하였음.
* 제안된 접근법의 모든 조건에서 세션 duration 8s, 60s, 76s에 대해 각 1.5ms, 4.5ms, 5.5ms 미만의 계산 시간을 보임.


---
## **IX. Conclusion**

본 연구에서는 LSTM을 사용하여 HAS의 Overall quality를 예측하기 위한 새로운 머신러닝 접근법을 제안하였다. 

제안된 접근법은 Segment Quality, Content Characteristics, Stalling Duration, Padding과 같은 몇 가지 feature를 고려한다.

실험 결과를 통해 제안된 접근법이 HAS의 Overall quality 예측에서 매우 높은 성능을 달성함을 보였다.

input option과 LSTM 설정에 대한 광범위한 평가를 바탕으로 input feature의 역할, Segment Quality feature를 나타내는 최적의 메트릭, 효과적이고 효율적인 input option, 최적의 LSTM 설정 등에서 결과를 얻었다.

향후 연구에서는 다양한 세그먼트 duration, 세션 길이 및 비디오 코덱을 사용하여 제안된 접근법을 평가할 계획이다. 이를 위해 새로운 데이터셋을 구축할 필요가 있으며, 제안된 접근법을 HAS를 위한 적응 전략의 성능 평가에 활용할 계획이다.

---

세션은 세그먼트 시퀀스, 당연히 연속되는 세그먼트 간에 temporal한 관계가 있음.

실제로 LSTM도 좋았음.. 그리고 본 논문의 adLSTM 구조를 보면, 미래 판단에 지난 과거의 정보도 사용함. 또 성능도 baLSTM보다 좋음.    
구조가 딱 컨텍스트를 고려하는 느낌,, 트랜스포머, 바다나우 어텐션,, 뭐 이런 것들 사용해도 좋은 성능 보이지 않을까?  
근데, baLSTM 장점이 기존 과거와 현재만 있어 세션 진행 중에 예측이 가능하다고 하는데,, 내 생각에는 adLSTM도 똑같을 것 같은데,,  
이 부분 조금 더 알아술 실시간성을 저해하지 않는 수준이라면, 사용할 수 있을듯.

트랜스포머를 여기에 적용하되 모델 자체에 집중하기 보다는 본 논문 레퍼런스 해서 temporal한 관계가 있다.  
그리고 같은 모티베이션에서 시작한 이 논문의 결론에서도 그렇듯 스트리밍 세션을 구성하는 세그먼트 사이에는 temporal한 관계가 있다는 것을 트랜스포머로 좋은 성능 뽑아서 이야기한다면, 모델, 인공지능 쪽보다는 네트워크 쪽으로 이야기할 수 있지 않을까 싶음.


이걸로 논문 써본다면,  
모티베이션: 세션을 구성하는 세그먼트 간의 시간적 관계가 존재함. LSTM도 좋음. 그러니 트랜스포머 써보자.  
~  
실제로 트랜스포머 써보니 좋더라~ 그러한 관계가 있다는 것은 분명하다.  << 단지 이걸로는 근거가 빈약한 느낌  
일반적인 영상들은 결국 프레임 당 사진의 연속이니 당연한 거임. 이걸 비전 쪽 개념 가지고 와서 트랜스포머랑 같이 엮어서 설명하면, 좋을 것 같기도.  
근데 이렇게 되면, 트랜지션이 잦은 영상은?  
트랜지션 잦은 영상에도 트랜스포머가 잘 나오는지 보고, 잘 나오면, 결국 영상에서 트랜지션이 일어나는 순간은 짧고(세그먼트를 많이 먹지 않는다), 다시 사진의 연속이 된다는 쪽.  
잘 안 나오면 그거에 맞춰서 생각해봐야할듯,,


아니면, 기존에 있는 이러한 방법들을 실제로 활용하였을 때 네트워크 리소스를 많이 먹진 않는지, 실시간성에 적합한지, 등을 생각해서 테스트하고 개선하는 건 어떨지?  
또는 LSTM 같이 성능 개선보다는 모델을 활용하되, 전체 네트워크에서 이득을 볼 수 있는 방향으로 디벨롭하는 것도 좋을듯. 보안성도 높일 수 있을듯 federated learning은 어떨지 공부해봐야 할듯


이 논문도 이전 논문도 DASH 알고리즘 자체를 건드리는 게 아니라, Overall quality 예측에 한한다는 게 아쉬움,,, Overall quality 예측해서 활용하는 방향으로 가야하는 게 아닌가,,?

멀티모달로 가보는 건 어떨까,,

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














