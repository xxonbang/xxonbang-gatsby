---
title: '[encryption] 암호화(encryption), 복호화(decryption)에 대한 기초 이해'
date: 2020-11-03 13:01:00
category: 'etc'
draft: false
---

application 에서 생성/전달 되는 data 를 특정 목적(해킹 방지, 데이터 위/변조 예방 등)에 따라 암호화/복호화 해야 되는 경우가 있다. 대표적으로 user 로그인 시 사용하는 password 는 암호화하여 db에 저장하는 것이 보편적이다. 이제부터 암호화에 대한 <u>기본적인 정보</u>를 정리해 보려고 한다.

## Table of Contents
```toc
```

## 용어해설
- `해쉬 알고리즘(hash algorithm)` : hash 를 수행하기 위하여 사용하는 알고리즘. 대표적인 단방향 hash 알고리즘으로는 MD5, SHA-1, SHA-256, SHA-512 등이 있음. 
- `해시 함수(hash function)` : 원본 값을 받아 함수 내부에서 연산을 통해 암호화를 수행
- `다이제스트(digest)` : 해쉬함수를 통해 수학적인 연산을 하고 원본 메시지를 변환하여 생성한 <u>암호화된 메시지</u>
- `솔트(salt)` : 단방향 해시 함수에서 다이제스트를 생성할 때 추가되는 바이트 단위의 임의의 문자열
- `솔팅(salting)` : 원본 메시지를 해쉬함수를 통해 암호화할 때 salt 문자열을 추가하여 다이제스를 생성하는 것  
- `키 스트레칭(key stretching)` : 특정 data 의 digest 를 생성 → 생성한 digest 를 입력 값으로 다시 digest 생성 → 특정 횟수만큼 반복
- `Adaptive Key Derivation Functions` : 키 스트레칭에 사용되는 함수  
- `Padding` : 암호 블록 사이즈와 데이터 사이즈가 맞지 않을 경우에 배수에 맞춰 빈공간을 채워주는 방식(PKCS#5, PKCS#7 등)
- `IV(Initialization Vector)` : 평문 블록 암호화 시 직전 암호문 블록이 존재하지 않아 이를 대체할 목적으로 정해진 1개의 블록을 의미  
- `mode` : 블럭 암호화 순서 및 규칙에 대한 표준(CBC 등)

## 1. 암호화 방식 (단방향, 양방향)
1. **단방향**
    - 암호화를 통해 다이제스트를 생성
    - 복호화를 하지 않음
    - 원본 메시지를 알면 암호화된 메시지를 구하기는 쉽지만 암호화된 메시지로는 원본 메시지를 구할 수 없어야 함
2. **양방향**
    - 암호화를 통해 다이제스트를 생성
    - 복호화 할 것을 기본 전제로 사용
    - key 를 통해 암호화 된 메세지를 복호화하여 원본 메세지를 구할 수 있음

## 2. 단방향 암호화
data 를 hashing 하여 암호화하며 salting, key stretching 등의 추가적인 방법을 사용하여 암호화하는 것이 보편적이다.

### 1. salting 의 목적과 방법
1. 목적
    - user 가 id 와 password 를 통해 로그인하는 상황으로 예를 들겠음
    - 위/변조, 해킹의 목적으로 user 의 password 를 얻어내었다 하더라도, 해쉬에 사용한 salt 까지 정확하게 알아내지 못하면 실제 password 를 얻어낼 수 없음 
    - 서버에서는 user 별 고유한 salt 와 digest 를 db 에 저장해두고, 로그인 시 user 가 입력한 password 의 값과 서버에 미리 저장해준 salt 값을 사용하여 hash 하고 db 에 저장된 digest 와 일치 여부를 비교함 
2. 방법
    ```
    9a87d6f876as8a6f8as7df6 + password1234    ⟹    generated digest
            (salt)             (password)  (hash fn)
    ```

### 2. key stretching 의 목적과 방법
1. 목적
    - <u>salt 를 사용하여 hash → digest 생성</u> 하는 것 보다 더 강력한 digest 를 생성하고자 사용
    - 똑같은 digest 를 얻어내기 위해서는 key stretching 에 적용된 hash 횟수와 정확히 동일한 횟수만큼 반복 hash 해야만 같은 값을 얻어 낼 수 있음
    - 이는 해커가 data 를 얻어내기 위해 연산할 때 걸리는 시간 또한 더 오래 걸리게 만들어 data 를 찾아내기 힘들게 함
2. 방법
    - 솔팅과 키 스트레칭으로 구성된 암호화 시스템을 구현하려고 한다면 이미 검증된 암호화 시스템(Adaptive Key Derivation Functions)을 사용할 것을 권장
    - 가장 많이 사용되는 대표적인 방식으로 PBKDF2(Adaptive Key Derivation Functions 의 종류 중 하나) 가 있고, 5개의 파라미터가 있음.
        ```
        DIGEST = PBKDF2(PRF, Password, Salt, c, DLen)
        ```
      - PRF : 난수 (ex : HMAC)
      - Password: 패스워드
      - Salt: 암호학 솔트
      - c: 원하는 iteration 반복 수
      - DLen: 원하는 다이제스트 길이      

## 3. 양방향 암호화
<u>복호화 될 수 있는 암호화</u> 이기 때문에 Key 를 사용하여 data 를 암호화 하고 복호화 하게 된다.

### 1. 대칭키 암호 방식 vs 비 대칭키(공개키) 암호 방식
1. 대칭키 암호 방식
    - 암호화/복호화에 같은 key 를 사용
    - 암호화 할 때에 사용한 key 를 복호화 하고자 하는 쪽으로 전달함
    - 전달 받은 key 를 통해 data 를 복호화 함
    - key 를 반드시 전달해야 되며, 이러한 점 때문에 보안 및 안정성이 떨어질 수 있음
        ```
        ex)
        철수 : 평문 → 대칭키로 암호화 → 암호문  / 영희 : 암호문 → 대칭키로 복호화 → 평문
        ```
2. 비 대칭키(공개키) 암호 방식
    - 암호화/복호화에 다른 key(개인키와 공개키) 를 사용
    - 개인키 : 타인에게 절대 노출되면 안되는 비밀키(사용자만 알고 있는 암호를 풀 수 있는 키)
    - 공개키 : 개인키를 토대로 만든 키, 사람들에게 공개된 키이며 정보를 암호화 할 수 있음
    - Public Key 는 외부 아무에게나 노출되어도 상관이 없으나, Private Key 는 보안이 유지되어야 함
        ```
        ex)
        철수 : 평문 → 영희의 Public Key 로 암호화 → 암호문
        영희 : 암호문 → 영희의 Private Key 로 복호화 → 평문
        ```
      
### 2. 대칭키 암호 방식에 대한 간단한 정리
1. 대표적인 알고리즘
    - 대칭키 암호화 방식에 사용되는 알고리즘은 AES 방식이 있음
    - 키의 길이에 따라 AES-128, AES-192, AES-256 등으로 나뉨
    - 함수 실행 횟수 또한 128bit 키 사용시에는 10라운드, 192bit에서는 12라운드, 256bit에서는 14라운드를 실행
2. 구현 방법
    - 사용할 알고리즘 + 모드 + 패딩방식 을 통해 구현됨 (ex. AES256 / CBC / PKCS#7)
    - 사용할 `key` 를 hash 하여 생성
    - 사용할 `IV` 를 hash 하여 생성
    - AES 알고리즘을 사용한다 했을 때, AES 인스턴스를 생성하고 mode, padding, `key`, `IV` 의 값을 설정
    - encryption 을 위해 설정 된 위의 AES 인스턴스에 실제 암호화할 data 를 넣어 암호화를 수행 (실제 자세한 코드 예시는 하기 reference 의 <u>안전한 암호화를 위한 AES 알고리즘에 대한 이해와 구현코드(Java, C#)</u> 를 참고하길 바람)

## 4. 마무리
암호화에 대한 기초 개념의 이해에 목적을 두고 글을 작성하였음을 다시 말씀드리고자 한다. 암호화에 대해 전혀 모르는 상태에서 접근하고 이해하기에 좋은 수준/내용으로 구성하려 노력하였다. 실제 코드구현 예시/방법 및 좀 더 자세한 내용은 하기 reference 및 검색을 통해 이해의 폭을 넓히길 바란다.


## References
- [naver D2 - 안전한 패스워드 저장](https://d2.naver.com/helloworld/318732)
- [안전한 암호화를 위한 AES 알고리즘에 대한 이해와 구현코드(Java, C#)](https://dailyworker.github.io/AES-Algorithm-and-Chiper-mode/)
- [crypto 모듈을 사용한 암호화](https://www.zerocho.com/category/NodeJS/post/593a487c2ed1da0018cff95d)
- [공개키(Public Key) and 개인키( Private Key)](http://blog.naver.com/PostView.nhn?blogId=chodahi&logNo=221385524980) 
