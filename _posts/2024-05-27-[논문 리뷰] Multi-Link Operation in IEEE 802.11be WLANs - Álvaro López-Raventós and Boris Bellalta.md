---
published: False
---

# **[논문 리뷰] Multi-Link Operation in IEEE 802.11be WLANs**
##  Álvaro López-Raventós and Boris Bellalta

-----

## 논문 리뷰를 시작하며

 연구실에서 진행하는 논문 세미나를 준비하는 과정에서 해당 내용들을 그냥 넘어가지 않고, 블로그에 남겨 놓는다면 추후에 내가 공부하면서 어려워 했던 부분이나,
 놓쳤던 부분을 다시 확인할 수 있을 것 같아 논문 리뷰 글도 블로그에 작성해 보기로 하였다.
 
오늘의 논문은 아래와 같다.

**Multi-Link Operation in IEEE 802.11be WLANs** - Álvaro López-Raventós and Boris Bellalta (IEEE Wireless Communications • August 2022)

---
## 한글 번역 및 코멘트
### Abstract

multi-link operation (MLO)은 IEEE 802.11 be Extremely High Throughput (EHT)의 개선안의 일부로 제안된 새로운 기능이다.

이러한 기능은 노드가 여러 무선 인터페이스를 통해 동시에 데이터를 송수신할 수 있게 됨으로써 multi-link 통신으로 패러다임 전환됨을 보인다.

이를 위해 802.11be의 태스크 그룹은 노드의 아키텍처, 전송 운영 및 관리 기능에 대한 다양한 수정 사안을 제안하였다.

본 기사에서는 이러한 변경사항과 여전히 해결되지 못한 트래픽이 다중 링크에서 어떻게 분산되어야 하는지에 대한 문제를 검토한다.

이를 위해 active links를 통해 상이한 load balancing 전략을 평가한다.

결과는 high load, high dense, 그리고 복잡한 시나리오에서 MLO를 사용하여 차세대 WLAN의 성능을 높이기 위한 congestion aware load balancing 전략이 필수적임을 시사한다.

---
#### notes

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
### Introduction

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
#### notes

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
### Multi-Link Operation

이어서 작성..