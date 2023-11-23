```c++
#include <iostream>
#include <string>
using namespace std;

// 캐릭터 클래스
class Character {
    string name; //직업
    int level; //레벨
    string weapon; //무기
    int weaponAttack; //무기의 공격력
    string armor; //방어구
public:
    // 캐릭터 정보 출력 함수
    void showInfo() const {
        cout << "[캐릭터 정보]" << endl;
        cout << " *직업: " << name << endl;
        cout << " *레벨: " << level << endl;
        cout << " *무기: " << weapon << "(공격력:" << weaponAttack << ')' << endl;
        cout << " *방어구: " << armor << endl;
    }
};
// 빌더 인터페이스
class Builder {
public:
    virtual ~Builder() {}
    virtual void setName(const string& name) = 0;
    virtual void setLevel(int level) = 0;
    virtual void setWeapon(const string& weapon, int weaponAttack) = 0;
    virtual void setArmor(const string& armor) = 0;
};
int main(){
    return 0;
}
```
