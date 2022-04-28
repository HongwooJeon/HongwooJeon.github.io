---
title: 1. Design Pattern 생성패턴
author: Banjung
date: 2022-04-24 10:00:00 +0800
categories: [Design Pattern, Chapter 1(생성패턴)]
tags: [Design Pattern]
math: true
mermaid: true
---

## 1 Abstract Factory
- 구체적인 클래스를 지정하지 않고 관련성을 갖는 객체들의 집합을 생성하거나 서로 독립적인 객체들의 집합을 생성할 수 있는 인터페이스를 제공한다.

### 1.1 구조

![image](/assets/img/DesignPattern/AbstractFactory.png)


### 1.2 설명
- AbstractFactory: 개념적 제품에 대한 객체를 생성하는 오퍼레이션으로 인터페이스를 정의한다.
- ConoreateFactory: 구제척인 제품에 대한 객체를 생성하는 오퍼레이션을 구현한다.
- AbstractProduct: 개념적 제품 객체에 대한 인터페이스를 정의한다.
- ConcreateProduct: 구체적으로 팩토리가 생성할 객체를 정의하고, AbstractProduct 가 정의하고 있는 인터페이스를 구현한다.
- Client: AbstractFactory 와 AbstractProduct 클래스에 선언된 인터페이스를 사용한다.

## 2 Builder
-복합 객체의 생성 과정과 표현 방법을 분리함으로써 동일한 생성 공정이 서로 다른 표현을 만들 수 있게 한다.

### 2.1 구조

![image](/assets/img/DesignPattern/Builder.png)

### 2.2 설명
- Builder: Product 객체의 일부 요소들을 생성하기 위한 추상 인터페이스를 정의한다.
- ConcreateBuilder: Builder 클래스에 정의된 인터페이스를 구현하며, 제품의 부품들을 모아 빌더를 합성한다. 생성한 요소의 표현을 정의하고 관리한다. 또한 제품을 검색하는데 필요한 인터페이스를 제공한다.
- Director: Builder 인터페이스를 사용하는 객체를 합성한다.
- Product: 구축할 복합 객체를 표현한다. ConcreateBuilder 는 제품의 내부 표현을 구축하고 어떻게 모아 하나로 만드는지의 과정을 정의한다.

## 3 Factory Method
- 객체를 생성하는 인터페이스를 정의하지만, 인스턴스를 만들 클래스의 결정은 서브클래스가 한다. Factory Method 패턴에서는 클래스의 인스턴스를 만드는 시점을 서비클래스로 미룬다.

### 3.1 구조

![image](/assets/img/DesignPattern/FactoryMethod.png)

### 3.2 설명
- Product: 팩토리 메소드가 생성하는 객체의 인터페이스를 정의한다.
- ConcreteProduct: Product 클래스에 정의된 인터페이스를 실제로 구현한다.
- Creator: Product 타입의 객체를 반환하는 팩토리 메소드를 선언한다. Creator 클래스는 팩토리 메소드를 기본적으로 구현하는데, 이 구현에서는 ConcreteProduct 객체를 반환한다. 또한 Product 객체의 생성을 위해 팩토리 메소드를 호출한다.
- ConcreteCreator: ConcreteProduct 의 인스턴스를 반환하기 위해 팩토리 메소드를 재정의한다.

## 4 Prototype
- 프로토타입의 인스턴스를 이용해서 생성할 객체의 종류를 명세하고 이 프로토타입을 복사해서 새로운 객체를 생성한다.

### 4.1 구조

![image](/assets/img/DesignPattern/ProtoType.png)

### 4.2 설명
- Prototype: 자신을 복제하는데 필요한 인터페이스를 정의한다.
- ConcretePrototype: 자신을 복제하는 오퍼레이션을 구현한다.
- Client: prototype 에 복제를 요청함으로써 새로운 객체를 생성한다.

## 5 Singleton
- 클래스의 인스턴스는 오직 하나임을 보장하며 이 인스턴스에 접근할 수 있는 방법을 제공한다.

### 5.1 구조

![image](/assets/img/DesignPattern/Singleton.png)

### 5.2 설명
- Singleton: Instance() 오퍼레이션을 정의하여, 유일한 인스턴스로의 접근이 가능하도록 한다. Instance() 오페레이션은 클래스 오퍼레이션이다.
- 클래스 오퍼레이션이란 C++ 에서는 static 으로 정의되는 멤버함수로 클래스에서 만드는 모든 인스턴스에 걸쳐서 공유하는 함수이다. 이로써 유일한 인스턴스의 생성에 대한 책임을 지게 된다.