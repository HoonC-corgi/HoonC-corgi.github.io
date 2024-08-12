---
title: "Multi-Link Operation in IEEE 802.11be WLANs"
published: True
excerpt: "Álvaro López-Raventós and Boris Bellalta (IEEE Wireless Communications • August 2022)"
---

<!-- TOC -->
* [논문 리뷰를 시작하며](#--)
* [한글 번역 및 코멘트](#---)
  * [Abstract](#abstract)
    * [Notes](#notes)
  * [Introduction](#introduction)
    * [Notes](#notes)
  * [Multi-Link Operation](#multi-link-operation)
    * [Architecture](#architecture)
      * [Notes](#notes)
    * [Transmission Modes](#transmission-modes)
      * [Notes](#notes)
    * [Management](#management)
      * [Notes](#notes)
    * [Power Save](#power-save)
      * [Notes](#notes)
  * [Traffic Management](#traffic-management)
    * [Notes](#notes)
  * [Performance Evaluation](#performance-evaluation)
  * [Open Issues and Challenges](#open-issues-and-challenges)
    * [Notes](#notes)
<!-- TOC -->

---

# 논문 리뷰를 시작하며

 연구실에서 진행하는 논문 세미나를 준비하는 과정에서 해당 내용들을 그냥 넘어가지 않고, 블로그에 남겨 놓는다면 추후에 내가 공부하면서 어려워 했던 부분이나,
 놓쳤던 부분을 다시 확인할 수 있을 것 같아 논문 리뷰 글도 블로그에 작성해 보기로 하였다.
 
오늘의 논문은 아래와 같다.

**Multi-Link Operation in IEEE 802.11be WLANs** - Álvaro López-Raventós and Boris Bellalta (IEEE Wireless Communications • August 2022)

---
# 한글 번역 및 코멘트
## Abstract

multi-link operation (MLO)은 IEEE 802.11 be Extremely High Throughput (EHT)의 개선안의 일부로 제안된 새로운 기능이다.

이러한 기능은 노드가 여러 무선 인터페이스를 통해 동시에 데이터를 송수신할 수 있게 됨으로써 multi-link 통신으로 패러다임 전환됨을 보인다.

이를 위해 802.11be의 태스크 그룹은 노드의 아키텍처, 전송 운영 및 관리 기능에 대한 다양한 수정 사안을 제안하였다.

본 기사에서는 이러한 변경사항과 여전히 해결되지 못한 트래픽이 다중 링크에서 어떻게 분산되어야 하는지에 대한 문제를 검토한다.

이를 위해 active links를 통해 상이한 load balancing 전략을 평가한다.

결과는 high load, high dense, 그리고 복잡한 시나리오에서 MLO를 사용하여 차세대 WLAN의 성능을 높이기 위한 congestion aware load balancing 전략이 필수적임을 시사한다.

---
### Notes

* MLO는 IEEE 802.11 be EHT의 개선안의 일부로 새롭게 제안된 특징임
* MLO로 인해 노드는 여러 무선 인터페이스를 통해 동시에 데이터 송수신이 가능해짐.
* 이로 인해 multi-link 통신으로 패러다임이 전환됨.
  * _기존에는 어땠기에?_
* MLO를 위해 노드 아키텍처, 전송 운영 및 관리 기능에 대한 수정 사안 제안됨.
* 이렇게 제안된 여러 변경사항과 "여전히 해결되지 못한 문제"에 대하여 이야기하는 논문임.
  * **트래픽이 multi-link에서 어떻게 분산되어야 하는가?**
    * active links에서의 여러 load balancing 전략 평가를 통해 확인
 

* 결과적으로 high load, high dense, complex 시나리오에서는 MLO를 사용하여 차세대 무선랜의 성능을 높이기 위한 **congestion aware load balancing** 전략이 필수적임.

---
## Introduction

WiFi로 흔히 알려진 IEEE 802.11 standard는 낮은 복잡성과 비용 효율을 가지는 무선 연결을 제공하기 위한 솔루션으로 90년대 후반에 출시되었다.

최근 6세대가 된 WiFi의 확산은 지속적인 표준 개정을 통해 새로운 use-case에 따른 요구사항에 대처할 수 있게 되었기 때문이다.

무선 데이터 서비스들은 방대한 양의 데이터, 까다로운 throughput, latency, 신뢰성을 요구하는 VR/AR, Video/Game Streaming, 클라우드 서비스 등의 어플리케이션과 함께 계속해서 성장할 것이다.

이러한 기대를 해결하기 위해 802.11be의 태스크 그룹 (TGbe)은 다가오는 WiFi 7을 가속 시키기 위한 개발을 해결하기 위해 2019년 5월에 만들어 졌다.

IEEE 802.11be Extremely High Throughput (EHT)라고 불리는, 이 개정안은 WiFi의 throughput을 증가 시키고, 종단 간 latency를 줄이며, 통신의 신뢰성을 높이는 것을 목표로 한다.

이에 Multi-link Operation (MLO)는 MLO 주요 후보 기능으로 고려되고 있으며, 여러 무선 인터페이스를 사용하도록 하여,
이중, 삼중 대역 기능을 가지는 APs와 STAs에서 데이터를 동시에 송수신할 수 있게 한다.

게다가 흥미롭게도 MLO 프레임워크의 사용은 빠르게 증가하고 있다. 
실시간 어플리케이션에서의 Latency는 이미 [3-5]에서 진행된 연구에서 MLO가 worst-case에서의 latency를 확연히 줄일 수 있음을 보인다.
한편, [4]의 저자들은 다중 링크에서의 신뢰성을 평가하기 위해 분석을 확장하였는데, 여러 개의 correlated 링크일 때 높은 전송율을 보였다.
또한 동시 송수신(STR) 기능 없이 상이한 링크들을 통해 스테이션에 병렬적으로 downlink 송신할 수 있도록 하는 **end time alignment mechanism**이 [6]에 제시되어 있다.
이러한 접근은 스펙트럼 효율을 최대화 하는 것을 의도한다.
유사하게 진행 중인 송신에서 collision이 발생하지 않는 것을 확인하게 되면 non-STR 스테이션들이 backoff 타이머를 다시 시작할 수 있도록 하는 **opportunistic backoff** 메커니즘이 [7]에서 제안 되었다.
[8]의 저자들은 APs 간 협조 없이 MLO의 기능만으로는 충분하지 않을 것이기에 high density areas에서의 high throughput을 구현하기 위한 프레임워크를 제안했다.

여러 무선 인터페이스를 동시에 운영할 수 있도록 하는 프레임워크의 통합은 새로운 문제와 연구 기회를 가져온다. 이러한 맥락에서 우리는 MLO 호환 장치가 여러 링크를 통해 서로 다른 Quality of Service (QoS) 요구사항을 가지는 패킷을 송수신 할 수 있다는 것을 알았다.
지난 개정에서는 허용되지 않았던 이 기능은 link mapping을 위한 **TID**라고 불리며 이는 새로운 트래픽 관리 메커니즘을 구상할 수 있게 한다.
예를 들어 우리는 모든 링크에 TID를 할당할 수 있게 되어, 트래픽을 여러 링크 간에 부분적으로 또는 완전히 이동시킴으로써  **full adaptive load balancing** 전략을 사용할 수 있다.
반대로, 다른 방식들은 특정 QoS 트래픽에 전용 링크를 할당하는 데 의존할 수 있다. 이러한 방식은 더욱 엄격하고 덜 유연한 load balancing 솔루션이지만, 동일한 QoS 요구사항을 가지는 트래픽만이 서로의 자원 세트를 공유하도록 보장한다.

이 기사에서, 우리는 여러 인터페이스에 트래픽을 배포하는 방법과 그것이 네트워크 성능에 어떤 영향을 미칠 수 있는지에 대한 통찰력을 제공하기 위해 적응형 또는 링크 전용 구현을 따르는 다양한 할당 전략을 평가한다.

주된 내용은 아래와 같다.
* 우리는 MLO 프레임워크가 TGbe에 의해 어떻게 고안되고 있는지에 대한 포괄적인 개요를 제공하며, 노드의 아키텍처 변경, 트랜스 미션 모드 및 관리 기능과 관련하여 다양한 수정 사항을 지적한다.
* 우리는 잠재적인 이익과 개정안과 관련된 도전 문제에 대해 논의한다. 또한, 우리는 MLO 프레임 작업이 직면한 몇 가지 열린 문제와 연구 방향을 지적한다.
* 우리는 TID 할당 문제를 해결하기 위해 다양한 정책 기반 전략을 평가한다. 또한, 우리는 구현, 적합성 및 단점을 보여주기 위해 TID-to-link 매핑 기능을 채택한다.
* 우리는 다른 트래픽 요구사항에 따라 제시된 전략을 평가하여 link-dedicated 접근이 high dense와 high load 시나리오 모두에서 적합하지 않음을 보인다.

---
### Notes

* MLO의 장점
  * worst-case latency의 대폭 감소
  * 여러 개의 correlated 링크일 때 높은 전송율 보장
  * end time alignment mechanism
    * 동시 송수신 기능 없이 상이한 링크들로 스테이션에 병렬적으로 downlink하는 것
  * 유사하게 opportunistic backoff mechanism에서는 진행 중인 송신에서 collision이 발생하지 않는 경우 non-STR 스테이션들이 vackoff 타이머를 다시 시작할 수 있도록 한다.
    * 두 mechanism 모두 collision의 감소에 목적을 둔다는 점에서 유사
  * MLO 호환 장치가 여러 링크를 통해 서로 다른 QoS 요구사항을 가지는 패킷을 송수신 할 수 있음.
    * 이는 link mapping을 위한 TID로, 지난 개정에는 허용되지 않았으나 트래픽 관리 메커니즘을 새로 할 수 있음.
      * ex) TID를 모든 링크에 할당 -> 트래픽을 여러 링크에 이동 가능 -> **full adaptive load balancing** 가능
    * QoS 트래픽에 전용 링크 할당하는 방법도 존재
      * 더욱 엄격하고 덜 유연한 load balancing
      * 동일한 QoS 요구사항을 가지는 트래픽만이 서로의 자원 세트를 공유할 수 있도록 보장함.


* AP간 협조 없이는 부족!
  * high density areas에서의 high throughput 구현을 위한 프레임워크도 제안됨.

* 이어질 내용
  * MLO 프레임워크의 포괄적 개요
  * 남은 문제들
  * TID 할당 문제 해결을 위한 전략 평가
    * TID-to-link mapping 기능으로 평가
  * link-dedicated 접근이 high dens와 high load 시나리오에서의 성능평가

-----
## Multi-Link Operation

MLO의 기능과 관련하여 IEEE 802.11 개정에 포함될 가능성이 있는 다양하고 가장 관련성 높은 제안들을 살펴 본다.  

### Architecture
첫 번째 아키텍처 변화는 고전적인 AP, STAs를 MLD(Multi-Link capable Device)로 재정의 하는 데에 있다.  
AP MLDs 또는 STA MLDs는 여러 무선 인터페이스가 있는 단일 장치를 나타낸다.  
이와 가장 연관된 측면으로 MLDs 각 인터페이스의 독립적인 파라미터를 잃지 않은 채 고유한 MAC 인스턴스를 상위 계층에 제공한다는 사실이다.  
이를 구현하기 위해서 be의 Task Group은 기능에 따라 두 개의 레벨으로 MAC sub-layer를 나눌 것을 제안하였다.    

첫째로, 상위 MAC(U-MAC)이 있는데, 이는 모든 인터페이스에 대한 MAC sub-layer의 공통 부분이다.  
U-MAC에서는 링크에 불가지론적인(알수 없는? 무관한? 구애받지 않는?) 작업이 발생함을 알 수 있다. 그 예로, 시퀀스 넘버 할당 및 MSDU(MAC Service Data Unit) aggregation 및 aggregation 해제가 있다.  
이러한 맥락에서 동일한 트래픽 흐름에 속하는 패킷은 서로 다른 링크를 통해 fragment되고 전송될 수 있기 때문에 시퀀스 번호 할당은 U-MAC에서 수행되어야 한다는 점이 중요하다.
그리고 이러한 접근은 수신 측에서 패킷을 재정렬하는 것을 용이하게 한다. 추가적으로 설정 연결 및 인증과 같은 모든 링크에 대한 공통 관리 기능이 본 layer에서 이루어 진다.  

U-MAC의 아래에 있는 low MAC(L-MAC)은 각 인터페이스에 독립적인 하위 계층으로, 채널 엑세스와 같은 링크의 특정 기능을 담당한다. 이러한 맥락에서 개별적인 L-MAC 인스턴스들을 가지는 것은 필요한 경우 인터페이스가 그들의 채널 파라미터를 유지할 수 있도록 한다.

본질적으로, 이러한 구현은 각 인터페이스가 자신의 향상된 분산채널 액세스 큐(EDCA Queue)를 추적하여 전송이 끝날 때까지 트래픽을 유지할 수 있도록 허용한다.  
L-MAC 계층의 다른 기능들은 각각 송수신할 때 관리 및 제어 프레임을 생성하는 것 뿐만 아니라 MAC 헤더를 생성 및 검증하기도 한다.  

이러한 2 계층 아키텍처의 motivation은 MLD가 한 링크에서 다른 링크로 트래픽을 이동하여 상위 계층에 완전히 투명하게(Network Transparency) 할 수 있다는 것이다.  
따라서 로드 밸런싱 기술은 가용한 모든 자원을 활용하여 각 클라이언트의 전송이 오직 하나의 대역 또는 다른 대역에서만 수행되는 현재 표준화된 다중 대역 접근 방식의 스펙트럼 사용의 비효율성을 최소화할 수 있다.  

하지만 이러한 아키텍처는 더 복잡한 설계를 수반한다. 트래픽에 대한 링크 할당에 대한 새로운 정책을 설계해야할 뿐만 아니라 여러 링크에서 채널 경쟁 및 패킷 전송이 수행되는 방식에 대한 low-level 측면을 재고하여야 한다.

#### Notes
AP, STAs을 단일 장치에서 여러 인터 페이스를 통해 여러 채널에 데이터를 송수신할 수 있는 MLD(Multi-Link Devices)로 재정의  
MLDs 각 인터페이스의 독립적인 파라미터를 잃지 않은 채 고유한 MAC 인스턴스를 상위 계층으로 전달하기 위해 MAC sub-layers의 기능에 따라 
U-MAC, L-MAC 두 개의 서브 계층으로 나누기를 제안함.

MSDU
: 상위 계층의 데이터를 MAC 계층에서 처리하기 위해 사용되는 데이터 단위로, MAC 계층으로 하달된 MSDU는 프레임으로 구성되어 PHY 계층을 통해 송신됨.



**U-MAC**
시퀀스 넘버 할당 및 MSDU aggregation 및 aggregation 해제, 데이터 및 QoS 관리 등의 링크와는 무관한 작업을 처리함.  
시퀀스 넘버 할당이 U-MAC에서 이루어지는 이유는 MLO를 수행함에 따라 동일한 트래픽 흐름에 속하는 패킷(가령 송신하고자 하는 데이터의 전체 스트림)이 상이한 링크를 통해 fragment되어 전송될 수 있어 
수신 측에서 그 순서를 알 수 있도록 하여야 함. U-MAC은 전체적인 설정과 같은 기능을 담당 L-MAC은 링크 각각에 대한 설정을 가지기에 U-MAC에서 시퀀스 넘버를 할당하여 L-MAC으로 하달하여야 함.
_U-MAC과 L-MAC은 투명함!_ L-MAC에서는 설정된 시퀀스 넘버에 따라 각 링크로 전송 해당 링크만 유지하면 됨. 이를 통해 수신 측은 수신 순서가 다르더라도 시퀀스 넘버를 통해 재조립할 수 있음.


**L-MAC**
인터페이스의 독립적인 하위 계층으로, 채널 액세스와 같은 링크와 관련된 작업들을 처리함. 개별적인 L-MAC 인스턴스를 가짐으로써 필요한 경우 채널 파라미터를 유지할 수 있음.
그 이유로는 로밍, DFS(Dynamic Frequency Selection) 등의 네트워크 변동사항에 대처하기 위함임.


**Motivation**
MLO를 통해 여러 링크로 나누어 데이터를 송수신함. 구현 역시 U-MAC과 L-MAC 역시 공통 관리 기능과 각 링크 관리에 대한 기능으로 서브 계층으로 나눔으로써 각 계층이 서로에게 투명하도록 함.  
이러한 로드 밸런싱으로 각 전송이 오직 하나의 링크만을 이용하던 기존 ax와 같은 표준의 비효율적인 스펙트럼 사용을 개선함.

---

### Transmission Modes
be 태스크 그룹은 MLDs를 두 가지의 다른 transmission mode로 정의 했다.
첫째, 비동기 tranmission mode는 MLD가 여러 링크에 비동기적으로 프레임을 전송할 수 있도록 한다. 
본 모드에서는 각 인터페이스는 다른 인터페이스를 존중하는 독립적인 동작과 함께 자신의 고유한 channel access 파라미터를 유지한다. 
또한 STR 기능을 허용하여 업 링크(UL)과 다운 링크(DL) 통신을 동시에 가능하도록 한다. 
이상적으로, asynchronous transmission mode는 더 높은 throughput 성능을 제공하기에 802.11을 따르는 모든 노드에 의해 기본 operation 방식으로서 선택되어야 한다고 제안된다. 
하지만 이러한 operation 방식은 특히 handheld 기기에서 동시에 동작하는 여러 비동기 인터페이스들로 인해 전력 소비가 극심하므로, power save 매커니즘을 따라야만 한다. 
추가적으로 비동기 operation은 IDC(In-Device Coexistence) interference에 의해 성능이 제한될 수 있다. 
즉, 인터페이스 간의 power leakage는 동작 대역/채널(ex) 5GHz 대역의 두 채널) 간의 분리가 충분하지 않은 경우 한 인터페이스에서 transmission 중일 때 다른 인터페이스에서 진행 중인 프레임 수신을 막게 될 수 있다.   

IDC 이슈를 피하기 위해서 be 태스크 그룹은 가용한 링크에서 동기화된 프레임 transmission을 수행하는 sychronous transmission mode를 정의하였다. 
synchronous trasmission mode에서 동작하는 장치는 다른 장치를 통해 수신하는 동시에 idle 인터페이스를 통해 전송하는 것을 허용하지 않기 때문에 constrained MLDs 또는 non-STR MLDs라고 한다. 
동기화를 수행하기 위해서 end-time alignment 또는 지연 전송 메커니즘이 구현될 수 있다. 
asynchronous mode는 동시에 다른 채널에서 transmission을 종료하는 것에 의존하는 반면, sychronous mode는 백오프를 완료한 링크의 전송을 다른 링크에서 동일한 카운터가 종료될 때까지 연기한다. 
이를 통해 AP 또는 스테이션이 STR operation을 수행하는 것을 방지하여 IDC 문제를 피할 수 있으나, 비동기 방식에 비하여 그 throughput이 낮다. 

채널 액세스와 관련하여 SPC(Single Primary Channel) 또는 MPC(Multiple Primary Channel) 방법론을 따를 수도 있다. 
기본적으로 SPC는 하나의 채널 내에 접근하기 위해 경쟁하지만, MPC는 각 모든 채널에서 경쟁이 일어난다. 
MPC 방식의 적용을 통해 노드가 경쟁에서 이기고 프레임을 전송할 수 있는 가능성이 높아지지만, SPC는 non-primary channel 인터페이스가 doze 상태로 유지되어 전력 소비를 줄일 수 있다. 
SPC 또는 MPC 방법을 사용하여 인터페이스가 해당 채널의 경쟁에서 이길 경우 PIFS 시간 동안 다른 인터페이스를 확인하여, 이들이 aggregation될 수 있는지 확인, TXOP aggregation를 수행한다. 
마지막으로 MLD를 지원하는 AP는 언제든 transmission 모드를 변경(synchronous to asynchronous, and vice versa)할 수 있다.

#### Notes

MLDs의 transmission mode를 두 가지로 정의
- asynchronous transmission mode
- synchronous transmission mode

MLDs를 지원하는 AP는 언제든 위 두가지 transmission mode를 변경할 수 있음.

**asynchronous trasmission mode**
asynchronous transmission mode에서 각 인터페이스는 다른 인터페이스의 동작에 영향을 주지 않도록 독립적으로 동작하며, 필요에 따라 자신의 고유한 channel access 파라미터를 유지함. 
STR 기능을 허용하여 업 링크와 다운 링크 통신을 동시에 가능하도록 함. 본 눈문에서는 asynchronous trasmission mode가 높은 throughput을 제공하기에 802.11을 따르는 모든 노드에 기본 operation으로 선택되어야 한다고 제안함. 
하지만 asynchronous transmission mode는 특히 handheld 기기에서 와이파이, 셀룰러, BT 등 동시에 동작하는 여러 비동기 인터페이스들로 인해 전력 소비가 심하여 power save 메커니즘을 따라야만 함. 
또한 같은 이유로 발생하는 IDC(In-Device Coexistence) interference로 인해 성능이 제한될 수 있음. 동작 대역/채널 간의 분리(인접한 채널을 사용하는 경우)가 충분하지 않은 경우 이러한 power leakage가 다른 인터페이스에서 진행 중인 프레임의 수신을 방해할 수 있음. 


**synchronous transmission mode**
be 태스크 그룹은 전술한 IDC 이슈를 피하기 위해 가용한 링크에서 동기화된 프레임을 송신하는 sychronous transmission mode를 정의하였음. 
본 모드에서 동작하는 장치는 다른 장치를 통해 수신하는 동시에 idle 인터페이스를 통해 전송하는 것이 허용되지 않음. 즉, 동시에 송수신이 불가함. 이를 constrained MLDs/non-STR MLDs라 함. 
이러한 동기화를 수행하기 위해서는 데이터 전송의 종료 시점을 세밀하게 조정하는 end-time alignment 또는 지연 전송 메커니즘이 구현될 수 있음. 
asynchronous mode에서는 여러 채널에서 동시에 transmission이 종료될 수 있는 반면, synchronous mode에서는 백오프를 완료한 링크의 전송 역시 다른 링크에 존재하는 동기화된 카운터가 종료될 때까지 전송하지 않음. => defer transmission mode 
이를 통해 AP 또는 스테이션이 동시 송수신(STR operation)하는 것을 방지하여 IDC 이슈를 피할 수 있으나, 이로 인해 비동기 방식에 비하여 throughput이 낮음. 

**SPC(Single Primary Channel), MPC(Multiple Primary Channel)**
SPC는 모든 데이터 전송이 하나의 주 채널을 통해 이루어 지는 것, MPC는 여러 주 채널을 통해 데이터를 전송하는 것으로, SPC에서는 하나의 채널에 접근하기 위해, MPC에서는 모든 채널에 접근하기 위해 TXOP 경쟁이 이루어짐. 
데이터 전송에 있어 SPC 또는 MPC에서 특정 인터페이스가 경쟁에서 이길 경우 PIFS 시간 동안 다른 인터페이스들이 자신의 전송 시간에 함께 데이터를 전송할 수 있을지를 확인하고 하나의 패킷으로 함께 전송하는데, 이를 **TXOP aggregation**이라고 함. 

---

### Management

The Multi-Link Element:  
상이한 management frames에 포함된 정보 요소(IEs)를 통해 장치는 기능과 동작과 관련된 매개변수를 교환할 수 있다. 
이를 위해 802.11be는 MLE(Multi-Link Element)를 정의한다. MLE는 다양한 Management에 대한 공통 요소로 설계 되었다. 
그러한 구현을 위해서, MLE는 control 필드 내에 각 작업을 특정 값으로 매핑하는 type sub-filed를 도입한다. 
따라서 이 정보 필드는 유형에 따라 다르며, presence bitmap에 의해 표현된다. 이러한 기능은 프레임이 과도하게 커지는 것을 피하고, 오버헤드를 최소화 하며 유형별 정보를 전달할 수 있는 유연한 구조를 제공한다. 

현재 802.11be 개정판에서는 두 가지의 MLE 유형을 정의하고 있다. 먼저 비콘 프레임에 사용하고자 하는 basic type이 있다. 
이 유형에서 MLE는 모든 인터페이스에 공통된 정보(MLD MAC 주소, 활성화된 링크 또는 STR 기능)만을 전달한다. 
두 번째는 멀티 링크 셋업 시 사용할 것으로 예상되는 multi-link request/response type이 있다. 
이 타입에서 MLE는 공통된 정보와는 별도로 개별적이고 독립적인 필드를 통해 advertising interface와 다른 인터페이스의 완전한 정보를 포함한다. 이때 주어진 인터페이스의 필드에 명시되지 않은 파라미터의 값은 advertising interface의 해당 파라미터와 동일한 값을 가지는 것으로 간주된다. 
예를 들어, 일부 명시된 매개변수는 채널 할당(주 채널 및 대역폭) 및 사용 가능한 공간 스트림의 수를 포함한다. 
현재 정의된 유형은 두 가지뿐이지만 MLE 유형이 추가될 수 있다. 예를 들어 제안은 TIM(Traffic Indication Map)을 보고하거나 TID 값과 링크 간의 매핑에 관한 변경 사항을 나타내는 방식으로 버퍼링된 트래픽 정보를 포함하는 것을 모색하고 있다. 


Discovery and Setup:  
802.11be discovery 메커니즘은 802.11 표준에 이미 정의된 것과 동일한 원칙을 재사용한다. 
즉 스테이션은 passive/active scanning을 기반으로 검색 프로세스를 수행, 주변 AP의 정보를 수집할 수 있다. 
그러나 MLD의 도입을 위해 일부 업데이트가 필요하다. 위에서 설명한 바와 같이 비콘과 probe 프레임은 멀티 링크 수준에서 부분적인 정보(U-MAC 관련 정보)만을 포함한다. 
그러나 이 구현 방식은 멀티 링크 셋업을 수행하기 전에 MLD의 모든 인터페이스를 스캔해야 하기 때문에 검색 프로세스에 많은 시간이 소요될 수 있다. 
이를 방지하기 위해 802.11be는 이미 정의된 RNR(Reduced Neighbor Report) 요소를 재사용하여 동일한 AP MLD의 서로 다른 인터페이스에 대한 몇 가지 기본 정보를 나타낸다. 
이러한 정보는 비콘 프레임을 전송하지 않는 인터페이스에만 속하게 된다. 이를 통해, 스테이션은 AP MLD의 다른 인터페이스의 전체 기능, 파라미터 및 운영 요소를 요청하여 직접 프로브할 수 있다. 
이러한 프로빙을 수행하기 위해 스테이션은 multi-link request/response type을 사용하여야 한다. 이러한 접근은 장치가 추가적인 멀티 링크 요청/응답을 보내야 하므로 비효율적으로 보일 수 있다. 
하지만 실제로는 non-AP MLD가 여러 연결을 활성화할 필요가 없기에(즉, AP MLD의 다른 밴드/채널을 스캔할 필요가 없기에) 에너지를 절약하게 된다. 
또한 이 방법을 사용하면 관리 프레임의 air-time 점유율을 줄이고, 스테이션이 탐색 과정에서 멀티 링크 셋업으로 전환하는 데 필요한 시간을 단축 시킨다. 
설정 과정과 관련하여 802.11be는 현재의 연결 요청/응답 프레임을 추가 MLE와 함께 재사용할 것이다. 그 다음 MLE를 통해 AP MLD와 STA MLD는 자신들의 기능을 교환하여 이후의 운영 방식을 설정한다. 
또한 멀티 링크 셋업 프로세스는 오버헤드를 줄이기 위해 단일 링크에서만 수행되도록 제안된다. 각 STA MLD에 대해 활성화된 링크의 집합은 모든 인터페이스에서 연결 상태를 측정하여 결정된다는 점을 언급할 필요가 있다. 
즉, CCA(Clear Channel Assessment) threshold 이상의 품질 값을 갖는 것은 활성화, 아닌 것은 비활성화 된다. 
사용자가 이동성을 가질 수 있기에 비활성화된 링크들은 리스트화 하여 이후 재설정을 요청하여 추가될 수 있다. 마찬가지로, 재설정 과정은 이미 정의 된 재연결 요청/응답 프레임을 재사용한다. 


Link Management:  
현재 multi-band AP에는 서로 다른 TID에 속하는 MSDU가 여러 링크를 통해 전송될 수 없는 주요 제한 사항이 있다. 
TID는 서로 다른 트래픽 유형을 QoS 요구 사항에 따라 분류하고, 이를 통해 사용자 간 우선 순위를 설정하기 위해 만들어졌다는 점이 중요하다. 
지난 개정안에서는 이러한 사용자 우선 순위를 사용하여 EDCA를 통해 차별화 및 우선 순위를 제공했다. 이는 각 데이터 패킷을 액세스 카테고리로 분류하고, 이를 각각 고유한 MAC 파라미터를 가진 특정 MAC 전송 큐에 연결함으로써 이루어졌다. 
그러나 802.11ax 개정에서 2.4GHz와 5GHz 대역을 모두 사용하더라도 모든 TID는 여전히 단일 링크 operation에 묶여있었다. 802.11be에서는 MLO의 도입으로 이러한 변화를 촉진한다. 
기본적으로 AP MLD는 모든 TID를 모든 링크에 매핑하는 것이 권장되며, 이는 스테이션이 임의의 링크를 통해 모든 유형의 트래픽을 검색할 수 있음을 의미한다. 
그러나 AP가 dynamic TID transfer를 수행함으로써 한 링크에서 다른 링크로 원활하게 TID를 이동할 수 있으므로, 이러한 조건이 반드시 정적인 것은 아니다. 
이러한 맥락에서 link management 기법은 클라이언트 steering을 수행하지 않고도 과도한 수준의 링크 혼잡을 방지하는 load balancing 메커니즘으로 수행될 수 있다. 실제로 이 기능은 로드 밸런싱 분야에서 새로운 연구 기회를 열어 준다.


#### Notes
MLE(The Multi-Link Element):  
MLE는 Management에 대한 공통 요소들로 설계 되어 있음. 이를 통해 상이한 management frames에 포함된 정보 요소(IEs)를 통해 장치는 기능과 동작과 관련된 매개변수를 교환할 수 있음. 
이를 구현하기 위해 MLE는 control 필드 내에 각 작업을 특정 값으로 매핑하는 type sub-field를 도입함. 
본 정보 필드는 management 유형에 따라 다르며, presence bitmap으로 표현됨. presence bitmap은 각 비트가 특정 속성이 존재하는지를 나타내는 것임. 
이를 통해 프레임의 과도한 크기 증가, 오버헤드를 최소화 하고 유연한 구조를 갖도록 함.  
802.11be 개정판에서는 **basic type**과 **multi-link request/response type** 두 가지의 MLE 유형을 정의함. 

**basic type**  
MLE는 모든 인터페이스에 MLD MAC 주소, 활성화된 링크 등 모든 인터페이스에 공통된 정보를 전달하는 MLE임.  

**multi-link request/response type**  
멀티 링크 셋업 시 사용될 수 있는 MLE로, basic type과 달리 각 링크에 대한 개별적인 정보와 기능을 전달하는 MLE임. 
이때 별도로 명시되지 않은 파라미터는 advertising interface의 파라미터와 같은 값으로 간주함.

버퍼링된 트래픽 정보 등을 담고 있는 새로운 MLE 유형이 추가될 수 있음.



Discovery and Setup:  
be의 discovery 메커니즘은 기존 802.11의 표준과 같이 스테이션이 passive/active scanning으로 검색 프로세스를 수행, 주변 AP 정보를 수집함.
하지만 MLD의 도입을 위해 beacon frame, probe frame은 전체가 공통적으로 가지는 네트워크 전반에 대한 정보이므로 U-MAC과 관련된 부분적인 정보만을 포함하도록 추가적인 업데이트가 필요함. 
이러한 업데이트로 인해 인터페이스 개별적인 링크에 대한 정보가 부족하여 멀티 링크 셋업을 수행하기 전에 MLD의 모든 인터페이스를 스캔해야 한다는 점에서 많은 시간이 소요됨. 
이를 방지하기 위해 be에서는 AP MLD의 상이한 인터페이스에 대해 몇 가지 기본 정보를 나타내는 RNR(Reduced Neighbor Report) 요소를 재사용함. 
MLD에서는 여러 주파수 대역을 사용할 수 있으므로, 다른 주파수 대역(인터페이스)에서는 비콘 프레임이 전송되지 않을 수 있음. 
비콘 프레임을 전송하지 않는 인터페이스의 정보는 RNR 요소를 통해 인터페이스의 전체 기능, 파라미터 운영 요소를 요청하여 직접 프로브할 수 있음. 
이 과정에서 multi-link request/response type을 사용하여야 하기에 추가적인 작업으로 비효율적으로 보일 수 있으나 
이를 통해 인터페이스 전체를 스캔하는 과정이 없어짐에 따라 관리 프레임의 air-time 점유율을 줄여 스테이션이 탐색 과정에서 멀티 링크 셋업으로 전환하는 데에 필요한 시간을 단축 시킴.  

1. STA가 AP MLD로부터 2.4GHz 대역에서 AP MLD의 비콘 프레임을 수신함.(5GHz 대역에 대한 정보를 RNR 요소로 확인 가능)
2. STA가 AP MLD로 multi-link request 프레임을 전송하여 5GHz 대역의 전체 기능, 파라미터 및 운영 요소를 요청(multi-link request/response MLE 타입 사용)
3. AP MLD는 multi-link response 프레임을 통해 5GHz 대역의 상세 정보를 STA에 제공함.
4. 두 대역 모두에서 multi-link 연결 설정


Link Management:  
TID는 트래픽 유형을 QoS 요구 사항에 따라 분류하는 식별자임. 가령, 음성 비디오, 데이터 등의 다양한 트래픽 유형에 우선 순위를 설정하고 관리하기 위함. 
기존 표준인 ax에서는 2.4GHz, 5GHz 대역을 모두 사용할 수는 있으나 TID는 단일 링크에 묶여 있어, 특정 TID에 속하는 트래픽은 단일 링크를 통해서만 정송되었음. 
be에서는 MLO의 도입으로 TID를 모든 링크에 매핑하여 임의의 링크에 모든 유형의 트래픽을 태워 전송할 수 있도록함. **load balancing**으로서 사용 가능함.

---

### Power Save
오늘날 인터넷 연결은 주로 휴대용 장치를 통해 수행되기 때문에 전력 관련 소비 문제를 신중하게 고려해야 한다. 
이러한 문제를 해결하기 위해 be 태스크 그룹은 TIM(Traffic Indication Map)과 TWT(Target Wake Time)의 사용을 채택하고 조정할 것을 제안하였다. 

TIM(Traffic Indication Map):  
AP가 연결된 스테이션에 버퍼된 데이터가 있음을 알리는 역할으로, AP는 TIM을 비콘 프레임에 포함해 주기적으로 브로드캐스트한다. 
TIM 요소에서, AP들은 2007 bits로 형성된 비트맵을 포함하고, 각 비트는 스테이션들에 대응한다. 
가령, 주어진 스테이션에 해당하는 비트맵의 비트가 0이면 스테이션은 doze 상태로 유지되는 반면, 
1이면 AP에서 데이터를 검색할 준비가 되어 awake 상태로 전환된다. 
단일 링크 스테이션에서는 TIM 메커니즘이 작동하였으나, MLD의 도입으로 수정되어야 했다. 
be 태스크 그룹은 스테이션이 연결될 수 있는 모든 multi-link에 대한 정보를 포함하기 위해 TIM 요소 뒤에 링크 표시 필드를 추가할 것을 제안하였다. 
링크 표시 필드 내에는 link mapping bitmap이 포함되며, 여기서 각 비트는 지정된 링크를 나타낸다. 
따라서 STA MLD가 TIM 요소에서 해당 비트가 1로 설정된 것을 감지하면 STA MLD는 링크 매핑을 추가로 확인하여 버퍼링된 트래픽에 매핑된 특정 링크를 찾는다. 
본 메커니즘에서는 기존의 TIM을 확장하여 보다 효율적인 기능을 제공한다. 이를 통해 스테이션은 특정 시간대에만 특정 인터페이스를 깨어나도록 하여 전력 소비를 최소화하고 배터리 수명을 연장할 수 있다. 


TWT(Target Wake Time):  
AP와 STA가 깨어나서 통신하는 시간을 사전에 협상하고 조정하도록 하여 효율을 높이는 방법이다.
TWT는 802.11ah 개정판에 처음 포함된 전력 절약 메커니즘이며, 802.11ax 개정판에서 더욱 발전되었다. 
본 메커니즘은 초기 협상에 기반하여 동작하며, 이 협상 과정에서 스테이션과 AP는 공통의 wake 스케줄링(즉, 스테이션이 데이터를 전송하거나 수신할 수 있는 세션 기간(Session Period, SP) 또는 TWT 세션)에 합의한다. 이를 통해 스테이션은 데이터 전송 및 수신을 할 수 있다. 
이를 구현하기 위해 TWT는 초기 협상 단계부터 SP 파라미터를 결정해야 한다. be 태스크 그룹은 MLO 프레임워크에서 TWT 작업을 효율적으로 해결하기 위해 단일 링크를 통해 서로 다른 활성화된 링크에 대해 TWT 합의를 수행할 것을 제안한다. 
이를 위해 STA MLD는 비트맵을 통해 식별되는 특정 링크에 대응하는 서로 다른 TWT 요소를 TWT 요청에 포함한다. 
이는 링크들이 wake up time, wake interval 또는 최소 wake 지속 시간과 같은 상이한 TWT 파라미터들을 가질 수 있기 때문에 필요하다. 
반대로, 동일한 파라미터들이 모든 링크들에 적용되는 경우, 하나의 TWT 요소만을 필요로 한다. 
TIM뿐만 아니라 TWT에서도 스테이션은 필요할 때 깨어있는 상태에서 doze 상태로 전환하여 전력 소비를 줄일 수 있다. 
TWT의 채택이 다른 성능 영향을 미칠 수도 있으나 현재까지 이러한 문제와 관련된 작업은 없다. 


#### Notes
오늘날의 인터넷 연결은 주로 휴대용 장치를 통해 수행되기에 전력 소비 문제가 중요함. 
이를 해결하기 위해 be 태스크 그룹은 TIM(Traffic Indication Map)과 TWT(Target Wake Time)의 사용을 채택하고 조정할 것을 제안함. 

**TIM(Traffic Indication Map)**:  
AP가 연결된 스테이션에게 버퍼된 데이터가 있음을 알리는 역할임.  
AP는 TIM을 비콘 프레임에 담아 주기적으로 브로드 캐스트하며, TIM의 요소에는 2007 비트의 비트맵이 포함되어 있으며, 각 비트는 스테이션에 대응함.  
특정 스테이션에 해당하는 비트맵의 비트가 0이면 doze, 1이면 wake 상태임.  
MLD의 도입으로 TIM 메커니즘의 수정도 필요.  
스테이션이 연결될 수 있는 모든 multi-link에 대한 정보를 포함하기 위해 TIM 요소 뒤에 링크 표시 필드를 추가함.  
링크 표시 필드 내에는 지정된 링크를 각 비트로 나타내는 link mapping bitmap이 포함됨.  
1. STA MLD가 AP로부터 비콘 프레임을 받고 안에 있는 TIM 요소에서 비트가 1로 설정된 것을 감지하면,
2. 링크 매핑을 추가로 확인하여 버퍼링된 트래픽에 매핑된 링크를 찾음.

기존의 TIM에서 확장된 본 메커니즘으로 스테이션은 특정 시간대에만 특정 인터페이스를 깨움으로써 전력 소비를 줄일 수 있음.  


**TWT(Target Wake Time)**:  
AP와 STA가 깨어나 통신하는 시간을 사전에 협상하고 조정하도록 하여 효율을 높이는 방법임.  
초기에 스테이션과 AP는 스테이션이 데이터를 송수신할 수 있는 시간 SP를 협상하고, 이 시간 동안에만 송수신을 수행함. => wake scheduling  
이를 위해 TWT는 초기 협상 단계부터 SP 파라미터를 협상해야 하며, 단일 링크에서 해당 스테이션에 활성화된 모든 인터페이스에 대한 TWT 합의를 수행하여 효율성을 높임.  
이를 위해 STA MLD는 비트맵을 통해 식별되는 특정 링크의 TWT 요소를 자신의 TWT 요청에 포함함. 각각의 링크는 서로 다른 wake up time, interval, 최소 wake 지속 시간 등의 상이한 TWT 파라미터를 가질 수 있기 때문임.  
반대로, 동일한 파라미터가 모든 링크에 적용될 때에는 하나의 TWT 요소로 충분함.  
TIM과 같이 TWT에서도 스테이션은 필요할 때 깨어있는 상태에서 doze 상태로 전환하여 전력 소비를 줄일 수 있음.  

---

## Traffic Management
여러 인터페이스를 가진 AP와 스테이션이 존재하기 때문에 트래픽 flow 할당은 MLO에서 어려운 부분이다. 
이와 관련하여 802.11be U-MAC 구현은 특정 L-MAC에 버퍼링된 데이터를 배포하기 위해 트래픽 관리자에 의존해야 한다. 
인터페이스를 효율적으로 사용하는 것은 네트워크 성능 측면에서 매우 중요한 역할을 할 것이기 때문이다. 
이를 위해 일련의 정책을 도입하였다. 먼저 MLSA(Multi Link Same Load to All Interface)는 모든 인터페이스에 트래픽을 동일하게 할당한다. 
하지만 이러한 작업은 채널 점유율을 고려하지 않고 결정하기 때문에 매우 비효율적이다. 
다음으로 SLCI(Single Link Less Congestion Interface)와 MCAA(Multi-link Congestion-Aware Load Balancing at Flow Arrivals) 정책도 도입하였다. 
이들은 단일 또는 여러 대역에 트래픽을 할당하기 위해 링크 점유율 측정에 의거하므로, 예상대로 상당한 성능 향상을 가져왔다. 
따라서 트래픽 결정은 자체 트래픽 부하뿐만 아니라 채널의 순간적인 점유율을 고려하여야 함을 보여 주었다. 
그러나 제안된 메커니즘의 non-adaptive 구현은 링크의 점유율이 매우 빠르게 변할 수 있으므로, 장기간의 flow에서는 효율적이지 않을 수 있다. 
이러한 맥락에서 흐름이 활성화될 때 각 인터페이스의 순간적인 채널 점유율을 고려할 뿐만 아니라 지속적으로 추적하여 변경 사항이 발생할 때 트래픽을 동적으로 재할당할 수 있는 동적인 전략의 채택을 추가적으로 연구할 예정이다. 

이전의 전략에서는 모든 TID가 여러 인터페이스에 매핑되는 것으로 간주되었지만, MLO는 TID-to-link 매핑 기능을 통해 링크 기반 트래픽 분리를 수행할 수 있는 가능성을 열어 준다. 
즉, 시간에 민감한 트래픽에 대한 액세스 지연을 최소화하기 위해 서로 다른 TID를 서로 다른 링크에 매핑할 수 있다. 
게다가 이러한 기능은 노드의 공간적 분포가 특히 5GHz 6GHz 대역에서 유리한 전파 조건으로 인해 서로 다른 contention-free 링크를 생성할 수 있다는 점으로 보완될 수 있다. 
따라서 높은 QoS 요구사항이 있는 트래픽은 이러한 contention-free 링크가 존재하는 한 독점적으로 교환할 수 있다. 
TID-to-link 매핑 전략의 적용에서 장점과 단점을 보여주기 위해 이 글에서는 서로 다른 유형의 트래픽 흐름을 구분하는 새로운 트래픽 할당 정책을 소개한다. 
즉, 데이터 흐름에 해당하는 트래픽은 비디오 흐름과 다른 링크에 할당된다. 
이 정책을 VDS(Video and Data Separation)이라고 하며, 데이터 흐름은 2.4GHz 또는 5GHz 대역에 할당하고, 비디오 흐름은 6GHz 대역에 할당한다. 
VDS는 여러 인터페이스에 데이터 흐름을 분산하지 않고 전체 트래픽을 단일 인터페이스(즉, 2.4GHz 또는 5GHz 대역 중 가장 빈 인터페이스)에 할당한다. 

### Notes
MLO는 여러 인터페이스를 가진 AP와 스테이션이 존재하는 환경이기에 트래픽 flow의 할당이 어렵고 중요함.  
802.11be에서는 이와 관련된 일련의 정책을 도입하였는데, MLSA(Multi Link Same Load to All Interface), 
SLCI(Single Link Less Congestion Interface), 
MCAA(Multi-link Congestion-Aware Load Balancing at Flow Arrivals)가 이에 해당함.

**MLSA(Multi Link Same Load to All Interface)**
: 모든 인터페이스에 트래픽을 동일하게 할당 => 채널 점유율을 고려하지 못하여 비효율적임.



**SLCI(Single Link Less Congestion Interface)**
: 단일 링크에 트래픽을 할당하되, 현재 가장 혼잡이 적은 링크에 트래픽을 할당하는 방식  



**MCAA(Multi-link Congestion-Aware Load Balancing at Flow Arrivals)**
: 멀티 링크에서 새로운 트래픽 흐름이 발생할 때, 각 링크의 혼잡 상태를 고려하여 트래픽을 분산하는 방식  
링크의 혼잡 상태를 실시간으로 평가, 트래픽을 분산하는 과정으로 이루어짐.



SLCI와 MCAA는 MLSA와 달리 링크의 혼잡한 정도와 같은 환경을 고려하여 트래픽을 할당하여 상당한 성능 향상을 보임.  
트래픽 할당은 시스템의 전체 트래픽 처리양뿐만이 아니라, 순간적으로 요구되는 트래픽 처리의 양까지 고려하여서 수행하여야 함을 의미함.  
하지만 위와 같은 구현은 non-adaptive 방식이라 순간적인 트래픽 처리의 양이 매우 빠르게 변화하는 환경에서는 장기적으로 효율적이지 않을 수 있음.   
따라서 순간적인 트래픽 처리의 양을 고려하되 이를 지속적으로 추적하여 변경사항이 있을 시 대처할 수 있는 동적인 정책의 채택을 추가적으로 연구할 예정임.   


SLCI, MCAA, MLSA는 특정 인터페이스의 부하를 막기 위해 모든 TID를 여러 인터페이스에 매핑하여 트래픽을 처리하였음.   
하지만 MLO에서는 TID-to-link 매핑 기능을 통헤 링크 별로 상이한 TID를 매핑하여 처리할 수 있도록 함.  
가령, 기존에는 2.4GHz, 5GHz 모두에서 모든 TID를 처리할 수 있었다면, MLO에서는 비디오 스트리밍은 6GHz, 일반 데이터 전송 트래픽은 2.4GHz 또는 5GHz에 할당함으로써 높은 QoS를 보일 수 있음.  
이러한 정책을 **VDS(Video and Data Separation)**이라 함.


---

## Performance Evaluation

그림에서 볼 수 있듯 VDS 정책은 평가된 전체 시나리오의 50%에 대해서는 Video flows, Data flows에서 평균 throughput losses를 5% 미만의 값을 보였으나, 
최악의 경우 거의 40%에 육박하는 평균 손실을 보여 MSLA 정책보다 낮은 성능을 보였다. 이는 VDS 정책의 중요한 단점을 보여준다. 
VDS의 트래픽 분리는 인접 BSS가 겹치거나 트래픽 시나리오가 높은 조건에서 심각한 성능 문제를 겪을 수 있지만, SLCI 및 MCAA 정책은 활성 링크 간의 트래픽 부하 균형을 맞출 수 있기에 
시나리오 75%의 문제에서 이를 극복할 수 있음을 볼 수 있다.  

또한 기존 Multi-Band Single Link(MB-SL)과 Single Link(SL)를 통해 단일 링크가 아닌 다중 링크에 분산하는 것이 더 좋은 성능을 보이며, 
그럼에도 MLO의 정책들이 모든 시나리오에서 더 좋은 성능을 보이는 것을 확인할 수 있었다.


---

## Open Issues and Challenges
MLO는 차세대 무선랜에서 구현될 유망한 기능을 나타내지만, 여러 인터페이스를 동시에 사용하면 새로운 과제에 직면하게 된다. 
이러한 맥락에서 추가 연구가 필요한 몇 가지 미해결 문제를 지적한다. 

**Non-STR and Legacy Blindness**:  
이 문제는 non-STR 및 기존 STA가 그들의 제한된 운영으로, 다양한 충돌 시나리오를 유발할 수 있다는 것과 관련된다. 
첫째, non-STR STA는 다른 링크에서 전송을 수행하고 있기 때문에 사용 가능한 다른 링크에서의 DL, UL을 감지하지 못할 수 있다. 
따라서 non-STR STA가 이미 사용중인 링크에서 전송을 시도하면 충돌이 발생할 수 있다. 
이 문제는 AP MLD가 non-STR STA에게 AP MLD가 사용 중인 다른 링크의 채널 상태를 알리도록 하여 해결 되었다.
한편, 마찬가지로 레거시 장치들은 단일 링크에서만 동작하기 때문에 다른 링크에서 전송이 이루어지고 있는지 알지 못할 수 있다. 
따라서 non-STR 사례에서 제안된 것과 같은 지표가 필요하여 레거시 노드에게 다른 링크에서 일어나는 활동을 알려야 한다.  


**Spectrum Inefficiency**:  
IDC interference 또는 충돌을 피하기 위한 보수적인 접근 방식은 하나의 링크에서 백오프 절차를 중단하고, 
다른 링크에서 medium access가 허용되는 경우 스펙트럼의 비효율적인 사용으로 이어질 수 있다. 
이에 non-STR 노드의 스펙트럼 활용을 극대화하기 위해 채널 상태가 충돌이 발생하지 않음을 보장할 때만 전송 시도를 재개하는 opportunistic backoff 메커니즘을 제안하였다. 


**Channel Access Fairness**:  
MLO는 여러 링크에서 TXOP aggregation을 수행하기 때문에 단일 링크만 사용할 수 있는 노드들은 채널 접근이 더 어려워질 수 있다. 
따라서, legacy STA가 존재하는 경우, link aggregation 기술의 사용을 제한하거나 막아야 한다.


**Load Balancing**:  
로드 밸런싱이 글의 주제였으나, MLO WLAN에서 트래픽을 균형 있게 분산하기 위한 최적의 전략을 완전히 이해하기 위해서는 추가적인 연구가 필요하다. 
예로, MLO가 업 링크 트래픽에 어떻게 사용될 수 있는지도 고려하여야 하며, 이는 다운 링크와는 전혀 다른 접근 방식을 요구할 수 있다. 
이 측면에서, 로드 밸런싱 전략은 미래의 트래픽과 네트워크 동향을 예측하기 위해 머신러닝 솔루션을 사용할 때 이점을 얻을 수 있다.  


### Notes
MLO를 사용함에 따라 발생한 문제와 해결책, 미해결 문제  
1. Non-STR and Legacy Blindness
2. Spectrum Inefficiency
3. Channel Access Fairness
4. Load Balancing


**Non-STR and Legacy Blindness**:  
Non-STR STAs: MLO는 지원하나, 동시 송수신은 지원하지 않는 스테이션  
Legacy STAs: 이전 표준까지의 스테이션으로 MLO 자체를 지원하지 않을 수 있음.  
non-STR STA는 동시 송수신을 지원하지 않으므로, 송신 또는 수신 중일 때 다른 링크에서의 송수신을 감지하지 못할 수 있음.
가령, 1번 링크를 이용 중인 STA_A(non-STR STA)는 2번 링크가 사용되고 있음을 감지하지 못함. 이때 STA_A가 2번 링크에서 전송을 시도하면 충돌이 발생함.  
=> AP MLD가 STA_A에게 자신이 사용 중인 2번 링크의 채널 상태를 알리도록 하여 해결하였음.  


Legacy STA 는 단일 링크에서만 동작하므로, non-STR STA와 같이 다른 링크의 사용 여부를 알지 못할 수 있음.  
=> 같은 맥락의 해결책이 필요함.  


**Spectrum Inefficiency**:  
IDC interference 또는 충돌을 피하기 위해 사용되는 보수적인 접근론은 다른 링크에서 medium access가 허용될 경우 
기존 링크에서의 백오프 절차를 중단하게 되어 이러한 과정이 스펙트럼 활용 효율을 낮추게 됨.  
=> 채널 상태가 충돌이 발생하지 않음이 보장될 때만 전송 시도를 재개하는 opportunistic backoff 메커니즘을 제안함.  


**Channel Access Fairness**:  
MLO는 여러 링크에서 TXOP aggregation을 수행함에 따라 단일 링크만 사용할 수 있는 노드들은 채널 접근이 더 어려워질 수 있음.   
그 이유는 TXOP aggregation 과정에서 여러 링크를 사용하는 노드에게 채널 접근의 우선권을 주기 때문에 접근 경쟁에서 밀림.  
=> legacy STA가 존재하는 경우, link aggregation의 사용을 제한하거나 막아야 함.


**Load Balancing**:  
MLO UL에서의 로드 밸런싱 전략은 다운 링크와 전혀 다른 접근 방식을 요구할 수 있기에 미래 트래픽과 네트워크 동향을 예측하는 머신러닝 솔루션과 함께 사용되도록 연구해야 함.  

---

> **시작했으면 끝은 봐야지!**

---

> ### Profile
>
>
> ***Seong Hun KIM***
>
>
> **Student**  
> **Dept. of Computer Science Engineering | Yeungnam University, Repulic of Korea**
>
> ![yu signature](https://github.com/HoonC-corgi/Convolution_Filter_Application/assets/118245330/37c81d9e-cfb8-4aee-8497-ff1071b2458b)
>
> **Phone** [010 - 6685 - 1140](tel:010-6685-1140)  
> **Mail** [tgh7544@naver.com](mailto:tgh7544@naver.com)  
> **LinkTree** [https://linktr.ee/HoonC_corgi](https://linktr.ee/HoonC_corgi)
