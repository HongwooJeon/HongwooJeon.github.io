---
title: 1. Design Pattern 구조패턴
author: Banjung
date: 2022-04-24 10:00:00 +0800
categories: [Design Pattern, Chapter 2(구조패턴)]
tags: [Design Pattern]
math: true
mermaid: true
---

## 1. Adapter
- 클래스의 인터페이스를 클라이언트가 기대하는 다른 인터페이스로 변환한다.
- Adapter 패턴은 호환이 안되는 인터페이스 때문에 함께 사용할 수 없는 클래스를 개보하여 작동하도록 해준다.

### 1.1 구조
- 다중상속을 활용해서 설계한 Adapter 패턴

![image](/assets/img/DesignPattern/Adapter1.png)


- 객체합성에 의한 Adapter 패턴

![image](/assets/img/DesignPattern/Adapter2.png)


### 1.2 설명
- Target: 클라이언트가 사용할 도메인에 종속적인 인터페이스를 정의하고 있는 클래스이다.
- Client: Target 인터페이스를 만족하는 객체와 동작할 대상이다.
- Adaptee: 인터페이스 개조가 필요한 기존의 인터페이스를 정의하고 있는 클래스이다.
- Adapter: Target 인터페이스에 Adaptee 의 인터페이스를 맞춰주는 클래스

## 2. Bridge
- 추상화와 구현을 분리하여 각각을 독립적으로 변형할 수 있게한다.
- 구현과 추상화 개념을 분리하여 구현 자체도 하나의 추상화 개념으로 다양한 변형이 가능하고 구현과 독립적으로 인터페이스도 다양함을 가질수 있다.

### 2.1 구조

![image](/assets/img/DesignPattern/Bridge.PNG)


### 2.2 설명
- Abstraction: 추상화 개념에 대한 인터페이스를 제공하고 객체 구현자(Implementor)에 대한 참조자를 관리한다.
- RefinedAbstraction: 추상화 개념에 정의된 인터페이스를 확장한다.
- Implementor: 구현 클래스에 대한 인터페이스를 제공한다. 다시 말해서 실질적인 구현을 제공한 서브클래스들에 있어서 공통적인 오퍼레이션의 시그니처만을 정의하고 있다. 이 인터페이스는 Abstraction 클래스에 정의된 인터페이스에 정확하게 대응할 필요가 없다. 즉, 두 인터페이스는 서로 다른 형태일 수 있다. 일반적으로 Implementor 인터페이스는 기본적인 구현 오퍼레이션을 수행하고 Abstraction 은 보다 추상화된 서비스 관점의 인터페이스를 제공한다. 그러므로 서비스 관점의 인터페이스를 구현하기 위해서 Implementor 에 정의된 여러 개의 오퍼레이션이 필요할 수도 있는 것이다.
- ConcreteImplementor: Implementor 인터페이스를 구현하는 것으로 실제적인 구현 내용을 담고 있다. 구현 방식이 달라지면 지속적으로 만들어지는 클래스이다.

## 3. Composite
- 부분/전체 계층을 나타내기 위해 복합 객체를 트리 구조로 생성한다.
- 클라이언트가 개별적 객체와 복합 객체 모두를 동일하게 다루도록 한다.

### 3.1 구조

![image](/assets/img/DesignPattern/Composite.PNG)


### 3.2 설명
- Component: 집합 관계에 정의될 모든 객체에 대한 인터페이스를 정의한다. 모든 클래스에 해당하는 인터페이스에 대해서는 공통의 행위를 구현한다. 전체 클래스에 속한 요소들을 관리하는데 필요한 인터페이스를 정의한다. 순환 구조에서 요소들을 표함하는 전체 클래스로의 접근에 필요한 인터페이스를 정의한다. 필요하다면 공통의 행위는 구현할 수 있다.
- Leaf: 집합 관계에서의 다른 객체를 포함할 수는 없고 포함되기만 하는 객체로 객체에 가장 기본이 되는 행위를 정의한다.
- Composite: 포함된 요소들을 갖는 복합 객체에 대한 행위를 정의한다. 자신이 합성하고 있는 요소들을 저장하고 있으면서, 각 합성 요소를 다루는데 관련된 오퍼레이션을 구현한다.
- Client: Component 인터페이스를 통해 집합 관계에 있는 객체들을 관리한다.

## 4. Decorator
- 객체에 동적으로 책임을 추가할 수 있게한다.
- 기능의 유연한 확장을 위해 상속대신 사용할 수 있는 방법이다.

### 4.1 구조

![image](/assets/img/DesignPattern/Decorator.png)


### 4.2 설명
- Component: 동적으로 추가할 서비스를 가질 가능성이 있는 객체들에 대한 인터페이스
- ConcreteComponent: 추가적인 서비스가 실제로 정의되어야 할 필요가 있는 객체
- Decorator: Component 객체에 대한 참조자를 관리하면서 Component 에 정의된 인터페이스를 만족하도록 인터페이스를 정의
- ConcreteDecorator: Component 에 새롭게 추가할 서비스를 실제로 구현하는 클래스이고 Decorator 에 정의된 기본 오퍼레이션을 만족하면서, 추가적인 행위를 addBehavior 로 또는 addedState 로 처리한다.

## 5. Façade
- 서스 시스템에 있는 인터페이스 집합에 대해서 하나의 통한된 인터페이스를 제공한다.
- 서브시스템을 좀 더 사용하기 편하게 하기 위해서 높은 수준의 인터패이스를 정의한다.

### 5.1 구조

![image](/assets/img/DesignPattern/facade.png)


### 5.2 설명
- Facade: 단순하고 일관된 통합 인터페이스를 제공하며, 서브시스템을 구성하는 어떤 클래스가 어떤 요청을 처리해야 하는지를 알고 있으며, 클라이언트의 요청을 해당하는 서브시스템 객체에 전달한다.
- Subsystem Classes: 서브시스템의 기능성을 구현하고, Façade 객체에 의해 할당된 작업을 실제로 처리하지만 Façade 에 대한 아무런 정보를 갖고 있지 않다.

## 6. Flyweight
- 작은 크기의 객체들이 여러 개 있는 경우, 객체를 효과적으로 사용하는 방법으로 객체를 공유하게 한다.

### 6.1 구조

![image](/assets/img/DesignPattern/Flyweight.png)


### 6.2 설명
- Flyweight: Flyweight 가 받아들일 수 있고, 부가적 상태에서 동작해야 하는 인터페이스를 선언하고 있다.
- ConcreteFlyweight: Flyweight 인터페이스를 구현하고 내부적으로 갖고 있어야 하는 본질적 상태에 대한 저장소를 정의하고 있다. ConcreteFlyweight 객체는 공유할 수 있는 것이어야 한다. 그러므로 관리하는 어떤 상태라도 본질적인 것이어야 한다.
- UnsharedConcreteFlyweight: 모든 Flyweight 서브클래스들이 공유될 필요는 없다. Flyweight 인터페이스는 공유를 가능하게 하지만 그것을 강요해서는 안 된다. UnsharedConcreteFlyweight 객체가 ConcreteFlyweight 객체를 자신의 자식으로 갖는 것은 흔한 일이다.
- FlyweightFactory: Flyweight 객체를 생성하고 관리한다. Flyweight 가 적절히 공유되도록 보장해야 한다. 클라이언트가 Flyweight 를 요청하면 FlyweightFactory 객체는 이미 존재하는 인스턴스를 제공하거나, 만약 존재하지 않는다면 생성해야 한다.
- Client: Flyweight 에 대한 참조자를 관리하고 Flyweight 의 부가적 상태를 저장한다.

## 7. Proxy
- 다른 객체로의 접근을 통제하기 위해서 다른 객체의 대리자 또는 다른 객체로의 정보 보유자를 제공한다.

### 7.1 구조

![image](/assets/img/DesignPattern/Proxy.png)


### 7.2 설명
- Proxy: 실제로 참조할 대상에 대한 참조자를 관리한다. Subject 와 동일한 인터페이스를 제공하여 실제 대상을 대체할 수 있어야 한다. 실제 대상에 대한 접근을 제어하고 실제 대상의 생성과 삭제를 책임진다.
- Subject: RealSubject 와 Proxy 에 공통적인 인터페이스를 정의하고 있어, RealSubject 가 요청되는 곳에 Proxy 를 사용할 수 있게 한다.
- RealSubject: 프록시가 대표하는 실제 객체이다.

### 7.3 Proxy 종류

|종류|설명|
|-----------|-----------|
|원격지 프록시|서로 다른 주소 공간에 존재하는 개체에 대한 지역적 표현으로 사용된다. 요청 메시지와 Argument를 인코딩하여 이를 다른 주소 공간에 있는 실제 대상에게 전달한다.|
|가상 프록시|요청이 있을 때만 필요한 객체를 생성한다. 실제 대상에 대한 추가적 정보를 보유하고 있어 실제 접근을 지연할 수 있도록 한다.|
|보호용 프록시|원래 객체에 대한 실제 접근을 제어한다. 객체별로 접근 제어 권한이 다를 때, 유용하게 사용할 수 있다.|