---
title: BehavioralPattern_09.Strategy
date: 2020-10-11
author: jyoon
category: DesignPattern
tags:
  - Strategy
  - DesignPattern
  - BehavioralPattern
  - JavaScript
---

# def
전략 패턴은 특정 작업에 대한 대체 알고리즘 (또는 전략)을 캡슐화한다.  
이를 통해 클라이언트가 인식하지 못한 채 다른 방법 (전략)에 의해 런타임에 메서드를 교체 할 수 있다.  
기본적으로 전략은 상호 교환 가능한 알고리즘 그룹이다.  

* 예
  - 쉘 정렬, 힙 정렬, 버블 정렬, 퀵 정렬 등과 ​​같은 숫자 배열에 대해 다른 정렬 알고리즘의 성능을 테스트하고 싶다고 가정해 본다.  
  - 이러한 알고리즘에 전략 패턴을 적용하면 테스트 프로그램이 모든 알고리즘을 간단히 변경할 수 있다.  
  - 런타임에 이들 각각을 배열에 대해 테스트한다.  
  - 전략이 작동하려면 모든 메소드 서명이 동일해야 클라이언트 프로그램이 알지 못하는 사이에 다를 수 있다  
  
JavaScript에서 전략 패턴은 확장 가능한 프레임 워크를 빌드 할 때 플러그인 메커니즘으로 널리 사용된다.  
이것은 매우 효과적인 접근 방법이 될 수 있다.   
  
# participants
  * Context 
    - [POINT1] 현재 Strategy 객체에 대한 참조를 유지 (Context역할 객체 Shipping객체 compnay 프로퍼티)
    - [POINT2] 클라이언트가 strategy calculate를 요청할 수 있도록 인터페이스 제공 (calculate 함수)
    - [POINT3] 고객이 전략을 변경할 수 있다. (setStategy함수를 통해서 설정가능)
    - code: Shipping
  * Strategy 
    - [POINT4] Strategy 인터페이스를 사용하여 알고리즘을 구현한다.(Context에서 calculate 인터페이스를 제공하기에 Strategy에서 calculate동작을 구현한다.)
    - code: UPS, USPS, Fedex

# Sample Code 설명
  * 창고에서 고객에게 배송해야하는 제품 주문이 있다. 
  * 최상의 가격을 결정하기 위해 여러 운송 회사가 평가된다. 
  * 이는 고객이 배송 선호도를 선택하고 선택한 전략이 예상 비용을 반환하는 쇼핑 카트에 유용 할 수 있다.

  * 배송은 컨텍스트이며 UPS, USPS 및 Fedex의 3 개 배송 회사가 전략이다. 
  * 운송 회사(전략)는 3번 변경되며 매번 운송 비용을 계산한다. 
  * 실제 시나리오에서 계산 방법은 발송인의 웹 서비스를 호출 할 수 있다. 
  * 마지막에 우리는 다른 비용을 표시한다.

# 나의 분석
  * Shipping 생성자 객체 setStategy 함수를 통해 company프로퍼티에 Strategy 역할 객체(UPS, USPS, Fedex) 할당
  * Shipping 생성자 객체 calculate 함수를 통해서 company프로퍼티에 할당된 객체에 calculate 인터페이스 호출

```js
var log = (function () {
  var log = '';

  return {
    add: function (msg) { log += msg + '\n' },
    show: function () { console.log(log); log = ''; }
  }
})();

// Context 역할
var Shipping = function () {
  // [POINT1]
  this.company = ""; //UPS, USPS, Fedex 생성자 객체가 차례로 할당
}
Shipping.prototype = {
  // [POINT3]
  setStrategy: function (company) {
    this.company = company;
  },
  // [POINT2]
  calculate: function (package) {
    return this.company.calculate(package);
  }
}

// Strategy 역할
var UPS = function () {
  // [POINT4]
  this.calculate = function (package) {
    // calculations...
    return "$45.95";
  }
}
// Strategy 역할
var USPS = function () {
  // [POINT4]
  this.calculate = function (package) {
    // calculations...
    return "$39.40";
  }
}
// Strategy 역할
var Fedex = function () {
  // [POINT4]
  this.calculate = function (package) {
    // calculations...
    return "$43.20";
  }
}

function run() {
  var package = { from: "76712", to: "10012", weight: "1kg" };

  // the 3 strategies
  var ups = new UPS();
  var usps = new USPS();
  var fedex = new Fedex();

  var shipping = new Shipping();

  shipping.setStrategy(ups);
  log.add("UPS Strategy: " + shipping.calculate(package));
  shipping.setStrategy(usps);
  log.add("USPS Strategy: " + shipping.calculate(package));
  shipping.setStrategy(fedex);
  log.add("FEDEX Strategy: " + shipping.calculate(package));

  log.show();
}

run();
```