---
title: BehavioralPattern_07.Observer
date: 2020-10-11
author: jyoon
category: DesignPattern
tags:
  - Observer
  - DesignPattern
  - BehavioralPattern
  - JavaScript
---

# def
**Observer 패턴은 객체가 이벤트를 구독하고 이벤트 발생시 알림을받는 구독 모델을 제공한다.**  
이 패턴은 JavaScript를 포함한 이벤트 기반 프로그래밍의 초석이다.   
Observer 패턴은 좋은 객체 지향 설계를 용이하게하고 느슨한 결합을 촉진시킨다.  

웹 앱을 빌드 할 때 많은 이벤트 핸들러를 작성하게된다.  
이벤트 핸들러는 특정 이벤트가 발생할 때 알림을받는 함수이다.  
이러한 알림은 선택적으로 이벤트에 대한 세부 정보 (클릭 이벤트에서 마우스의 x, y position정보와 함께 이벤트 받는다.)  

JavaScript의 이벤트 및 이벤트 처리기 패러다임은 Observer 디자인 패턴의 표현이다.   
Observer 패턴의 또 다른 이름은  Pub/Sub이다 (Publication/Subscription 의 짦은 표현이다)  

# participants
  * Subject
    - 관찰자 목록을 유지/ 다수의 Observer 객체가 주제를 관찰 할 수 있다.
    - 옵저버 객체를 구독하거나 구독 취소 할 수있는 인터페이스를 구현한다.
    - 상태가 변경되면 관찰자에게 알림을 보낸다.
    - code: Click
  * Observers
    - Subject가 변경 될 때 호출 될 수있는 특정 함수가 있다.(예: 이벤트 발생)
    - code: clickHandler
  
# Sample Code 설명
  * Click 객체
    - Click 객체는 관찰자다.(clickHandler함수를 subScribe, unsubscribe한다)나타낸다. 
    - clickHandler 함수는 구독되는 대상이다. 
    - 이 핸들러는 이벤트가 발생하는 동안 구독, 구독취소, 다시구독을 하고 있다.
    - event #1, event #3 에 대해서만 알림을받는다.

  * fire 메서드에 대해서 
    - fire 메서드는 두 개의 인수를받는다. 
    - 첫 번째는 이벤트에 대한 세부 정보를 가지고 있고 
    - 두 번째는 컨텍스트, 즉 이벤트 핸들러가 호출 될 때의 this 값이다. 
    - 컨텍스트가 제공되지 않으면 전역 객체(window)에 바인딩된다.

# 나의 분석 
  * [POINT1]
    * Observer 생성자 함수에서 fire할 때 대상   
      : this.handler
    * this.handler설정은 subscribe, unsubscribe에서 해주고 있다.

  * [POINT2]
    * 호출 될 함수의 scope 설정
   
# CODE
```js
var log = (function () {
  var log = '';

  return {
    add: function (msg) {
      log += msg + "\n";
    },
    show: function () {
      console.log(log);
      log = '';
    }
  }
})();

// Subject 역할
// Observer 생성자 함수 
function Click() {
  // [POINT1]
  this.handlers = [];   //observers
}

Click.prototype = {
  // [POINT1]
  subscribe: function (fn) {
    this.handlers.push(fn);
  },
  // [POINT1]
  unsubscribe: function (fn) {
    this.handlers = this.handlers.filter(item => {
      return item !== fn
    });
  },
  fire: function (o, thisObj) {
    // [POINT2] 호출 될 함수의 scope 설정
    var scope = thisObj || window;
    this.handlers.forEach(item => {
      item.call(scope, o);
    });
  }
};

function run() {
  var clickHandler = function (item) {
    log.add(`fired: ${item}`);
  }
  var click = new Click();

  click.subscribe(clickHandler);
  click.fire("event #1");
  click.unsubscribe(clickHandler);
  click.fire("event #2");
  click.subscribe(clickHandler);
  click.fire("event #3");

  log.show();
}

run();
```