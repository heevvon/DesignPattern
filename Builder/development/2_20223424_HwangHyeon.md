# Information 클래스
```C++
class Information : public CharacterBuilder {
    Character* character;
public:
    Information() { this->Reset(); }
    ~Information() { delete character; }

    void Reset() {
        this->character = new Character(); 
    }
    void setName(const string& name) override {
        character->setName(name);   
    }
    void setLevel(int level) override {
        character->setLevel(level);     
    }
    void setWeapon(const string& weapon, int weaponAttack) override {
        character->setWeapon(weapon, weaponAttack);   
    }
    void setArmor(const string& armor) override {
        character->setArmor(armor);     
    }

    Character* getResult() override {
        return character;    
    }
};
```
- ``Character`` 클래스의 포인터 타입 멤버 변수가 실제 캐릭터 객체를 가리킨다.
  > 해당 멤버 변수를 통해 ``Information`` 클래스는 빌더 패턴의 핵심 원리를 구현하고, 클라이언트에게 최종적으로 완성된 캐릭터 객체를 제공한다.
- 생성자는 ``Reset`` 함수를 호출하여 ``character``를 초기화한다.
  > 처음에 객체가 생성될 때마다 새로운 객체를 만들기 위한 초기화 단계
- 소멸자는 메모리 누수를 방지하기 위해 동적으로 할당된 ``character``를 삭제한다.
- ``Reset`` 함수는 새로운 캐릭터를 만들기 위해 ``character``를 새로운 ``Character`` 객체로 초기화한다.
- ``set~~`` 함수는 ``character``의 ``set~~`` 메서드를 호출하여 캐릭터의 정보를 저장한다.
- ``getResult()`` 함수는 빌더 패턴에서 정의된 ``getResult`` 메서드를 오버라이드하여, 최종적으로 완성된 캐릭터 캑체를 반환한다.

# Director 클래스
```C++
class Director {
    CharacterBuilder* builder;
public:
    // 주어진 CharacterBuilder 객체를 멤버 변수 builder에 저장하는 역할
    Director(CharacterBuilder* builder) : builder(builder) {}

    // 특정한 순서나 로직에 따라 빌더 클래스의 메서드 호출
    void setWarrior() {
        builder->setName("전사");
        builder->setLevel(20);
        builder->setWeapon("검과 방패", 25);
        builder->setArmor("갑옷");
    }
    void setMagician() {
        builder->setName("마법사");
        builder->setLevel(27);
        builder->setWeapon("마법 스태프", 20);
        builder->setArmor("마법 로브");
    }
    void setAssassin() {
        builder->setName("암살자");
        builder->setLevel(15);
        builder->setWeapon("단검", 23);
        builder->setArmor("검은 로브");
    }
    void setArcher() {
        builder->setName("궁수");
        builder->setLevel(25);
        builder->setWeapon("활과 화살", 17);
        builder->setArmor("갑옷");
    }
};
```
- ``CharacterBuilder`` 타입의 포인터 멤버 변수가 특정 빌더 객체를 가리킨다.
  > 해당 빌더 객체를 통해 캐릭터를 구성하게 된다
- 생성자는 빌더 객체를 받아 멤버 변수 ``builder``에 저장한다.
  > 해당 생성자로 ``Director``가 특정 빌더 객체에 의존하여 캐릭터를 생성할 수 있도록 한다.
- 각각의 메서드는 특정 직업에 맞는 캐릭터를 만들기 위해 빌더 객체의 메서드를 호출한다.
  > 해당 메서드들을 통해 빌더 객체에 정의된 메서드들이 호출되고, 결국 완성된 캐릭터가 ``CharacterBuilder``에서 반환된다.
