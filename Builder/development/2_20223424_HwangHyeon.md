```C++
// 콘크리트 클래스 (실제로 캐릭터를 생성하는 구현 클래스)
class Information : public CharacterBuilder {
    Character* character;
public:
    //객체 생성 시 초기화
    Information() { this->Reset(); }
    //소멸자에서 할당된 메모리 해제
    ~Information() { delete character; }

    void Reset() {
        this->character = new Character();    // 객체 생성 및 초기화
    }
    void setName(const string& name) override {
        character->setName(name);     // 이름 설정
    }
    void setLevel(int level) override {
        character->setLevel(level);     // 레벨 설정
    }
    void setWeapon(const string& weapon, int weaponAttack) override {
        character->setWeapon(weapon, weaponAttack);     // 무기, 공격력 설정
    }
    void setArmor(const string& armor) override {
        character->setArmor(armor);     // 장신구 설정
    }

    Character* getResult() override {
        return character;     // 최종적으로 완성된 캐릭터 객체 반환
    }
};

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
