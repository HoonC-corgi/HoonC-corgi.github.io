---
title: "[연구] Cross Layer DASH using bss load"
published: False
excerpt: "연구 과정 정리"
---

---
[사전 준비 단계]
- GPAC(MP4Box)를 사용하여 표준 MPEG-DASH MPD 및 세그먼트 생성

[시뮬레이션 환경 구성]
- ns-3을 활용하여 Wi-Fi 네트워크 환경 구축
- IEEE 802.11ax/be로 설정, AP가 BSS Load 정보를 Beacon 프레임으로 브로드캐스팅하도록 구현

[클라이언트 구현 단계]
- Linux 환경의 DASH.js를 활용하여 DASH 플레이어 구현
- Linux Wireless (nl80211)를 이용하여 Beacon 프레임에서 BSS Load 정보를 클라이언트가 실시간 파싱
- 얻어진 BSS Load 정보를 DASH.js의 ABR 로직에 추가하여 품질을 결정하도록 구현

[성능 분석 단계]
- Wireshark를 이용하여 클라이언트에서 서버로의 HTTP 요청·응답 및 Wi-Fi Beacon 프레임 캡처
- 기존 ABR 방식과 제안된 BSS Load 기반 ABR 방식의 성능(QoE)을 지연, 버퍼링 횟수, 품질 안정성 등을 기준으로 비교 분석


---

```terminal
# MPEG-DASH MPD 및 세그먼트 생성 도구 gpac 다운
brew install gpac

# 테스트 비디오 파일 준비(Big Buck Bunny)
curl -O http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4

# 영상을 오디오/비디오로 분리
ffmpeg -i BigBuckBunny.mp4 -c:v copy -an BigBuckBunny_video.mp4 (-an: 오디오 제거)
ffmpeg -i BigBuckBunny.mp4 -c:a copy -vn BigBuckBunny_audio.mp4 (-vn: 비디오 제거)

# DASH용 MPD 및 세그먼트 생성
MP4Box -dash 4000 -frag 4000 -rap -profile live \
  -out BigBuckBunny.mpd \
  BigBuckBunny_video.mp4#video \
  BigBuckBunny_audio.mp4#audio
//
-dash 4000: 세그먼트 길이 4000ms (4초)
-frag 4000: 프래그먼트 길이도 동일하게 설정
-rap: 키프레임에서만 세그먼트 분할 (random access point)
-profile live: DASH 프로파일 지정
#video, #audio: 각각 비디오/오디오 Representation으로 설정 

정상적으로 수행되면 MPD 파일 내에 아래와 같은 구조가 나타나야 함..
<AdaptationSet contentType="video">...</AdaptationSet>
<AdaptationSet contentType="audio">...</AdaptationSet>
//


//
BigBuckBunny.mpd (DASH MPD 파일)
BigBuckBunny_dashinit.mp4 (초기화 파일)
BigBuckBunny_dash1.m4s, BigBuckBunny_dash2.m4s, … (세그먼트 파일들)
//


```

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Big Buck Bunny DASH 플레이어</title>
</head>
<body>
<video id="videoPlayer" controls width="800" height="450"></video>

<script src="https://cdn.dashjs.org/latest/dash.all.min.js"></script>
<script>
    var url = "BigBuckBunny.mpd";  // mpd 파일 경로
    var player = dashjs.MediaPlayer().create();
    player.initialize(document.querySelector("#videoPlayer"), url, true);
</script>
</body>
</html>
```

* $ python3 -m http.server 8000  
html 페이지 접속 시 영상 재생 가능

