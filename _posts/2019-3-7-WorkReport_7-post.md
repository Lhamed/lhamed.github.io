---
layout: post
title: WorkReport_4
description: "Fri,2019.3.4"
modified: 2019-3-4
tags: [WorkReport]
image:
  path: /images/Lawn.png
  feature: abstract-3.jpg
  credit: lhaemd
  creditlink: lhamed.github.io
---
# 오늘 목표 업무 
- [x] 기존 난독화 대신 Unity APK Proguard 난독화 
   - [x] Gradle Proguard 빌드에서 , 유난히 씬 로딩 시간이 길어지는 이슈 디버깅 하고
      - 2017.1.1 의 버그로 추정 ( 스택 오버 플로에서 찾음. )
      - [x] 2017.1.5 ( 2017.1의 최종 유니티 버전 )을 설치
      - [x] 이걸로 다시 Gradle 빌드
      - [x] 테스트 
   - [x] 위 사항 해결버전 재 빌드 
- [x] 새 버전 마이그레이션을 위한 API 변경 작업 
   - [ ] 빌드
      - 버그 : GPGS 가 제대로 작동하지 않는다. 
- [ ] 파이어베이스 푸시 알림 SDK 구현 ( 시간이 남는 다면 ) 

# 데이터 
시간 견적 정확도 : 0 hour

만족도 : 90 

목표 이행 : 90

퇴근 시 피로도 : 65

출근 시 피로도 : 98 ( 집에 가서 잘잠 )

업무 중 생각난 아이디어의 갯수 : 0

학습량 : 2

# 업무중 학습

- 다른 씬에 있는 매니저라도 , public static void 등으로 설정해두면 정상 작동 한다.. ? ( 기존 레거시 코드에서 그렇게 작동하는 중이라 신기)
   static 스트링 을 static 함수로 건드리는게 가능. 

- gradle 3.2 부터 app bundle 로 (apk 대신) 빌드가 가능하다. (이는 유니티 2017.1 의 그래들 버전은 안됨 )

- gralde 에서 Loading 시간이 폭발적으로 증가하는 이슈 ( 시간이 30~40초에서 5분 이상으로 증가함)
   - 해결 : android 항목 내에 
      aaptOptions {
      noCompress 'unity3d' // or whatever extension you use
      }
      를 추가하면 된다. 이 이슈는 streamingAsset 폴더내에서 자료를 읽어다가 게임에서 로드하고 있을 경우 인터널에서는 문제가 없다가 
      그래들에서 발생하는데, StreamingAsset 내부까지 그래들에서는 압축을 걸어버리기 때문에, 위처럼 unity3d 에서 관장하는 폴더는 건드리지 말라는 옵션을 걸어두어야 한다 . (aapt 는 안드로이드 에셋 패키지 툴 이다. )
   - 후기 : 내가 gradle 빌드를 처음해봐서, 생각보다 꼬인 점이 많다. 다 떠먹여주는 internal 빌드와 다르게 안드로이드 스튜디오를 이용한 gradle은 무척이나 삐걱대지만, 그만큼 커스텀 할 수 있는 여지가 매우 많아보여서 꼭 배워둬야 할 것 같다. 그래도 오늘 내로 해결해서 다행이다. 

# 내일로 미뤄진 작업 
- [x] 새 버전 마이그레이션을 위한 API 변경 작업 
   - [ ] 빌드
      - 버그 : GPGS 가 제대로 작동하지 않는다. 
- [ ] 파이어베이스 푸시 알림 SDK 구현 ( 시간이 남는 다면 )

# 궁금증
