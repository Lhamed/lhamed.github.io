---
layout: post
title: 여러개의 원격 브랜치 한번에 삭제하기 ( delete multiple remote branch at once ) 
description: 
modified: 2020-4-1
tags: [ git, cli, git-cli , git-flow  ] 
---

# 예전 브랜치들이 찌꺼기 처럼 쌓이다. 

내가 속한 개발팀에서, 그간의 브랜치 관리전략은 중구난방에 가까웠다. 

새로운 개발 이슈가 떨어지면, develop/feature/xxxxx 등으로 들어가는 것 까지는 좋았으나, 

develop/feature/firstName/secondName/lalalala 식의 뇌절(?)까지 생기면서 가독성에 문제가 생겼고 

병합 이후에 feature 브랜치를 삭제해주지 않았기 때문에 , 약 2년의 업데이트 기간동안 600개가 넘는 브랜치가 쌓이게 되었다. 

소규모 개발 인원의 특성상 , 엄청나게 큰 병목을 발생시키지 않았기 때문에 , 우선 순위에 밀려서 그간 방치되었다. 

# git-flow 를 기반으로 한 개선 

자세한 내용을 설명하기는 그렇지만, git-flow 브랜치 관리전략을 기반으로 해서 

현재 돌아가고 있는 사내 개발 프로세스에 맞추어 브랜치 관리전략을 성문화한 다음, 

팀에 공유해서 회의를 통해 결정했다. 

그래서 다음과 같은 브랜치 관리의 흐름을 정하게 되었다.  

master >>> develop (하위 브랜치 없음) >>> feature/xxxxx (하위 브랜치 없음) >>(PR + feature 삭제)>> develop >> release >>> master

# 예전의 브랜치들에게 새 전략을 소급하다. 

앞으로의 새 브랜치들은 문제 없이 위 전략을 따르면 되지만, 

이미 만들어진 브랜치들은 어떻게 할까 고민하다. 

다행히 develop/~~ 브랜치들을 모두 삭제하면 문제가 없었다. 

손으로 2개의 브랜치를 삭제하다가 , 깨달았다. 400개가 넘는 삭제 대상 브랜치들을 전부 이렇게 할 수는 없겠다는 생각이 들었다. 

# git command line 

필요한 행동을 생각해보면 , 다음과 같이 명세 할 수 있었다. 

1. develop 이라는 이름을 포함한 대상 브랜치들을 검색해야 한다.
2. [1] 의 브랜치들을 삭제해주어야 한다. 

git cli 로 하면, 

1. git branch -a ( remote 는 -r , 디폴트는 local  )
2. git branch --delete 대상_브랜치 ( local 브랜치 삭제 )
    2-1. git push origin :대상_브랜치 ( remote 에도 적용 )

문제는 위의 것을 한번에 모두 처리하는 것만 남았다. 

# grep , sed , xargs 의 사용 

결과적으로는 다음의 커맨드라인으로 해결할 수 있었다. 

### 로컬 브랜치 삭제 
> git branch -a | grep -E 대상_브랜치 | xargs git branch --delete

### 리모트 브랜치 삭제 적용
> git branch -a | grep -E 대상_브랜치 |  sed 's/remotes\/origin\//:/' | xargs git push origin

아래는 위의 마법주문(?) 이 나온 과정이다. 

| 는 여러 커맨드를 독립적으로 처리하는 문법인 것 같다. 제대로 모르니 나중에 보강하자. 

( 굉장히 직관적이어서 쓰기에 불편함이 없다. )

git branch -a 를 하면 모든 브랜치를 출력한다. 

이 출력을 'grep -E 내용' 해주면, '내용' 이라는 문자열을 포함한 출력 내용만을 추려준다. 

그리고 그 추린 내용의 출력을 받아 xargs 명령어로는 변수처럼 받아서 뒤에 붙여서 다시 입력한다. 

리모트 브랜치의 경우는 불필요한 원격브랜치 경로를 제거해주고, 원래 명령어가 git push origin :대상 이므로 

: 스트링으로 교체해주는 sed 's/~~~/xxxx/' 구문을 사용했다. 이 또한 굉장히 직관적이다. 

( 물론 방법이 너무 많아서 오히려 오래 걸렸다. 서울에서 김서방 찾기 )


# 후기

확실히 이제는 , 모르는 내용에 당황하거나 겁에 질리지 않고 슥 처리하는 것이

얼굴이 두꺼워졌다 ㅎㅎㅎ. ( 좋게 말하면 담력이 올라갔다. )

그리고 그러다보니, 당장의 정보의 디테일을 취하기 보다는

기술적으로 내가 하려는 동작을 명확히하고 분해해서 

검색하면 꽤 명쾌하고 효율적으로 문제를 해결할 수 있다. 

간만에 화장실도 제대로 안가고 2시간 좀 못되는 시간 열심히 해냈다.

끝나고 빌드용 태깅만 남은 깃 히스토리는 너무 아름다웠다. 