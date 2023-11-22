```C++
#include <iostream>
#include <string>
using namespace std;

// 캐릭터 클래스
class Character {
    string name; //이름
    int level = 1; //레벨
    string weapon; //무기
    int weaponAttack = 1; //무기의 공격력
    string armor; //방어구
public:
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
        cout << " *이름: " << name << endl;
        cout << " *레벨: " << level << endl;
        cout << " *무기: " << weapon << "(공격력:" << weaponAttack << ')' << endl;
        cout << " *방어구: " << armor << endl;
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

class Direct {
    CharacterBuilder* builder;
public:
    void setWarrior() {
        builder->setName("전사");
        builder->setLevel(20);
        builder->setWeapon("검과 방패", 10);
        builder->setArmor("갑옷");
    }
};

int main() {

}
```
