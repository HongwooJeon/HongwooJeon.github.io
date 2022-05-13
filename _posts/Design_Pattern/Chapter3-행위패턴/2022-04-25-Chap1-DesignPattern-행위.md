---
title: 1. Design Pattern 행위패턴
author: Banjung
date: 2022-05-01 10:00:00 +0800
categories: [Design Pattern, Chapter 3(행위패턴)]
tags: [Design Pattern]
math: true
mermaid: true
---

## 1. Chain of Responsibility
- 요청을 처리할 수 있는 기회를 하나 이상의 객체에게 부여함으로써 요청하는 객체와 처리하는 객체사이의 결합도를 없애려는 것이다.
- 요청을 해결할 객체를 만날 때까지 객체 고리를 따라서 요청을 전달한다.

### 1.1 구조

![image](/assets/img/DesignPattern/ChainOfResponsibility.jpg)

### 1.2 설명
- Handler: 요청을 처리하는 인터페이스를 정의하고, 다음번 처리자와의 연결을 구현한다. 즉, 연결 고리에 연결된 다음 객체에게 다시 메시지를 보낸다.
- ConcreteHandler: 책임져야 할 행위가 있다면 스스로 요청을 처리한다. 다음번 처리자에 접근할 수 있다. 즉, 자신이 처리할 행위가 있으면 처리하고, 그렇지 않으면 다음번 처리자에게 다시 처리를 요청한다.
- Client: ConcreteHandler 객체에게 필요한 요청을 보낸다.

## 2. Command
- 요청을 객체로 캡슐화함으로써, 서로 다른 요청으로 클라이언트를 파라미터화 하고, 요청을 저장하거나 기록을 남겨서 오퍼레이션의 취소도 가능하게 한다.

### 2.1 구조

![image](/assets/img/DesignPattern/Command.jpg)

### 2.2 설명
- Command: 오퍼레이션 수행에 필요한 인터페이스 선언.
- ConcreteCommand: Receiver 객체와 액션 간의 연결성을 정의한다. 또한 처리 객체에 정의된 오퍼레이션을 호출하도록 Execute 를 구현한다.
- Client: ConcreteCommand 객체를 생성하고 처리 객체로 정의한다.
- Invoker: 명령어에게 처리를 수행할 것을 요청한다.
- Receiver: 요청에 관련된 오퍼레이션 수행방법을 알고 있다.

## 3. Interpreter
- 언어에 따라서 문법에 대한 표현을 정의한다.
- 언어의 문장을 해석하기 위해 정의한 표현에 기반하여 분석기를 정의한다.

### 3.1 구조

![image](/assets/img/DesignPattern/Interpreter.jpg)

### 3.2 설명
- AbstractExpression: 추상 구문 트리에 속한 모든 노드에 해당하는 클래스들이 공통으로 가져야 할 Interpret() 오퍼레이션을 추상 오퍼레이션으로 정의한다.
- TerminalExpression: 문법에 정의한 터미널 기호와 관련된 해석 방법을 구현한다. 문장을 구성하는 모든 터미널 기호에 대해서 해당 클래스를 만들어야 한다.
- NonterminalExpression: 문법의 오른편에 나타나는 모든 기호에 대해서 클래스를 정의해야 한다. 문법에
R :: = R1 R2 … Rn
을 정의하고 있다면 R 에 대해서 NonterminalExpression 에 해당하는 클래스를 정의해야 한다. 또한 규칙의 오른쪽에 나타난 R1 에서 Rn 에 이르기까지의 모든 기호에 대응하는 인스턴스 변수들을 정의해야 한다. 또한 터미널 기호가 아닌 모든 기호들에 대해서 Interpret() 오퍼레이션을 구현해야 한다. 이 Interpret() 오퍼레이션은 R1 에서 Rn 에 이르기까지의 각 인스턴스 변수를 재귀적으로 호출하는 것이 일반적이다.
- Context: 번역기에 대한 포괄적인 정보를 포함한다.
- Client: 언어로 정의한 특정 문장을 나타내는 추상 구문 트리이다. 이 추상 구문 트리는 NonterminalExpression 과 TerminalExpression 클래스의 인스턴스로 구성된다. 이 인스턴스의 Interpret() 오퍼레이션을 호출한다.

## 4. Iterator
- 내부 표현방법을 노출하지 않고 복합 객체의 원소를 순차적으로 접근할 수 있는 방법을 제공한다.
### 4.1 구조

![image](/assets/img/DesignPattern/Iterator.jpg)

### 4.2 설명
- Iterator: 요소를 접근하고 순회하는데 필요한 인터페이스 제공.
- ConcreteIterator: Iterator 에 정의된 인터페이스를 구현하는 클래스로서 순회 과정 중에 집합 객체 내의 현재 위치를 기억한다.
- Aggregate: Iterator 객체를 생성하는 인터페이스를 정의
- ConcreteAggregate: 해당하는 ConcreteIterator 의 인스턴스를 반환하도록 Iterator 생성 인터페이스를 구현한다.

## 5. Mediator
- 객체들 간의 상호작용을 객체로 캡슐화한다.
- Mediator 패턴은 객체들 간의 참조관계를 객체에서 분리함으로써 상호작용만을 독립적으로 다양하게 확대할 수 있다.

### 5.1 구조

![image](/assets/img/DesignPattern/Mediator.jpg)

### 5.2 설명
- Mediator: Colleague 객체와 교류하는 데 필요한 인터페이스를 정의한다.
- ConcreteMediator: Colleague 객체와 조화를 이룸으로써 이루어지는 협력 행위를 구현하고 자신의 colleague 가 무엇인지를 알고 이를 관리한다.
- Colleague: Mediator 객체가 누구인지를 안다. 다른 객체와 연결성을 필요하면 Mediator 를 통해 이루어지도록 한다.

## 6. Memento
- 캡슐화를 위배하지 않고 객체 내부상태를 객체화 하여, 나중에 객체가 이 상태로 복구 가능하게 한다.
### 6.1 구조

![image](/assets/img/DesignPattern/Memento.jpg)

### 6.2 설명
- Memento: Originator 객체의 내부 상태를 저장한다. 메멘토는 originator 객체의 내부 상태를 필요한 만큼 저장해 둔다. originator 객체를 제외한 다른 객체는 Memento 클래스에 접근할 수 없다. 그래서 Memento 클래스는 두 개의 인터페이스를 갖는다. 관리 책임을 갖는 객체인 CareTaker 클래스는 Memento 에 정의된 모든 인터페이스를 다 보지 못하고 단지 Memnto 를 다른 객체에 전달한다. 이에 비해 Originator 클래스는 자신의 상태를 이전 상태로 복구하기 위해 필요한 모든 자료에 접근하는데 필요한 Memento 의 다양한 인터페이스를 사용할 수 있다. 즉, 메멘도를 생성하는 Originator 클래스만이 메멘토의 내부상태에 접근할 수 있는 권한을 갖는다.
- Originator: 메멘토를 생성하여 현재 객체의 상태를 저장하고 내부 상태를 복구한다.
- Caretaker: 메멘토의 보관을 책임지기는 하지만, 메멘토의 내용을 확인하거나 처리하지는 않는다.

## 7. Observer
- 객체 사이에 1:N 의 종속성을 정의하고 한 객체의 상태가 변하면 종속된 다른 객체에 통보가 되고 자동으로 수정한다.

### 7.1 구조

![image](/assets/img/DesignPattern/Observer.jpg)

### 7.2 설명
- Subject: 관찰자들을 알고 있다. 다수의 Observer 객체는 대상을 관찰한다. Observer 객체를 대상과 연결하거나 무관한 것으로 만드는 데 필요한 인터페이스를 갖는다.
- Observer: 대상에 생긴 변화에 관심 있는 객체를 변경하는데 필요한 인터페이스를 갖고 있다. 이로써 Subject 의 변경에 따라 변화되어야 하는 객체들의 일관성을 유지한다.
- ConcreteObserver: ConcreteSubject 객체에 대한 참조자를 관리한다. 대상과 일관성을 유짛해야 하는 상태를 저장하고 있다. 대상과 일관성을 유지하기 위해 관찰자를 수정해야 하므로 이에 필요한 인터페이스를 구현한다.

## 8. State
- 객체의 내부 상태에 따라 행위를 변경할 수 있게한다.
- 이렇게 하면, 객체는 마치 클래스를 바꾸는 것처럼 보인다.
### 8.1 구조

![image](/assets/img/DesignPattern/State.jpg)

### 8.2 설명
- Context: 클라이언트가 관심을 갖고 있는 인터페이스를 정의한다. ConcreteState 서브클래스의 인스턴스를 관리하고 있는데, ConcreteState 의 서브클래스들이 객체의 현재 상태를 정의하고 있다.
- State: Context 가 갖는 각 상태별로 필요한 행위를 캡슐화하여 인터페이스로 정의한다.
- ConcreteState Subclass: 각 서브클래스들은 Context 의 상태에 따라 처리되어야 할 실제 행위를 구현하고 있다.

## 9. Strategy
- 알고리즘군이 존재할 경우, 각각의 알고리즘을 별도의 클래스로 캡슐화하고 이들을 상호 교환 가능한 것으로 정의한다.
- Strategy 패턴은 클라이언트에 영향을 주지 않고 독립적으로 알고리즘을 다양하게 변경할 수 있게한다.

### 9.1 구조

![image](/assets/img/DesignPattern/Strategy.jpg)

### 9.2 설명
- Strategy: 제공하는 모든 알고리즘에 대한 공통의 오퍼레이션들을 인터페이스로 정의한다. Context 클래스는 ConcreteStrategy 클래스에 정의한 인터페이스를 통해서 실제 알고리즘을 사용한다.
- ConcreteStrategy: Strategy 인터페이스를 실제 알고리즘으로 구현한다.
- Context: ConcreteStrategy 객체가 무엇인지 구체화한다. 즉, Strategy 객체에 대한 참조자를 관리하고, 실제로는 Strategy 서브클래스의 인스턴스를 갖고 있음으로써 구체화한다. 또한 Strategy 가 자료에 접근해가는데 필요한 인터페이스를 정의한다.

## 10. Template Method
- 오퍼레이션에는 알고리즘의 처리 과정만을 정의하고 각 단계에서 수행할 구체적 처리는 서브클래스에 정의한다.
- Template Method 패턴은 알고리즘의 처리 과정은 변경하지 않고 알고리즘 각 단계의 처리를 서브클래스에서 재정의할 수 있게한다.

### 10.1 구조

![image](/assets/img/DesignPattern/TemplateMethod.jpg)

### 10.2 설명
- AbstractClass: 서브클래스들이 반드시 구현해야 하는 알고리즘 처리 단계 내의 기본 오퍼레이션이 무엇인지를 정의한다. 서브클래스에서 이들 오퍼레이션들을 구현한다. 이 클래스에서 템플릿 메소드를 구현하는데, 그 방법은 알고리즘의 기본 골격 구조를 정의하여 템플릿 메소드가 다른 객체에 의해 정의된 오퍼레이션뿐만 아니라 인터페이스로 정의된 기본 오퍼레이션을 재정의한 서브클래스에게 메시지가 전달되어 기본 골격 구조를 준수하되 다른 결과가 나타나게 된다.
- ConcreateClass: 서브클래스마다 기본 오퍼레이션을 다르게 구현한다.

## 11. Visitor
- 객체 구조의 요소들에 수행할 오퍼레이션을 표현한 패턴이다.
- Visitor 패턴은 오퍼레이션이 처리할 요소의 클래스를 변경하지 않고도 새로운 오퍼레이션을 정의할 수 있게한다.
### 11.1 구조

![image](/assets/img/DesignPattern/Visitor.jpg)

### 11.2 설명
- Visitor: 각 ConcreteElement 클래스에 대한 Visit 오퍼레이션을 선언한다. 오퍼레이션의 이름과 인터페이스 형태는 Visit() 요청을 보내는 방문자에게 보내는 클래스를 명시한다. 이로써 방문자는 방문할 요소의 실제 서브클래스를 결정한다. 그리고 나서 방문자는 Element 가 제공하는 인터페이스를 통해 직접 Element 객체에 접근할 수 있다.
- ConcreteVisitor: Visitor 클래스에 정의된 오퍼레이션을 구현한다. 각 오퍼레이션은 객체에 해당하는 클래스에 정의된 알고리즘을 구현한다. ConcreteVisitor 클래스는 내부 상태를 저장하고 있으며, 알고리즘이 운영될 수 있는 상황 정보를 제공한다. ConcreteVisitor 클래스가 저장하고 있는 내부 상태는 구조의 순회 과정에서 도출되기도 한다.
- Element: 아규먼트로 Visitor 클래스를 받아들이는 Accept() 오퍼레이션을 정의한다.
- ConcreteElement: 아규먼트로 Visitor 객체를 받아들이는 Accept() 오퍼레이션을 구현한다.
- ObjectStructure: 요소들을 나열하고 방문자로 하여금 이들 요소에 접근하게 하는 인터페이스를 제공한다. ObjectStructure 는 Composite 패턴의 복합 객체일 수도 있고, 리스트나 집합과 같은 컬렉션일 수도 있다.