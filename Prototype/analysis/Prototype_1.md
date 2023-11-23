# 프로토타입 예제 코드 분석
## 분석할 코드
```C++
using std::string;

enum Type {
    PROTOTYPE_1 = 0,
    PROTOTYPE_2
};

class Prototype {
protected:
    string prototype_name_;
    float prototype_field_;

public:
    Prototype() {}
    Prototype(string prototype_name)
        : prototype_name_(prototype_name) {
    }
    virtual ~Prototype() {}
    virtual Prototype* Clone() const = 0;
    virtual void Method(float prototype_field) {
        this->prototype_field_ = prototype_field;
        std::cout << "Call Method from " << prototype_name_ << " with field : " << prototype_field << std::endl;
    }
};

class ConcretePrototype1 : public Prototype {
private:
    float concrete_prototype_field1_;

public:
    ConcretePrototype1(string prototype_name, float concrete_prototype_field)
        : Prototype(prototype_name), concrete_prototype_field1_(concrete_prototype_field) {
    }

    Prototype* Clone() const override {
        return new ConcretePrototype1(*this);
    }
};

class ConcretePrototype2 : public Prototype {
private:
    float concrete_prototype_field2_;

public:
    ConcretePrototype2(string prototype_name, float concrete_prototype_field)
        : Prototype(prototype_name), concrete_prototype_field2_(concrete_prototype_field) {
    }
    Prototype* Clone() const override {
        return new ConcretePrototype2(*this);
    }
};

class PrototypeFactory {
private:
    std::unordered_map<Type, Prototype*, std::hash<int>> prototypes_;

public:
    PrototypeFactory() {
        prototypes_[Type::PROTOTYPE_1] = new ConcretePrototype1("PROTOTYPE_1 ", 50.f);
        prototypes_[Type::PROTOTYPE_2] = new ConcretePrototype2("PROTOTYPE_2 ", 60.f);
    }

    ~PrototypeFactory() {
        delete prototypes_[Type::PROTOTYPE_1];
        delete prototypes_[Type::PROTOTYPE_2];
    }

    Prototype* CreatePrototype(Type type) {
        return prototypes_[type]->Clone();
    }
};

void Client(PrototypeFactory& prototype_factory) {
    std::cout << "Let's create a Prototype 1\n";

    Prototype* prototype = prototype_factory.CreatePrototype(Type::PROTOTYPE_1);
    prototype->Method(90);
    delete prototype;

    std::cout << "\n";

    std::cout << "Let's create a Prototype 2 \n";

    prototype = prototype_factory.CreatePrototype(Type::PROTOTYPE_2);
    prototype->Method(10);

    delete prototype;
}

int main() {
    PrototypeFactory* prototype_factory = new PrototypeFactory();
    Client(*prototype_factory);
    delete prototype_factory;

    return 0;
}
```

### 에러 발생
코드를 분석하기 전, 코드를 복사해서 비주얼 스튜디오로 붙여넣기를 했더니, 에러가 발생했다.
에러는 다음과 같았다.
```bin
이 선언에는 스토리지 클래스 또는 형식 지정자가 없습니다.
식별자 "string"이(가) 정의되어 있지 않습니다.
식별자 "prototypes_"이(가) 정의되어 있지 않습니다.
네임스페이스 "std"에 "unordered_map" 멤버가 없습니다.
네임스페이스 "std"에 "string" 멤버가 없습니다.
네임스페이스 "std"에 "cout" 멤버가 없습니다.
네임스페이스 "std"에 "endl" 멤버가 없습니다.
';'가 필요합니다.
```
### 에러 해결
에러를 해결하기위해 코드를 잠깐 수정했는데, 먼저 헤더파일과 네임스페이스를 선언했다.
```C++
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;
```
이 코드를 추가함으로써 모든 에러가 해결되었다.  이후 네임스페이스인 'std'를 사용하는 것을 선언했으므로 필요없는 'std::'는 삭제했다.

## 수정한 코드
```C++
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;

enum Type {
    PROTOTYPE_1 = 0,
    PROTOTYPE_2
};

class Prototype {
protected:
    string prototype_name_;
    float prototype_field_;

public:
    Prototype() {}
    Prototype(string prototype_name)
        : prototype_name_(prototype_name) {
    }
    virtual ~Prototype() {}
    virtual Prototype* Clone() const = 0;
    virtual void Method(float prototype_field) {
        this->prototype_field_ = prototype_field;
        cout << "Call Method from " << prototype_name_ << " with field : " << prototype_field << endl;
    }
};

class ConcretePrototype1 : public Prototype {
private:
    float concrete_prototype_field1_;

public:
    ConcretePrototype1(string prototype_name, float concrete_prototype_field)
        : Prototype(prototype_name), concrete_prototype_field1_(concrete_prototype_field) {
    }

    Prototype* Clone() const override {
        return new ConcretePrototype1(*this);
    }
};

class ConcretePrototype2 : public Prototype {
private:
    float concrete_prototype_field2_;

public:
    ConcretePrototype2(string prototype_name, float concrete_prototype_field)
        : Prototype(prototype_name), concrete_prototype_field2_(concrete_prototype_field) {
    }
    Prototype* Clone() const override {
        return new ConcretePrototype2(*this);
    }
};

class PrototypeFactory {
private:
    unordered_map<Type, Prototype*, hash<int>> prototypes_;

public:
    PrototypeFactory() {
        prototypes_[Type::PROTOTYPE_1] = new ConcretePrototype1("PROTOTYPE_1 ", 50.f);
        prototypes_[Type::PROTOTYPE_2] = new ConcretePrototype2("PROTOTYPE_2 ", 60.f);
    }

    ~PrototypeFactory() {
        delete prototypes_[Type::PROTOTYPE_1];
        delete prototypes_[Type::PROTOTYPE_2];
    }

    Prototype* CreatePrototype(Type type) {
        return prototypes_[type]->Clone();
    }
};

void Client(PrototypeFactory& prototype_factory) {
    cout << "Let's create a Prototype 1\n";

    Prototype* prototype = prototype_factory.CreatePrototype(Type::PROTOTYPE_1);
    prototype->Method(90);
    delete prototype;

    cout << "\n";

    cout << "Let's create a Prototype 2 \n";

    prototype = prototype_factory.CreatePrototype(Type::PROTOTYPE_2);
    prototype->Method(10);

    delete prototype;
}

int main() {
    PrototypeFactory* prototype_factory = new PrototypeFactory();
    Client(*prototype_factory);
    delete prototype_factory;

    return 0;
}
```

### Prototype 클래스
```C++
class Prototype {
protected:
    string prototype_name_;
    float prototype_field_;

public:
    Prototype() {}
    Prototype(string prototype_name)
        : prototype_name_(prototype_name) {
    }
    virtual ~Prototype() {}
    virtual Prototype* Clone() const = 0;
    virtual void Method(float prototype_field) {
        this->prototype_field_ = prototype_field;
        cout << "Call Method from " << prototype_name_ << " with field : " << prototype_field << endl;
    }
};
```
- 기본적인 추상 클래스로, 복사 가능한 객체를 나타낸다.
- ``prototype_name_``과 ``prototype_field_``라는 멤버 변수를 가지고 있다.
- 생성자는 이름을 받아 ``prototype_name_``을 초기화 한다.
- 가상 소멸자를 가지고 있고, 가상 합수 ``Clone``과 ``Method``을 정의하고 있다.
  >``Clone`` 메서드는 순수 가상함수이기 때문에 반드시 하위 클래스에서 구현되어야 하고, 복제된 개체를 반환하는데 기본적으로 자기 자신을 복제하며 ``const`` 함수로 선언되어있기 때문에 이 메서드를 호출하는 동안 객체의 내부 상태를 변경하지 않음을 나타낸다.
- ``Method`` 메서드는 가상함수로 선언되어 있어 하위 클래스에서 재정의할 수 있고, 객체의 ``prototype_field_``를 주어진 값으로 설정하고 객체의 이름과 함께 상태를 출력한다.
  
1. 이 클래스의 목적은 객체를 복제할 수 있는 기본적인 프로토타입을 제공하는 것이다.
2. ``Clone`` 메서드를 하위 클래스에 구현함으로써, 다양한 타입의 객체를 효과적으로 복제한다.
3. ``Method`` 메서드는 기본적으로 상태를 출력하는 역할을 한다.

### ConcretePrototype1, ConcretePrototype2 클래스
```C++
class ConcretePrototype1 : public Prototype {
private:
    float concrete_prototype_field1_;

public:
    ConcretePrototype1(string prototype_name, float concrete_prototype_field)
        : Prototype(prototype_name), concrete_prototype_field1_(concrete_prototype_field) {
    }

    Prototype* Clone() const override {
        return new ConcretePrototype1(*this);
    }
};
```
- ``concrete_prototype_field1``은 float 자료형을 가진 ``ConcretePrototype1``에 추가된 멤버 변수이고, 복제된 객체가 가질 수 있는 고유한 속성을 나타낸다.
- 생성자는 부모 클래스의 생성자를 호출하여 ``prototype_name_``을 초기화하고, 추가된 변수 ``concrete_prototype_field1``을 초기화한다.
- ``concrete_prototype_field1``은 해당 클래스의 고유한 속성을 나타낸다.
- ``Clone`` 메서드는 부모 클래스의 가상함수를 오버라이드해서 복제 동작을 정의하고 있다.
  > 복제된 세로운 객체를 생성하고 현재 객체의 값을 그대로 복사하며, 복사 생성자를 사용하여 깊은 복사를 수행한다.

```C++
class ConcretePrototype2 : public Prototype {
private:
    float concrete_prototype_field2_;

public:
    ConcretePrototype2(string prototype_name, float concrete_prototype_field)
        : Prototype(prototype_name), concrete_prototype_field2_(concrete_prototype_field) {
    }
    Prototype* Clone() const override {
        return new ConcretePrototype2(*this);
    }
};
```
- ``concrete_prototype_field2``은 ``concrete_prototype_field1``과 마찬가지고 float 형의 멤버 변수로, 복제된 객체가 가질 수 있는 고유한 속성을 나타낸다.
- 생성자는 부모클래스를 호출하여 ``prototype_name_``을 초기화 하고, 추가된 멤버 변수 ``concrete_prototype_field2``도 초기화한다.
- ``concrete_prototype_field2``는 해당 클래스의 고유한 속성을 나타낸다.
- ``Clone`` 메서드는 부모 클래스의 가상함수를 오버라이드하여 복제 동작을 정의한다.
  > 복제된 세로운 객체를 생성하고 현재 객체의 값을 그대로 복사하며, 복사 생성자를 사용하여 깊은 복사를 수행한다.

1. 이 클래스들은 프로토타입 패턴을 사용하여 객체를 효과적으로 복제한다.
2. 새로운 객체를 만들기 위해 일일이 새로운 객체를 초기화할 필요가 없다.
3. 기존 객체를 복제하여 손쉽게 새로운 객체를 생성할 수 있다.

### PrototypeFactory 클래스
해당 클래스를 분석하기 전, 아래의 코드를 살펴보자.
```C++
enum Type {
    PROTOTYPE_1 = 0,
    PROTOTYPE_2
};
```
이 코드는 ``Type`` 식별자를 정의하는 코드이다. 이 코드 없이 ``Type``을 사용하려고 한다면, 에러가 발생하게 된다. ``enum``을 이용하여 다양한 프로토타입 유형을 식별한다. 그럼 이제 ``PrototypeFactory`` 클래스를 분석해보자.
```C++
class PrototypeFactory {
private:
    unordered_map<Type, Prototype*, hash<int>> prototypes_;

public:
    PrototypeFactory() {
        prototypes_[Type::PROTOTYPE_1] = new ConcretePrototype1("PROTOTYPE_1 ", 50.f);
        prototypes_[Type::PROTOTYPE_2] = new ConcretePrototype2("PROTOTYPE_2 ", 60.f);
    }

    ~PrototypeFactory() {
        delete prototypes_[Type::PROTOTYPE_1];
        delete prototypes_[Type::PROTOTYPE_2];
    }

    Prototype* CreatePrototype(Type type) {
        return prototypes_[type]->Clone();
    }
};
```
- ``nordered_map<Type, Prototype*, hash<int>> prototypes_``
  - 프로토타입 객체들을 관리하기 위한 ``unordered_map``이다.
  - ``Type``은 열거형으로 정의된 프로토타입의 종류를 나타낸다.
  - ``Prototype*``은 해당 타입의 프로토타입 객체에 대한 포인터를 가리킨다.
- 생성자
  - 기본 생성자에서는 두 가지 종류의 프로토타입 객체를 생성 후 ``prototypes_`` 맵에 추가한다.
  - ``ConcretePrototype1`` 객체와 ``ConcretePrototype2`` 객체를 생성하여 맵에 추가하고, 각각의 객체에 고유한 초기 상태 값을 설정한다.
- 소멸자는 클래스가 소멸될 때 ``prototypes_`` 맵에 저장된 모든 프로토타입 객체를 메모리에서 삭제한다,
- ``CreatePrototype`` 메서드 (``Prototype* CreatePrototype(Type type)``)
- - 특정 타입 프로토타입을 생성하는 메서드이다.
  - ``type`` 매개변수를 통해 원하는 프로토타입을 지정하고, 해당 타입의 프로토타입 객체를 ``Clone`` 메서드를 통해 생성하여 반환한다.
  
 1. ``PrototypeFactorty`` 클래스를 사용하면 클라이언트는 새로운 객체를 생성할 때 해당 객체의 타입만 지정하면 된다.
 2. ``CreatePrototype`` 메서드는 지정된 타입에 해당하는 프로토타입 객체를 생성하여 반환하므로, 객체의 생성 및 초기화를 단순화할 수 있다.
    > 프로토타입 패턴을 통해 객체의 종류를 동적으로 확장할 수 있는 장점을 제공한다.

### Client 함수
```C++
void Client(PrototypeFactory& prototype_factory) {
    cout << "Let's create a Prototype 1\n";

    Prototype* prototype = prototype_factory.CreatePrototype(Type::PROTOTYPE_1);
    prototype->Method(90);
    delete prototype;

    cout << "\n";

    cout << "Let's create a Prototype 2 \n";

    prototype = prototype_factory.CreatePrototype(Type::PROTOTYPE_2);
    prototype->Method(10);

    delete prototype;
}
```
- ``cout`` 객체를 사용하여 화면에 'Let's create a Prototype 1' 메세지를 출력한다.
- ``PrototypeFactory``를 통해 ``Type::PROTOTYPE_1``에 해당하는 프로토타입을 부모클래스인 ``Prototype`` 포인터로 가리킨다.
- 생성된 프로토타입 객체의 ``Method`` 함수를 호출하여 ``prototype_field_`` 값을 설정하고 객체의 이름과 상태를 출력한다.
- 이후 delete를 이용하여 동적으로 할당된 메모리를 삭제한다.
    
1. ``Client``함수는 두 종류의 프로토타입을 생성하고, 각각의 객체에 대해 특정 동작을 수행하여 ㅍ로토타입 패턴을 사용하는 예시를 보여준다.

### main 함수
```C++
int main() {
    PrototypeFactory* prototype_factory = new PrototypeFactory();
    Client(*prototype_factory);
    delete prototype_factory;

    return 0;
}
```
- ``prototype_factory``객체를 포인터 변수로 생성한뒤, ``PrototypeFactory``객체를 동적으로 생성한다.
- ``Client``함수를 호출하고 ``prototype_factory``포인터를 전달한다.
  > ``Client``함수 내에서는 이 ``prototype_factory``를 통해 프로토타입을 생성하고 동작을 수행한다.
- 이후 delete를 이용하여 동적으로 할당된 ``PrototypeFactory`` 객체의 메모를 삭제하는데, 이는 프로그램이 종료될 때 메모리 누수 방지 역할을 한다.
    
1. ``main`` 함수는 프로토타입 패턴을 사용하는 코드를 실행하는 역할을 한다.
2. ``PrototypeFactory``를 생성하고, 이를 ``Client`` 함수에 전달하여 프로토타입이 어떻게 작동하는지 보여준다.
3. 마지막으로, 동적으로 할당된 메모리를 삭제하여 안정적인 프로그램 종료를 보장한다.
