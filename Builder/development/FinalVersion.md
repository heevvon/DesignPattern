# 빌더 패턴 프로그램 개발
## 프로그램
```C++
#include <iostream>
#include <string>
using namespace std;

// 캐릭터 클래스
class Character {
    string name; //직업
    int level = 1; //레벨
    string weapon; //무기
    int weaponAttack = 1; //무기의 공격력
    string armor; //방어구
public:
    Character() = default;

    Character(const Character& other) {
        name = other.name;
        level = other.level;
        weapon = other.weapon;
        weaponAttack = other.weaponAttack;
        armor = other.armor;
    }
    // 멤버 함수
    void setName(const string newName) {
        name = newName;
    }
    void setLevel(int newLevel) {
        level = newLevel;
    }
    void setWeapon(const string& newWeapon, int newWeaponAttack) {
        weapon = newWeapon;
        weaponAttack = newWeaponAttack;
    }
    void setArmor(const string& newArmor) {
        armor = newArmor;
    }
    // 캐릭터 정보 출력 함수
    void showInfo() const {
        cout << "[캐릭터 정보]" << endl;
        cout << " * 직업: " << name << endl;
        cout << " * 레벨: " << level << endl;
        cout << " * 무기: " << weapon << "(공격력: " << weaponAttack << ')' << endl;
        cout << " * 방어구: " << armor << endl;
    }
};
// 빌더 인터페이스
class CharacterBuilder {
public:
    virtual ~CharacterBuilder() {}
    virtual void setName(const string& name) = 0;
    virtual void setLevel(int level) = 0;
    virtual void setWeapon(const string& weapon, int weaponAttack) = 0;
    virtual void setArmor(const string& armor) = 0;
    virtual Character* getResult() = 0;
};

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

int main() {
    Information* infoBuilder = new Information();
    Director director(infoBuilder);

    string job;
    cout << "캐릭터를 선택하세요 (전사, 마법사, 암살자, 궁수): ";
    cin >> job;

    if (job == "전사") {
        director.setWarrior();
    }
    else if (job == "마법사") {
        director.setMagician();
    }
    else if (job == "암살자") {
        director.setAssassin();
    }
    else if (job == "궁수") {
        director.setArcher();
    }
    else {
        cout << "잘못된 직업을 선택했습니다." << endl;
        return 1;
    }

    Character* character = infoBuilder->getResult();
    character->showInfo();

    delete infoBuilder;

    return 0;
}
```

## 출력결과
```cin
캐릭터를 선택하세요 (전사, 마법사, 암살자, 궁수):
```

### 전사 선택
```cin
캐릭터를 선택하세요 (전사, 마법사, 암살자, 궁수): 전사
[캐릭터 정보]
 * 직업: 전사
 * 레벨: 20
 * 무기: 검과 방패(공격력: 25)
 * 방어구: 갑옷
```

### 마법사 선택
```cin
캐릭터를 선택하세요 (전사, 마법사, 암살자, 궁수): 마법사
[캐릭터 정보]
 * 직업: 마법사
 * 레벨: 27
 * 무기: 마법 스태프(공격력: 20)
 * 방어구: 마법 로브
```

### 암살자 선택
```cin
캐릭터를 선택하세요 (전사, 마법사, 암살자, 궁수): 암살자
[캐릭터 정보]
 * 직업: 암살자
 * 레벨: 15
 * 무기: 단검(공격력: 23)
 * 방어구: 검은 로브
```

### 궁수 선택
```cin
캐릭터를 선택하세요 (전사, 마법사, 암살자, 궁수): 궁수
[캐릭터 정보]
 * 직업: 궁수
 * 레벨: 25
 * 무기: 활과 화살(공격력: 17)
 * 방어구: 갑옷
```

### 잘못 입력하였을 경우
```cin
캐릭터를 선택하세요 (전사, 마법사, 암살자, 궁수): 무도가
잘못된 직업을 선택했습니다.
```
