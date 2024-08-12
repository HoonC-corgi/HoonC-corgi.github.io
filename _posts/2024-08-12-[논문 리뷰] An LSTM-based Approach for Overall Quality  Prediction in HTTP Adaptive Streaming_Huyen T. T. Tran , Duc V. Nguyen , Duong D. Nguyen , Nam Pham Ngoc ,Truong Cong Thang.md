---
published: True
---

# **An LSTM-based Approach for Overall Quality Prediction in HTTP Adaptive Streaming**


## Huyen T. T. Tran, Duc V. Nguyen, Duong D. Nguyen, Nam Pham Ngoc and Truong Cong Thang

-----

<!-- TOC -->
* [**An LSTM-based Approach for Overall Quality Prediction in HTTP Adaptive Streaming**](#an-lstm-based-approach-for-overall-quality-prediction-in-http-adaptive-streaming)
  * [Huyen T. T. Tran, Duc V. Nguyen, Duong D. Nguyen, Nam Pham Ngoc and Truong Cong Thang](#huyen-t-t-tran-duc-v-nguyen-duong-d-nguyen-nam-pham-ngoc-and-truong-cong-thang)
  * [논문 리뷰를 시작하며](#--)
  * [한글 번역 및 코멘트](#---)
    * [Abstract](#abstract)
      * [Notes](#notes)
    * [Introduction](#introduction)
      * [Notes](#notes)
        * [Motivation](#motivation)
    * [Proposed Approach](#proposed-approach)
      * [A. Architecture](#a-architecture)
        * [Notes](#notes)
      * [B. Segment Features](#b-segment-features)
        * [Notes](#notes)
    * [Evaluation](#evaluation)
      * [A. Experiment Settings](#a-experiment-settings)
        * [1) Dataset](#1--dataset)
        * [2) Training Parameters](#2--training-parameters)
        * [3) Input Features](#3--input-features)
        * [4) Evaluation Metircs](#4--evaluation-metircs)
          * [Notes](#notes)
      * [B. Roles of Segment Features](#b-roles-of-segment-features)
        * [Notes](#notes)
      * [C. Comparison with Existing Approaches](#c-comparison-with-existing-approaches)
        * [Notes](#notes)
    * [Conclusion](#conclusion)
      * [Notes](#notes)
<!-- TOC -->

---

## 논문 리뷰를 시작하며

 연구실에서 진행하는 논문 세미나를 위한 논문 지정을 위해 여러 논문의 Abstract를 훑어 보던 시기에 교수님과의 면담에서 하나의 논문을 읽더라도 
제대로 읽어 보고 이해할 필요가 있다는 조언에 따라 훑어 보았던 여러 논문 중 가장 관심이 갔던 논문을 처음부터 읽어 보았다.  
이제부터는 단순히 논문 세미나 준비를 위한 읽기가 아닌, 올해 목표인 국내 논문 투고를 성취하기 위해서 연구 주제 선정을 위한 연구 동향을 파악, 인사이트를 얻는 것에 집중할 시기이기 때문이다.  
아직 어떠한 주제로 접근해야 할 지, 내가 주로 어떤 분야로 연구를 할지 정하지는 못했지만, 가장 끌리는 것은 IEEE 802.11의 표준 연구, 비디오 스트리밍이며, 오늘의 논문은 스트리밍과 관련된 논문이다.  


**[An LSTM-based Approach for Overall Quality Prediction in HTTP Adaptive Streaming](https://ieeexplore.ieee.org/abstract/document/8845041)** - Huyen T. T. Tran, Duc V. Nguyen, Duong D. Nguyen, Nam Pham Ngoc and Truong Cong Thang (IEEE INFOCOM WKSHPS • April 2019)

---
## 한글 번역 및 코멘트
### Abstract

**HAS(HTTP Adaptive Streaming)** 은 최근의 멀티미디어 전송을 위한 일반적인 솔루션이다. 

일반적으로 HAS에서는 각 스트리밍 세션에서의 비디오 품질이 변화한다.  

따라서 HAS에서의 핵심적인 질문은 스트리밍 세션에서의 전반적인 품질을 어떻게 평가할 것인지이다.

본 논문에서는 HAS에서 전반적인 품질 예측을 위한 머신러닝 접근법을 제안한다.

제안된 접근법에서 각 세그먼트는 _segment quality, stalling durations, content characteristics과 padding_ 의 네 가지 기능으로 표현된다.

특징들은 LSTM 네트워크에 입력되어 세그먼트 간의 시간적인 관계를 발견할 수 있도록 한다.

스트리밍 세션의 전반적인 품질은 선형회귀 모듈을 사용하는 LSTM 네트워크의 출력으로부터 예측된다.

실험적 결과들은 제안된 접근법이 스트리밍 세션의 전반적인 품질 예측에 효과적임을 보인다.

또한 본 방식은 기존의 네 가지 접근 방식을 능가하는 것으로 나타났다.

---
#### Notes

* **HAS(HTTP Adaptive Streaming)**
  * 비디오 컨텐츠를 상이한 품질을 가지는(상이한 비트레이트로 인코딩된) 여러 개의 작은 세그먼트로 분할함.  
  * 각 세그먼트들은 HTTP 서버에 저장되며, 클라이언트는 네트워크 상태에 따라 적절한 품질의 세그먼트를 선택하여 다운로드함.  
  * 이떄 각 세그먼트의 URL, 길이, 비트레이트, 해상도 등의 정보들은 매니페스트 파일에 저장되며, 이를 읽고 네트워크 상태에 맞게 선택하여 독립적으로 다운하는 방식임.  
  * 이외에도 고정된 품질의 비디오 컨텐츠를 HTTP 서버에서 다운로드 하는 PD(Progressive Download), UDP를 사용하고 실시간 스트리밍에 적합한
    RTSP(Real-Time Streaming Protocol)와 RTP(Real-time Transport Protocol)가 있음.
* HAS에서 각 스트리밍 세션에서의 비디오 품질 변화가 발생, 이를 평가할 방법이 중요한 포인트임.  
* LSTM을 활용하여 세그먼트 간의 시간적인 관계를 발견하고자 함.  
  * LSTM의 입력: segment quality, stalling durations, content characteristics padding 
  * LSTM의 출력: 스트리밍 세션의 전반적인 품질


* HAS는 네트워크 상태에 따라 적응형으로 세그먼트를 선택하는 것인데, 스트리밍 세션의 전반적인 품질을 예측한다는 것이 네트워크 상태 평가와 같은 것인지?  
  * 두 개가 상이하다면, 스트리밍 세션의 전반적인 품질을 예측해서 무엇을 하겠다는 것인지? 세그먼트 구조 조정?
* 여러 모델 중 LSTM을 사용한 이유는 무엇인지?
* LSTM 입력 파라미터 각각의 근거는 무엇인지?


---
### Introduction

HAS는 현재 멀티미디어 전송을 위한 비용에 효율적인 수단이 되었다.  

HAS에서는 비디오가 먼저 상이한 품질 수준의 여러 버전으로 인코딩된다.  

각 버전은 다시 짧은 세그먼트 시퀀스로 나누어진다.  

네트워크 상태에 따라 적절한 버전의 세그먼트가 선택된다.  

네트워크 대역폭의 변동으로 인해 선택된 세그먼트의 버전은 스트리밍 세션 동안 크게 달라질 수 있으며, 이는 품질 변화를 야기할 수 있다.  

또한, 세그먼트가 재생 기한(playback deadline) 전에 클라이언트에 도착하지 않으면 중단(stalling) 현상이 발생할 수 있다.  

품질 변동과 stalling 현상은 사용자의 시청 경험에 부정적인 영향을 미치는 것으로 알려져 있다.  

따라서, HAS의 주요 과제는 이러한 요소들의 영향을 고려하여 스트리밍 세션의 전반적인 품질을 평가하는 방법을 찾는 것이다.  


기존의 전반적인 스트리밍 세션 품질을 예측하는 방법들 대부분은 분석 모델 기반의 접근법으로, 
이 접근법에서는 품질에 영향을 미치는 요인들을 일부 통계치(세그먼트 품질 값의 평균 및 stalling duration의 평균)의 분석 함수(e.g., 선형 함수)를 사용하여 모델링한다.  

이러한 접근법들 중에서, 품질 변동과 stalling 현상의 영향을 모두 고려하는 경우는 극히 일부에 불과하다.  


[9]의 연구는 전반적인 품질 예측을 위한 고급 머신러닝 접근법을 제안한 첫 번째 연구이다.  

이 접근법에서는 random neural network가 사용되었다.  

네트워크 입력으로는 모든 비디오 프레임의 모든 매크로 블록에 대한 양자화 파라미터의 평균, stalling의 발생 횟수, stalling duration의 평균 및 최댓값이 포함된다.  

이 접근법은 16초 동안 118개의 스트리밍 세션을 사용하여 평가되었다.  


[10]의 저자들은 Support Vector Regression을 사용하는 고급 머신러닝 접근법을 제안하였다.  

이 접근법의 입력으로는 세그먼트 품질 값의 평균, 세그먼트 품질이 감소한 시간, 마지막 손상 이벤트(i.e., stalling 또는 세그먼트 품질 감소) 이후의 시간, stalling 발생 수와 duration의 합을 포함한다.  

이 접근 법은 약 72초 동안의 112개의 세션을 사용하여 평가되었다. 또한 [9], [10] 두 연구 모두 비디오 컨텐츠 특성의 영향을 고려하지 않았다는 점에 유의해야 한다.


이 연구에서 우리는 HAS 세션의 전반적인 품질을 예측하기 위한 새로운 고급 머신러닝 접근법을 제안한다. 제안된 접근법에서는 LSTM Network를 사용한다. 그 이유는 두 가지이다.  

첫째, LSTM은 메모리를 사용하여 비디오 세그먼트 간의 시간적 관계를 활용할 수 있기 때문이다.  

둘째, LSTM 네트워크는 video summarization과 video action recognition과 같은 다양한 비디오 관련 작업에서 성공적으로 사용된 바 있다.  


본 연구에서의 주요 기여는 다음과 같이 요약된다.  

첫째, 우리는 HAS 세션의 전반적인 품질을 예측하기 위해 LSTM 네트워크를 사용하는 새로운 고급 머신러닝 접근법을 제안한다.  
제안된 접근법은 품질 변화, stalling 이벤트, 컨텐츠 특성의 영향을 고려한다. 우리가 아는 한, HAS의 전반적인 품질 예측에 LSTM 네트워크를 사용하는 것은 본 연구가 처음이다.  

둘째, 제안된 접근법은 60초에서 76초 사이의 세션 길이를 가진 515개의 세션으로 구성된 데이터셋을 사용하여 평가하였다.  
실험 결과, 제안된 접근법이 높은 예측 성능을 달성하였으며, 기존의 네 가지 접근법보다 우수한 성과를 보였다.


---
#### Notes

* HAS는 여러 품질을 가지는 버전으로 인코딩되며, 각 버전은 다시 짧은 세그먼트 시퀀스로 나누어 짐. 이후 네트워크 상태에 따라 적절한 버전의 세그먼트를 선택함.  
  * 이렇게 대역폭의 변동으로 인해 선택된 세그먼트 버전은 스트리밍 세션 동안 크게 변화할 수 있으며, 이는 품질 변화를 야기할 수 있음.
  * 또한 세그먼트가 playback deadline 전에 도착하지 못할 경우 stalling 현상 발생할 수 있음.  
* 기존의 스트리밍 세션 품질 예측 방법
  * 세그먼트 품질 값의 평균 및 stalling duration의 평균 등의 일부 통계치를 선형 함수와 같은 분석 함수에 넣어 모델링하는 분석 모델 기반의 방식임.  
  **=> 품질 변화와 stalling 현상이 미치는 영향을 모두 고려하는 연구가 부족함.**

    
##### Motivation

* 고급 머신러닝 접근법을 제안한 연구들
  * random neural network를 사용한 연구
    * 입력: 모든 비디오 프레임의 모든 매크로 블록에 대한 양자화 파라미터의 평균, stalling 발생 횟수와 duration의 평균 및 최댓값

  * SVR(Support Vector Regression)을 사용한 연구
    * 입력: 스트리밍 동안의 세그먼트 품질 값의 평균, 품질 감소가 발생한 시간, 마지막으로 발생한 품질 저하/stalling 이후 경과된 시간, stalling 횟수 및 duration의 합

  => 두 연구 모두 video content characteristics(움직임, 장면 복잡도, 트랜지션 등등)의 영향을 고려하지 않았다는 한계가 있음.


* LSTM을 사용한 전반적인 품질 예측 접근법 제안
  * LSTM을 사용한 근거
    1. 비디오 세그먼트는 시간적 순서에 따라 재생되기 때문에 시계열 데이터 처리에 강한 순환신경망의 장점을 활용하여 시간적 과녜를 학습, 비디오 스트리밍 세션의 품질 변동을 보다 정확히 예측할 수 있다.  
    2. 실제로 LSTM은 이미 video summarization과 video action recognition과 같은 비디오 관련 작업에서 성공적으로 사용된 바 있다. 즉, 1.의 근거를 뒷받침할 수 있다.  
  * 주요 기여
    * LSTM을 HAS의 전반적인 품질 예측에 사용한 최초의 연구
      * 순환신경망으로 처리한다는 점에서 기존 연구에 비해 더 정교한 예측을 가능하도록 함.
    * 품질 변화, stalling 이벤트, 컨텐츠 특성을 모두 고려하는 접근 방식
    * 실험 결과에서 기존의 네 가지 접근법에 대하여 우수한 결과 도출

* 실험 환경을 60초에서 76초 사이의 세션 길이를 가진 515개의 세션으로 구성한 것이 어떠한 시나리오를 대변하는지에 대한 분석 필요할듯.
* 기존 네 가지 접근법과 같은 환경에서 비교한 것이 맞는지 따져볼 것.


-----
### Proposed Approach

이 섹션에서는 먼저 제안된 접근 방식의 아키텍처를 제시한다. 이후 다음 네 가지 세그먼트 기능에 대해 자세히 설명한다.

#### A. Architecture
그림 1은 제안된 접근법의 아키텍처를 보여준다. 특히, 각 스트리밍 세션은 일련의 세그먼트로 간주된다. 각 세그먼트는 일련의 특징들로 구성된다. 
이러한 특징들은 LSTM 네트워크에 입력으로 제공된다.  

LSTM 네트워크의 출력은 선형 회귀 모듈을 통해 전체 스트리밍 세션의 품질을 예측하는 데 사용된다.  

볼드체 대문자(e.g., X), 볼드체 소문자(e.g., x), 이탤릭체 문자(e.g., _X_)는 각각 행렬, 벡터, 스칼라를 나타낸다. _T_는 스트리밍 세션에서의 세그먼트 수를 나타낸다.


```
X_t = [ x^1_t x^2_t … x^M_t ]
```

세그먼트 𝑡 (1 ≤ 𝑡 ≤ 𝑇)의 특징 벡터를 x_t라고 하며, _M_은 세그먼트당 특징의 수다. 각 벡터 x_t는 그림 2에 나타난 바와 같이 LSTM 유닛을 통해 은닉 상태 **h**_t와 연결된다.
특히 은닉 상태 **h**_t는 다음 방정식을 사용하여 계산된다.

```
i_t = sigm(W_{ix} * x_t + V_{ih} * h_{t-1} + b_i),    (2)   
f_t = sigm(W_{fx} * x_t + V_{fh} * h_{t-1} + b_f),    (3)  
o_t = sigm(W_{ox} * x_t + V_{oh} * h_{t-1} + b_o),    (4)  
g_t = tanh(W_{gx} * x_t + V_{gh} * h_{t-1} + b_g),    (5)  
c_t = f_t ⊙ c_{t-1} + i_t ⊙ g_t,                      (6)  
h_t = o_t ⊙ tanh(c_t),                                (7)
```

이때 ⊙는 요소별 곱셈을 나타내며, **W** ∈ ℝ^{d×M}, **V** ∈ ℝ^{d×d}, **b** ∈ ℝ^d의 매개변수들은 학습 과정에서 학습되고, LSTM 유닛 전체에서 공유된다.  

**i**_t, **f**_t, **o**_t, **c**_t는 각각 입력 게이트, 망각 게이트, 출력 게이트, 메모리 셀의 출력 벡터를 나타낸다.  

이들은 LSTM 유닛이 세그먼트 간의 시간적 관계를 활용할수 있도록 하는 중요한 구성 요소이다.  

특히, 입력 게이트는 현재 입력에서 새로운 정보를 메모리 셀에 추가할지 여부를 결정한다.  

망각 게이트는 메모리 셀에서 오래된 정보를 선택하고 제거하며, 출력 게이트는 메모리 셀에서 유용한 정보를 선택하여 은닉 상태 **h**_t를 업데이트한다.

선형 회귀 모듈에서 세션의 전반적인 품질 Q는 마지막 세그먼트에 해당하는 숨겨진 상태 **h**_ _T_ 로부터 다음과 같이 예측된다.  
```
Q = w_r * h_T + b_r
```

여기서 w_r과 b_r도 학습되어야 하는 매개변수이다.  


##### Notes

* 제안된 접근법의 구조와 동작
  * 스트리밍 세션의 세그먼트화  
    제안된 접근법에서는 각 스트리밍 세션을 여러 개의 세그먼트로 나누어 처리함. 각 세그먼트는 해당 세그먼트의 품질, stalling duration, 컨텐츠 특성 등의 특징들로 표현됨.  
  * LSTM 네트워크의 역할  
    세그먼트의 특징들은 LSTM이 입력으로 사용됨. LSTM은 특징들을 통해 세그먼트 간의 시간적 관계를 학습하여, 스트리밍 세션 전반의 품질을 예측하는 데 필요한 정보를 추출함.  
    * 선형 회귀 모듈  
      LSTM 네트워크의 출력은 선형 회귀 모듈로 전달되어 최종적으로 전체 스트리밍 세션의 품질을 예측함. 선형 회귀는 LSTM의 출력 값들을 조합하여 최종적인 품질 점수를 계산함.  
  * 표기
    * X: 행렬
    * x: 벡터
    * _X_: 스칼라
    * _T_: 스트리밍 세션에서의 세그먼트 수
    * ⊙: 요소별 곱셈

  * LSTM 학습 방정식
    * **W** ∈ ℝ^{d×M}: 입력에서의 가중치
    * **V** ∈ ℝ^{d×d}: 이전 은닉 상태에서의 가중치
    *  **b** ∈ ℝ^d: bias 벡터

  * 각 게이트의 역할
    * 입력 게이트: 현재 입력에서 새로운 정보를 메모리 셀에 추가할지 여부를 결정함.  
    * 망각 게이트는 메모리 셀에서 오래된 정보를 선택 및 제거함.  
    * 출력 게이트는 메모리 셀에서 유용한 정보를 선택하여 은닉 상태를 업데이트 함.  

  * 전체 스트리밍 세션의 품질 예측 과정
    * 선형 회귀 모듈
      * LSTM 네트워크의 출력인 마지막 은닉 상태 **h**_T를 기반으로, 전반적인 세션의 품질 _Q_ 를 예측하는 과정에 선형 회귀 모듈이 사용됨.   
        _Q_ = **w** _r * **h** _T + b_r  
        **w** _r은 가중치 벡터, b_r은 바이어스 값이며, 학습 과정 동안 최적화 됨.  
        최종적으로 수식에 네트워크의 출력 **h** _T를 입력으로 하여, 세션의 품질 점수 _Q_를 예측함.  

---

#### B. Segment Features


##### Notes



---

### Evaluation


#### A. Experiment Settings


##### 1) Dataset


##### 2) Training Parameters


##### 3) Input Features


##### 4) Evaluation Metircs


###### Notes

---


#### B. Roles of Segment Features


##### Notes


---


#### C. Comparison with Existing Approaches


##### Notes


---

### Conclusion


#### Notes


---