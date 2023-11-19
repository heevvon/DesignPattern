# 분석한 코드
```C++
#include <iostream>
#include <string>
class BaseComponent;
class Mediator {
 public:
  virtual void Notify(BaseComponent *sender, std::string event) const = 0;
```
Mediator 클래스는 컴포넌트들이 중재자에게 이벤트를 알리기 위한 Notify 함수를 순수 가상함수로 선언한다. Notify 함수의 sender 매개변수는 어떤 컴포넌트에서 이벤트가 발생했는지 가리키며, event 매개변수는 발생한 이벤트의 종류를 나타낸다.
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
 Mediator 멤버 변수는 Mediator 객체에 대한 포인터를 멤버 변수로 가지고 있다. 이 멤버 변수는 해당 컴포넌트가 어떤 중재자와 연결되어 있는지를 나타낸다.
 생성자에서는 mediator 포인터를 기본값으로 설정하며 이렇게 함으로써 중재자가 명시적으로 제공되지 않는 경우에는 nullptr로 초기화한다.
 중재자를 동적으로 설정하기 위한 함수이며 이를 통해 컴포넌트 객체가 런타임 중에 중재자를 변경할 수 있다.
 ```C++
class Component1 : public BaseComponent {
 public:
  void DoA() {
    std::cout << "Component 1 does A.\n";
    this->mediator_->Notify(this, "A");
  }
 void DoB() {
    std::cout << "Component 1 does B.\n";
    this->mediator_->Notify(this, "B");
  }
};
```
BaseComponent에 상속받아 BaseComponent에서 정의된 멤버 변수와 함수를 상속받아 사용할 수 있다.
해당 함수는 Component I dose A를 출력하고 중재자에게 A 이벤트를 알린다. A 동작이 수행되었음을 중재자에게 알리는 역할을 한다.
나머지도 같은 역할을 한다.
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
ConcreteMediator은 Mediator의 상속을 받으며 component1과 2를 멤버 변수로 갖는다.
생성자에서 c1과 c2를 받아 초기화하고 각 컴포넌트를 자기 자신이 중재자가 되게 설정한다.
이벤트가 A인 경우에 component2 클래스의 DoC 함수를 호출한다.
이벤트가 D인 경우 DoB와 DoC 함수를 호출한다.

*The client code*
```C++
void ClientCode() {
  Component1 *c1 = new Component1; // Component1의 객체를 생성
  Component2 *c2 = new Component2; // Component2의 객체를 생성
  ConcreteMediator *mediator = new
```
