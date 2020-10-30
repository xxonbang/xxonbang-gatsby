---
title: '[Angular] providers 혹은 providedIn 설정의 이해'
date: 2020-10-30 16:01:13
category: 'angular'
draft: false
---

angular 의 service component 를 생성하고 이를 적재적소에 사용하기 위해서는 설정하려고 하는 위치에 따라 providers 혹은 providedIn 에 해당 service 를 등록해 주어야 한다. 이를 통해, angular injector 가 작동하는 과정에서 해당 service 를 제공 받고자 요청한 component 에 올바르게 주입(injection)해 주게 된다.  
이는 세 곳의 위치에서 설정이 가능하고, 이 설정들의 방법과 차이점 등에 대하여 소소하게 설명하고자 한다.

## Table of Contents
```toc
```

## 1. module 에 설정
특정 module (ex. app.module.ts) 내의 providers 에 service 를 등록함으로써, 해당 모듈의 하위에 속한 module 혹은 component 들 전반에 해당 service 를 injection 해줄 수 있다.
설정 위치는 @NgModule 데코레이터의 providers 배열에 등록하면 된다.
```typescript
@NgModule({
  declarations: [ ],

  imports: [ ],

  exports: [ ],

  providers: [
    service1,
    service2,
    service3
  ]
})
```
## 2. component 에 설정
특정 component 내의 providers 에 service 를 등록함으로써, 해당 component 및 그 하위에 속한 component 들 전반에 해당 service 를 injection 해 줄 수 있다.  
설정 위치는 @Component 데코레이터의 providers 배열에 등록하면 된다.
```typescript 
@Component({
  selector: 'app-component',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
  providers: [
    service1,
    service2,
    service3
  ]
})
```
## 3. service 에 설정
생성한 service 에 직접 provide 할 영역을 설정해 줄 수 있다. 기본 옵션은 'root', 'platform', 'any' 등이 있으며, 각각의 옵션에 따라 해당 service 가 provided 되는 영역에 차이가 생기게 된다.
위 3가지 옵션들의 간단한 차이점은 아래와 같다
- root : 해당 application 의 최상위 root 레벨에 service 를 등록하는 것으로, 이를 통해 application 내의 모든 module 및 component 가 해당 service 를 injection 받을 수 있다.
- platform : root 보다 더 상위의 개념으로서, 어떠한 angular application 이 여러개의 application 의 집합으로서 구성될 때에 하나의 service 를 등록한 특정 application 에 대해서만 service 를 global 하게 injection 하게 아니라, platform 개념으로 묶인 모든 angular application 들에 global 하게 공유되게 된다.
- any : 해당 service 를 injection 하는 모든 모듈에 대해서 singleton 방식의 instance 가 아닌 고유한 instance 를 제공한다. 즉, 애플리케이션에 3 개의 서로 다른 모듈이 있고 그 중 2 개가 해당 service 를 사용하는 경우 서로 다른 instance 2 개가 생성되며, 이를 injection 하는 각 module 에 각각 하나씩 생성되게 된다.  

자세한 내용은 [링크](https://blog.angulartraining.com/dependency-injection-with-angular-9-63ce524496d9)를 통해 확인할 수 있으며, singleton 개념이 등장한 이유에 대해서는 마무리 부분을 읽어보길 바란다.  
설정 위치는 @Injectable 데코레이션의 providedIn 에 옵션 값을 설정해 주면 된다.
```typescript 
@Injectable({
  providedIn: 'root'
})
```

## 마무리
총 3가지 방법을 통해 생성한 service 를 injection 설정하여 줄 수 있고, 이를 활용하여 해당 service 의 injection 범위를 정의해 줄 수 있다.  
마지막으로 angular injector 의 작동방식 중 핵심개념이라고 생각 되는 점 중에 하나를 설명하고 마무리 하겠다.
- angular injector 는 위의 방식을 통해서 등록된 service 들에 대한 instance container 를 보유하고 있고 injection 요청들에 대하여 singleton 방식으로 작동한다.
- container 에 등록 된 service 에 대하여 injection 요청을 받게 되면, angular injector 는 token 을 통해 container 내부를 검색하고 token 유/무를 확인한다.
- 등록 된 token 일 경우, instance 생성 여부에 따라 기존에 생성 된 instance 가 있으면 해당 instance 요청 받은 곳에 그대로 주입해 준다(singleton 방식). 반대로, 기존에 생성 된 instance 가 없으면 새로 생성하여 injection 을 수행한다.
