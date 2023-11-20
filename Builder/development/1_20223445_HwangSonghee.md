```C++
#include <iostream>
#include <string>
using namespace std;

// 캐릭터 클래스
class Character {
    string name; //이름
    int level; //레벨
    string weapon; //무기
    int weaponAttack; //무기의 공격력
    string armor; //방어구
public:
    // 캐릭터 정보 출력 함수
    void showInfo() const {
        cout << "[캐릭터 정보]" << endl;
        cout << " *이름: " << name << endl;
        cout << "*레벨: " << level << endl;
        cout << "*무기: " << weapon << "(공격력:" << weaponAttack << ')' << endl;
        cout << "*방어구: " << armor << endl;
    }
};
// 빌더 인터페이스
class Builder {
public:
    virtual ~Builder() {}
    virtual Builder& setName(const string& name) = 0;
    virtual Builder& setLevel(int level) = 0;
    virtual Builder& setWeapon(const string& weapon, int weaponAttack) = 0;
    virtual Builder& setArmor(const string& armor) = 0;
};
```
