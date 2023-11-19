## 코드 분석 결과
**중재자 인터페이스**는 컴포넌가 다양한 이벤트를 중재자에게 알리기 위해 사용하는 방법을 선언한다. 중재자는 이러한 이벤트에 반응하여 다른 컴포넌트에게 실행을 전달 할 수 있다. 
```C++
#include <iostream>
#include <string>
class BaseComponent;
class Mediator {
 public:
  virtual void Notify(BaseComponent *sender, std::string event) const = 0;
```
* ```Mediator``` 클래스는 컴포넌트들이 중재자에게 이벤트를 알리기 위한 ```Notify``` 함수를 순수 가상함수로 선언한다.
* ```Notify``` 함수의 ```sender``` 매개변수는 어떤 컴포넌트에서 이벤트가 발생했는지 가리키며, ```event``` 매개변수는 발생한 이벤트의 종류를 나타낸다.

 **기본 컴포넌트**는 객체 내부에 중재자 인스턴스를 저장하는 기본 기능을 제공한다.
```C++
class BaseComponent {
 protected:
  Mediator *mediator_;
 public:
  BaseComponent(Mediator *mediator = nullptr) : mediator_(mediator) {
  }
 void set_mediator(Mediator *mediator) {
    this->mediator_ = mediator;
  }
};
```
* ```Mediator``` 멤버 변수는 ```Mediator``` 객체에 대한 포인터를 멤버 변수로 가지고 있다. 이 멤버 변수는 해당 컴포넌트가 어떤 중재자와 연결되어 있는지를 나타낸다.
* 생성자에서는 ```mediator``` 포인터를 기본값으로 설정하며 이렇게 함으로써 중재자가 명시적으로 제공되지 않는 경우에는 ```nullptr```로 초기화한다.
* 중재자를 동적으로 설정하기 위한 함수이며 이를 통해 컴포넌트 객체가 런타임 중에 중재자를 변경할 수 있다.

 **콘크리트 컴포넌트**는 다른 컴포넌트에 의존하지 않으면서 다양한 기능을 구현한다. 또한 구체적인 매개변수 클래스에도 의존하지 않는다.
 ```C++
class Component1 : public BaseComponent {
 public:
  void DoA() {
    std::cout << "Component 1 does A.\n";
    this->mediator_->Notify(this, "A");
  } // 중재자에게 해당 이벤트를 알린다.
 void DoB() {
    std::cout << "Component 1 does B.\n";
    this->mediator_->Notify(this, "B");
  } // 중재자에게 해당 이벤트를 알린다.
};
```
* ```BaseComponent```에 상속받아 ```BaseComponent```에서 정의된 멤버 변수와 함수를 상속받아 사용할 수 있다.
* 해당 함수는 ```Component I dose A```를 출력하고 중재자에게 A 이벤트를 알린다. A 동작이 수행되었음을 중재자에게 알리는 역할을 한다.
```C++
class Component2 : public BaseComponent {
 public:
  void DoC() {
    std::cout << "Component 2 does C.\n";
    this->mediator_->Notify(this, "C");
  } // 중재자에게 해당 이벤트를 알린다.
  void DoD() {
    std::cout << "Component 2 does D.\n";
    this->mediator_->Notify(this, "D");
  } // 중재자에게 해당 이벤트를 알린다.
};
```

 **콘크리트 중재자**는 여러 컴포넌트를 조정함으로써 협력적인 행동을 구현한다.
```C++
class ConcreteMediator : public Mediator {
 private:
  Component1 *component1_;
  Component2 *component2_;
 public:
  ConcreteMediator(Component1 *c1, Component2 *c2) : component1_(c1), component2_(c2) {
    this->component1_->set_mediator(this);
    this->component2_->set_mediator(this);
  } 
  void Notify(BaseComponent *sender, std::string event) const override {
    if (event == "A") {
      std::cout << "Mediator reacts on A and triggers following operations:\n";
      this->component2_->DoC();
    } 
    if (event == "D") {
      std::cout << "Mediator reacts on D and triggers following operations:\n";
      this->component1_->DoB();
      this->component2_->DoC();
    } 
  }
};
```
* ```ConcreteMediator```은 ```Mediator```의 상속을 받으며 ```component1과 2```를 멤버 변수로 갖는다.
* 생성자에서 ```c1```과 ```c2```를 받아 초기화하고 각 컴포넌트를 자기 자신이 중재자가 되게 설정한다.
* 이벤트가 A인 경우에 ```component2``` 클래스의 ```DoC``` 함수를 호출한다.
* 이벤트가 D인 경우 ```DoB```와 ```DoC``` 함수를 호출한다.

**클라이언트 코드**
```C++
void ClientCode() {
  Component1 *c1 = new Component1; // Component1의 객체를 생성
  Component2 *c2 = new Component2; // Component2의 객체를 생성
  ConcreteMediator *mediator = new
  std::cout << "Client triggers operation A.\n";
  c1->DoA(); // DoA 함수를 호출한다.
  std::cout << "\n";
  std::cout << "Client triggers operation D.\n";
  c2->DoD(); // DoD 함수를 호출한다.

  delete c1; // c1을 삭제한다.
  delete c2; // c2를 삭제한다.
  delete mediator; // mediator를 삭제한다.
}
```
* 컴포넌트의 객체를 생성 후 이를 이용하여 콘크리트 중재자 객체를 생성하고 컴포넌트 객체와 연결한다.
* ```Client triggers operation A.```가 출력되고, ```c1->DoA()```가 호출된다. 이때 중재자를 통해 다른 객체에게 해당 작업을 알린다.
* ```Client triggers operation D.```가 출력되고, ```c2->DoD()```가 호출된다. 마찬가지로 중재자를 통해 다른 객체에게 해당 작업을 알린다.
* 객체들의 작업 수행이 완료된 후에는 ```delete```를 사용하여 메모리를 해제한다.

## 실행 결과
```C++
Client triggers operation A.
Component 1 does A.
Mediator reacts on A and triggers following operations:
Component 2 does C.

Client triggers operation D.
Component 2 does D.
Mediator reacts on D and triggers following operations:
Component 1 does B.
Component 2 does C.
```
이렇게 함으로써 객체들 간의 결합을 줄이고, 향후 변경이나 확장에 더 유연하게 대응할 수 있도록 하는 중재자 패턴이 적용된 디자인이 된다.
