# 브릿지 패턴 예제 코드 분석

## Implementation
```c++
#include <iostream>
#include <string>
using namespace std;

class Implementation {
public:
    virtual ~Implementation() {}
    virtual string OperationImplementation() const = 0;
};
```
* 추상 클래스 ```Implementation```를 정의한다.
* 이 클래스는 가상 소멸자(virtual ~Implementation() {})를 가지고 있으며, 순수 가상 함수(virtual string OperationImplementation() const = 0;)를 포함하고 있다.
* 이는 파생 클래스에서 반드시 구현되어야 하는 함수이다.
* 추상화된 인터페이스를 제공하고 다른 클래스들이 ```OperationImplementation()``` 함수를 구현하도록 한다.

## ConcreteImplementationA / ConcreteImplementationB
```c++
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
* ```Implementation``` 클래스를 상속받아 구체적인 구현을 하는 ```ConcreteImplementationA```와 ```ConcreteImplementationB``` 클래스를 정의한다.
* 두 클래스 모두 ```OperationImplementation()``` 함수를 오버라이드하여 각각의 특정 플랫폼에서의 동작을 구현하고 있다.
* 이 함수는 추상 클래스인 ```Implementation```에서 순수 가상 함수로 선언되었으며, 이를 구현함으로써 실제 동작을 정의하고 있다.

## Abstraction 
```c++
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
* 이어서 ```Abstraction``` 클래스를 정의한다.
* 이 클래스는 구현 객체인 ```Implementation```을 멤버 변수로 가지고 있으며, 생성자를 통해 이 멤버 변수를 초기화한다.(가상 소멸자를 가지고 있다)
* ```Operation()``` 함수는 가상 함수로, 기본 동작을 제공한다.
* "Abstraction: Base operation with:"라는 문자열과 구현 객체인 ```Implementation```의 ```OperationImplementation()``` 함수를 호출한 결과를 반환한다.
* 이를 통해 'Abstraction' 클래스는 구현 객체에 해당 기능을 위임하여 동작을 수행한다.

## ExtendedAbstraction 
```c++
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
* ```ExtendedAbstraction``` 클래스는 ```Abstraction``` 클래스를 상속받는다.
* 생성자에서는 ```Abstraction``` 클래스의 생성자를 호출하여 ```implementation_``` 멤버 변수를 초기화한다.
* ```Operation()``` 함수를 오버라이드하여 새로운 동작을 제공하며, ```Operation()``` 함수에서는 "ExtendedAbstraction: Extended operation with: " 문자열과 함께 ```implementation_``` 멤버의 ```OperationImplementation()``` 함수를 호출한 결과를 반환한다.

## ClientCode
```c++
void ClientCode(const Abstraction& abstraction) {
    cout << abstraction.Operation();
}
```
* ```ClientCode``` 함수는 ```Abstraction``` 클래스의 객체를 인자로 받아 해당 객체의 ```Operation()``` 함수를 호출하여 결과를 출력한다.

## Main
```c++
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
* ```Implementation```의 인스턴스인 ```ConcreteImplementationA```를 생성하여 ```Abstraction```의 인스턴스를 만든다.
* ```ClientCode``` 함수를 호출하여 해당 ```Abstraction```을 사용하여 작업을 수행한다.
* 이후 ```delete```를 사용하여 메모리를 정리합니다.
* ```Implementation```의 다른 인스턴스인 ```ConcreteImplementationB```를 생성하여 ```ExtendedAbstraction```의 인스턴스를 만든다.
* 다시 ```ClientCode``` 함수를 호출하여 새로운 ```ExtendedAbstraction```을 사용하여 작업을 수행하도록 한다.
* 마찬가지로 ```delete```를 사용하여 메모리를 정리한다.
