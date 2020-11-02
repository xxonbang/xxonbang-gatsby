---
title: '[Git] various error handling'
date: 2020-10-30 13:43:00
category: 'git'
draft: false
---

git 을 사용하다보면, 몇몇의 critical 한 error 들이 발생하게 된다. 직접 경험한 error 내용들을 정리함으로써 향후에 재활용하거나 같은 error 를 겪을 분들에게 도움이 되고자 한다.

## Table of Contents
```toc
```

## fatal authentication failed for github 또는 fatal authentication failed for gitlab Error
서버쪽 계정의 변동이 있었으나, local 쪽엔 해당 변경사항이 적용되지 않았을 때 발생하는 error 입니다.
해당 에러를 해결하기 위해선
```
git config --system --unset credential.helper
```
명령어를 통해 git config를 초기화를 해주면됩니다. 혹여 명령어가 제대로 동작하지 않을 경우엔, CMD를 관리자 권한으로 실행하면 해결될 수 있습니다.   
위의 명령어가 실행된 후에 git 에 push 를 시도할 때 username과 password를 다시 요구하게 됩니다. 이 때에 올바른 계정 아이디와, 비밀번호를 입력하면 정상적으로 push 가 진행되는 것을 확인하실 수 있습니다. 
