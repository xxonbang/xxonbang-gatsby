---
title: '[Git] 기본 명령어 정리'
date: 2020-10-29 17:03:00
category: 'git'
draft: false
---

평소 git 을 통해 특정 project 의 source 를 관리할 때에 commit / push 등 대부분의 작업을 tool (ex. source tree) 을 사용하여 진행하게 된다.
 그럼에도 불구하고 critical 한 error 나 tool 자체의 오류 때문에 cli 명령어로  작업을 해야 할 상황이 종종 발생하곤 한다. 그런 의미에서 기본적인 git cli 명령어를 정리해보려고 하며, 내용은 지속적으로 추가해 나갈 생각이다.

## Table of Contents
```toc
```

## git 사용자 설정 (global)
- user name 설정
```
git config --global user.name "실제 user name"
```
- user password 설정
```
git config --global user.password "실제 user password"
```
 
## Online git repository clone (online repository → Local) 
```
git clone https://github.com/xxonbang/repository           : repository cloning
cd clone 한 폴더명                                           : clone 한 폴더로 진입
touch README.md                                            : 특정 file 생성
git add README.md                                          : 생성한 file 을 commit 하기 위해 stage 에 등록
git commit -m "add README"                                 : stage 에 등록한 source 를 commit
git push -u origin master                                  : target repository 의 master branch 에 push
```
- git site (ex. github, gitlab 등) 에 이미 존재하는 repository 의 내용 및 source 를 자신의 PC(local)에 clone
- 작업을 통해 commit / push 할 file 생성 (아래 예시는 임시로 README.md 파일 생성)
- cloning 을 통해 연결 된 online git repository 에 source 를 push

## Local 에 새로운 source folder 생성 후 online repository 연결
```
cd 폴더명                                                        : source folder 로 진입
git init                                                       : git 생성
git remote add origin https://github.com/xxonbang/repository   : local 에 target online git repository 등록
git add .                                                      : 현재 폴더 내 모든 source 를 stage 에 등록
git commit -m "commit message"                                 : 커밋 메세지와 함께 commit
git push -u origin master                                      : target repository 의 master branch 에 push
```
- local 에 source 작업한 폴더(project 혹은 workspace 폴더 등)에 진입 후 git 초기 생성
- online 에 미리 생성해 둔 repository 의 URL 로 local 에 remote 등록
- 현재 폴더에 있는 모든 file 을 stage 에 올리고, commit 수행 및 push
