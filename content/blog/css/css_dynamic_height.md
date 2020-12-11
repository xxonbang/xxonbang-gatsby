---
title: '[css] 유동적으로 변하는 화면 높이에 맞게 height 100% 를 적용하는 방법 (how to make a web page dom area height 100% dynamically)'
date: 2020-12-11 15:12:00
category: 'css'
draft: false
---

대단한 tip 은 아니지만, 이 대단하지 않은 것 때문에 작업 중 이해가 되지 않아 한참을 삽질하게 되었다. 그래서 이 내용을 공유해 본다.  
react 를 사용하여 web application 을 제작할 때에 특정 dom 에 들어가는 contents 의 양이 늘어남에 따라 화면의 vertical 크기가 자동으로 길어지게 되었다. 이 때, 크롬 검사도구를 통해 html, body 등의 영역의 크기를 확인해보니 이 영역들은 height 가 100%로 적용되어 있음에도 불구하고 하위 dom 에서 늘어난 영역만큼을 100%로 인식하지 못하는 현상을 발견하게 되었다. 이에 대한 간단한 해결 방법을 정리해본다.

## Table of Contents
```toc
```

## 1. 증상에 대한 detail
- 일반적인 pc 화면에서는 한 화면에 content 가 잘 표현 됨
- 크롬 검사도구를 통해 <u>화면 사이즈를 mobile 사이즈로 변환하였을 때</u> 화면의 가로폭이 줄어들게 되고 해당 page 내의 contents 들을 그리기 위하여 vertical 영역이 길어지게 됨
- 이 때에 html, body 등의 height 가 100%로 설정되어 있음에도 늘어난 하위 dom 의 길이를 모두 커버하지 못하는 점을 발견 함.

## 2. 해결 시도
- html, body 등의 height 설정에 문제가 있다고 판단하여 height 설정을 100% → 100vh 로, 혹은 반대로 적용해 봄.
- 하위 dom 들의 class 값들에도 위와 같이 % 와 vh 를 번갈아가며 적용해 봄.
- 특정 dom 들을 감싸는 wrapper div 를 추가로 적용하여 height 값을 설정해 봄.
- index.html 파일 내 meta 태그에 viewport 와 content="width=device-width" 값이 들어가 있는지 확인.

## 3. 해결 및 문제 포인트
- 결론은 접근 포인트가 잘못 되어 있었다.
- html, body 의 height 가 자동으로 늘어나지 않는 것이기 때문에 이 것들에 대한 height 설정에 부족함이 있다고 판단했던 것이다.
- 그러나 허무하게도 최하위 dom(화면 폭이 줄어듬으로써 포함하고 있는 contents 가 세로로 길게 늘어난 dom) 의 height 를 100% 로 설정해 줌으로써 html, body 의 영역 또한 늘어난 최하위 dom 의 vertical 높이만큼 똑같이 늘어남을 확인했다.

## 4. 결론
- 서두에도 적었듯 대단한 tip 은 아니나, 이를 해결하고자 여러모로 검색해 보았을 때 딱히 내가 찾는 혹은 나의 상황에 맞는 내용의 검색물들이 없었다.
- 이에 혹여라도 나와 같은 문제로 고생하는 분들을 위하여 내용을 정리하였다.
