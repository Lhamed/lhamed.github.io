---
layout: post
title: 깃헙 지킬 블로그에 mermaid 달다. 
description: 
modified: 2019-7-20
tags: [ mermaid , jekyll ] 
---

### Mermaid.js 

마크다운 내에서 , 간단한 기호들로 도표를 그릴 수 있는 플러그인이다.
원래는 jekyll-mermaid 를 달려고 했으나, 깃허브 페이지 단에서 플러그인 차단이 걸려있어서 안되는 것 같아
Mermaid.min.js 파일을 직접 끌어오는 방식으로 사용했다. 
생각보다 쓸모가 많아보이고 재미있다. 
종종 써먹어야지 

>graph TD;
>   B[ㅇ]-->D[ㅠ];    
>   C[ㅇ]-->D;
>   E{이것도} --> F(되지롱);
>   G((요것도)) --> F;

( 이렇게 써두면, 아래 처럼 나온다 ! )
<div class="mermaid">
graph TD;
    B[ㅇ]-->D[ㅠ];
    C[ㅇ]-->D;
    E{이것도} --> F(되지롱);
    G((요것도)) --> F;
</div>

## 개발 윤회 개구리
<div class="mermaid">
graph LR;
    A((폭풍 야근))
    B((피곤 해짐))
    C((대충 개발))
    D((비상 사태))
    A --> B
    C --> D
    D --> A
    B --> C
</div>