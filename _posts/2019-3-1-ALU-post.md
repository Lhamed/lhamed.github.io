---
layout: post
title: ALU
description: "Fri,2019.2.16"
modified: 2019-2-24
tags: [cs, SelfStudy , Study , Note]
image:
  path: /images/Lawn.png
  feature: abstract-3.jpg
  credit: lhaemd
  creditlink: lhamed.github.io
---

# ALU ( Arithmetic logic unit, 산술 논리 유닛 )

ALU 는 컴퓨터의 부품 중에서 , 수와 논리의 연산을 담당한다. 

해당 영상에서는 

이 8 Bit ALU 를 기준으로 ALU 가 구성되는 것을 

논리 회로 (Logic Gate)의 조합으로 반가산기 (Half Adder)와 전가산기 (Full Adder), 

그리고 이들을 조합해서 리플 캐리 가산기 ( Ripple Carry Adder ) 등을 차례로 구현하면서 설명하는 강의였다. 

## 오늘도 신나는 야크세이빙 

일단 위의 개념들에서 

Logic Gate 는 모두들 알죠 ? 모르는 흑우는 없죠 ? 이런 식으로 강의가 시작되는데 

내가 바로 그 흑우입니다 선생님.. 

바로 공부했다. 의외로 옆에 있던 철학과 여자친구님께서 아는 내용 ( 논리학에서 거의 같은 개념을 쓴다. )

당연히 전부 불 대수 ( 1 or 0 , True Or False , 참 or 거짓 ..... ) 인풋과 아웃풋을 가지는

추상적인 논리를 컴퓨터 과학의 신묘한 능력으로 실제 전자회로로 구현한 것이다. 

철학과에서는 AND OR NOT 등이 가장 작은 단위이지만 , 컴퓨터과학이나 전기전자쪽에서 보기에는 더 구체회로로 쪼개진다. ( 정말 전선과 트랜지스터 등... )

추가로, 

수학에서 0 과 1 의 이진수 라면

논리학에서는 거짓과 참이고

전기전자학에서는 HIGH 와 LOW 이다. ( 아두이노할 떄 배웠지롱 ! )

컴퓨터공학에서는? 부르고 싶은 걸로 부르는거 같다 .( 주로 bool 로 표현하면 장땡이니 뭐 ㅎㅎ. 그리고 굳이 이거가지고 따질 이유도 없지. )

### Logic Gate

- NOT
  -  1 을 0 으로 , 0 을 1로 산출한다. 자연어 문법에서 부정어라고 부르는 친구들과 같다고 지금은 생각한다. (보통은 공부를 더하면 바뀌더라고..)
  -  전자공학에서는 inverter 라고 부르나보다. 해당 전공자 입장에서는 참이 거짓으로 뒤바뀐다기보다는 단순히 전기신호를 뒤집는 것일테니까 . 그러고보면 [철학(논리학) - 컴퓨터과학 - 전자공학 ] 을 이르는 어떤 스펙트럼이 있는 것이 아닐까. 추상세계와 물리세계 말이다. 
  - 당연히 인풋도 1개 , 아웃풋도 1개다. 

- AND 
  - 둘 다 1 이여야 아웃풋으로 1을 내보낸다. 
  - 수리 논리학에서는 논리곱이라고 부르기도 한다.  
  - 인풋 2개 , 아웃풋 1개  그래서 
  - 수학에서는 Conjunction 이라고 부르나보다. 

- OR
  - 인풋 2개 중 하나라도 1이라면, 1을 산출한다.
  - 수리 논리학에서는 논리합이라고 부르기도 한다. 
  - 인풋 2개, 아웃풋 1개 
  - 수학에서는 Disjunction 이라고 부른다. 

- NAND
  - AND 뒤에 NOT 붙인 결과를 낸다. 
  - 인풋 2개다 1이여야 아웃풋으로 0을 내보내고 , 아니면 다 1을 산출 

- NOR 
  - OR 뒤에 NOT 붙인 결과를 낸다. 
  - 인풋 2개중 하나라도 1이라면  0을 내보내고, 두개 다 0이라면 다 1을 산출한다. 

- EXOR 
  - 서로 달라야 1을 산출 

- EXNOR
  - 서로 같아야 1을 산출 

## 이제..  Adder  를 만들 차례 !

(이하 XOR = EXOR)
- Half Adder ( 반가산기 )
  - 인풋에 XOR 과 AND 를 각각 연결하면 XOR 의 결과는 SUM 이고 AND의 결과는 CARRY 가 된다.
  - SUM 은 합이고, 만약 이진법에서  1 + 1 이면 10 이 되므로 CARRY는 앞 자리수에 더할 수가 된다. 

- Full Adder ( 전가산기 )
  - 글로 설명하기 좀 복잡하긴 한데, AND ,XOR , OR 의 조합으로 세개의 bit 인풋을 CARRY 와 SUM 으로 내는 것
  - 나중에 그림을 그려놓자. 지금은 처음 입문이니 굳이 자세하게 들어가지 않겠다. 

이제 이 둘을 사용하면 8비트 리플 캐리 가산기를 만들 수 있다 !

> 최초 반가산기로 가장 낮은 자리수 합의 결과를 도출 . 1+1 이라서 앞자리수로 넘길 것이라면 그 넘긴 수와 다음 자릿수의 2개 총 3개의 인풋을 전가산기에 넣고  그 자리수의 합의 결과를 도출, 다시 여기서 나온 CARRY 값을 다음 자릿수의 전가산기로 넘겨서 .... 이를 반복

사실 n 비트 리플 캐리 가산기를 만들 수 있을 것 같다.

그리고 이런것들을 모아서 .. ALU 라는 모듈로 다시 추상화 하는 것이다.



# 후기

오늘도 느끼지만 , 컴퓨터 과학은 정말 매력적인 학문이다. 

> 추상 - 구체 , 개념 - 물리 이 사이의 어딘가. 
> 수학 - 논리학 - 컴퓨터 공학 - 전자공학 

이 스펙트럼에서 아주 재간둥이 포지션 (?) 을 가지고 있기도 하고 

여러 개념이 섞여있는 것처럼 보이는 가운데에서 

추상화와 모듈화 등을 반복적으로 사용해서 

말도 안되는 생산성을 가지는 도구를 만들어낸다.

그건 마치 프랙탈처럼 고수준 언어에서도 저수준 회로에서도 발견된다. 

저수준 깊게 내려가면 다른 학문으로 살짝 입장할 수 있는 문이 열리는 것도 매우 흥미롭다. 

철학 통계학 의학 실험과학 신경생물학 등의 근원에서 힘을 얻는 심리학과는 또 다르게 말이다. 

작고 간단한 것들을 조합해서 무엇을 만들어 낼 수 있다는 것이

이 학문의 독특한 향기? 색깔? 같은 게  아닐까 싶다 ㅎㅎ.

( 컴퓨터 과학의 본질까지는 아직 당연히 모르지만.. )



