# 브릿지 예제 코드 분석
## 분석할 프로그램
```C++
#include <iostream>
#include <string>
using namespace std;

class Implementation {
public:
    virtual ~Implementation() {}
    virtual string OperationImplementation() const = 0;
};

class ConcreteImplementationA : public Implementation {
public:
    string OperationImplementation() const override {
        return "ConcreteImplementationA: Here's the result on the platform A.\n";
    }
};
class ConcreteImplementationB : public Implementation {
public:
    string OperationImplementation() const override {
        return "ConcreteImplementationB: Here's the result on the platform B.\n";
    }
};

class Abstraction {
protected:
    Implementation* implementation_;

public:
    Abstraction(Implementation* implementation) : implementation_(implementation) {
    }

    virtual ~Abstraction() {
    }

    virtual string Operation() const {
        return "Abstraction: Base operation with:\n" +
            this->implementation_->OperationImplementation();
    }
};

class ExtendedAbstraction : public Abstraction {
public:
    ExtendedAbstraction(Implementation* implementation) : Abstraction(implementation) {
    }
    string Operation() const override {
        return "ExtendedAbstraction: Extended operation with:\n" +
            this->implementation_->OperationImplementation();
    }
};

void ClientCode(const Abstraction& abstraction) {
    cout << abstraction.Operation();
}

int main() {
    Implementation* implementation = new ConcreteImplementationA;
    Abstraction* abstraction = new Abstraction(implementation);
    ClientCode(*abstraction);
    cout << endl;
    delete implementation;
    delete abstraction;

    implementation = new ConcreteImplementationB;
    abstraction = new ExtendedAbstraction(implementation);
    ClientCode(*abstraction);

    delete implementation;
    delete abstraction;

    return 0;
}
```
### Implementation 클래스
```C++
class Implementation {
public:
    virtual ~Implementation() {}
    virtual string OperationImplementation() const = 0;
};
```
- 가상 소멸자는 구현 클래스들이 제대로 소멸될 수 있도록 한다.
  > ``Implementation`` 클래스를 상속하는 클래스들이 동적으로 할당된 메모리 등을 삭제할 수 있게 된다.
- 가상함수 ``OperationImplementation``는 순수 가상함수이기 때문에 자식 클래스에서 반드시 구현되어야 한다.
  - 자식 클래스에서 실제로 수행될 연산을 정한다.
  > const로 선언되어, 이 함수를 호출하는 동안에는 객체의 내부 상태를 변경하지 않는다.'
- ``Implementation`` 클래스는 추상화된 인터페이스를 제공하여, 구현 클래스들이 특정 플랫폼에서의 동작을 정의할 수 있게 한다.
- 추상화와 구현이 서로 독립적으로 확장 가능하게 되며, 새로운 구현이나 플랫폼이 추가되더라도 기존 코드를 수정하지 않고 확장이 가능하다.

### ConcreteImplementationA 및 ConcreteImplementationB 클래스
``` C++
class ConcreteImplementationA : public Implementation {
public:
    string OperationImplementation() const override {
        return "ConcreteImplementationA: Here's the result on the platform A.\n";
    }
};

class ConcreteImplementationB : public Implementation {
public:
    string OperationImplementation() const override {
        return "ConcreteImplementationB: Here's the result on the platform B.\n";
    }
};
```
- 두 클래스는 ``Implementation`` 클래스를 상속받아 구현한다.
- 두 클래스에서는 ``OperationImplementation`` 함수를 오벌아ㅣ드하여 구체적으로 구현한다.
  - ``ConcreteImplementationA``에서는 platform A에서의 동작을 나타내는 문자열 반환
  - ``ConcreteImplementationB``에서는 platform B에서의 동작을 나타내는 문자열 반환
- 클라이언트에서 ``OperationImplementation`` 함수를 호출할 때 ``Implementation`` 포인터를 통해 호출하지만, 실제로는 해당 포인터가 가리키는 객체의 실제 타입에 따라 적잘한 구현이 호출된다.
- 특정 플랫폼에서의 구현을 담당하고 있으며, 이를 통해 브릿지 패턴을 활용하여 추상화와 구현을 분리한 디자인을 구현하고 있다.

### Abstraction 클래스
```C++
class Abstraction {
protected:
    Implementation* implementation_;

public:
    Abstraction(Implementation* implementation) : implementation_(implementation) {
    }

    virtual ~Abstraction() {
    }

    virtual string Operation() const {
        return "Abstraction: Base operation with:\n" +
            this->implementation_->OperationImplementation();
    }
};
```
- ``Abstraction`` 클래스는 계층 구조의 제어 부분에 대한 추상적인 인터페이스를 정의한다.
  > 해당 클래스는 implementation을 멤버 변수로 가진다.
- 생성자는 ``Implementation`` 객체를 받아 이를 멤버 변수로 설정한다.
- ``Operation`` 함수는 가상 함수로, 기본 작업을 수행하는데 사용된다.
- > 이때, ``implementation``의 ``OperationImplementation`` 함수를 호출하여 구현에 특화된 작업을 수행한다.

### ExtendedAbstraction 클래스
```C++
class ExtendedAbstraction : public Abstraction {
public:
    ExtendedAbstraction(Implementation* implementation) : Abstraction(implementation) {
    }
    string Operation() const override {
        return "ExtendedAbstraction: Extended operation with:\n" +
            this->implementation_->OperationImplementation();
    }
};
```
- ``ExtendedAbstraction`` 클래스는 ``Abstraction`` 클래스를 확장하여 새로운 기능을 추가한다.
- 생성자는 ``Implemention`` 객체를 받아 ``Abstraction``의 생성자를 호출하여 멤버 변수를 설정한다.
- ``Operation`` 함수를 오버라이드하여 새로운 작업을 수행한다.
- > 여기서도 ``Implementation``의 ``OperationImplementation`` 함수를 호출하여 구현에 특화된 작업을 수행한다.

``Abstraction`` 클래스와  ``ExtendedAbstraction`` 클래스는 브릿지 패턴에서의 ``추상화``를 담당한다. ``Abstraction``은 인터페이스를 정의하고, ``ExtendedAbstraction``은 추가적인 동작을 이 인터페이스에 더한다. 이는 구현과 추상화를 분리하여, 각각을 독립적으로 확장하고 변형할 수 있도록 한다.

### ClientCode 함수
```c++
void ClientCode(const Abstraction& abstraction) {
	std::cout << abstraction.Operation();
}
```
* 해당 함수는 ```Abstraction 클래스```의 객체를 매개변수로 받아와서 해당 객체의 ```Operation 함수```를 호출하고 결과를 표시하는 역할을 한다.
* 여기서 ```Abstraction 클래스```는 추상화를 나타내는 클래스이며, ```Operation 함수```는 해당 추상화가 수행하는 작업을 정의한다.
* 클라이언트 코드에서는 ```ClientCode 함수```를 호출할 때 다양한 종류의 ```Abstraction 객체```를 전달할 수 있다.
* 이것으로 추상화가 다양한 구현을 가진 객체와 협력할 수 있게 된다.

### main 함수
```c++
int main() {
	Implementation* implementation = new ConcreteImplementationA;
	Abstraction* abstraction = new Abstraction(implementation);
	ClientCode(*abstraction);
	std::cout << std::endl;
	delete implementation;
	delete abstraction;

	implementation = new ConcreteImplementationB;
	abstraction = new ExtendedAbstraction(implementation);
	ClientCode(*abstraction);

	delete implementation;
	delete abstraction;

	return 0;
}
```
* ```ConcreteImplementationA```를 사용하여 구현 객체를 생성하고, 이를 ```Abstraction 객체```의 생성자에 전달하여 ```Abstraction 객체```를 생성한다.
* ```ClientCode 함수```에 ```Abstraction 객체```를 전달하여 해당 객체의 ```Operation 함수```를 호출하고 결과를 출력한다. 이때, ```ConcreteImplementationA```에서 정의한 구현이 수행된다.
* 이전에 사용된 ```implementation```과 ```abstraction```을 해제하고, ```ConcreteImplementationB```를 사용하여 새로운 구현 객체를 생성하고, 이를 ```ExtendedAbstraction 객체```의 생성자에 전달하여 ```ExtendedAbstraction 객체```를 생성한다.
* 다시 ```ClientCode 함수```에 ```ExtendedAbstraction 객체```를 전달하여 해당 객체의 ```Operation 함수```를 호출하고 결과를 출력한다. 이번에는 ```ConcreteImplementationB```에서 정의한 구현이 수행된다.
* 마지막으로 다시 동적으로 할당된 메모리를 해제한다.
*이렇게 함으로써 ```Abstraction```과 ```Implementation```이 서로 독립적으로 확장될 수 있다.
