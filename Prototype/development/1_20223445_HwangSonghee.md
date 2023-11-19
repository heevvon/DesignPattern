# 개발 코드
### Spawner 클래스
```c++
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class Spawner {
    int count;
    string name;
    double attack;
public:
    Spawner() {}
    Spawner(int count) : count(count) {}

    virtual Spawner* clone() const = 0;
    virtual void spawn() const = 0;
};
```
* 부모 클래스로 count, name, attack 멤버 변수를 가진 클래스이다.
  + 각각 객체 생성 횟수와 이름, 공격력을 나타내는 변수이다.
* 정수형 매개 변수를 받아 count 멤버 변수를 초기화하는 생성자를 갖고 있다.
* clone과 spawn을 순수 가상 함수로 선언하여 자식 클래스에서 구현하도록 한다.
* Spawner 클래스를 통해 다양한 종류의 몬스터를 스폰할 수 있다.

### ZombieSpawner 클래스
```c++
class ZombieSpawner : public Spawner {
    string nameZombie;
    double attackZombie;
public:
    //생성자
    ZombieSpawner(int count, string name, double attack)
        : Spawner(count), nameZombie(name), attackZombie(attack) {}

    // 복제 함수 구현
    Spawner* clone() const override {
        return new ZombieSpawner(*this);
    }
    // 스폰 함수 구현
    void spawn() const override {
        cout << "좀비가 생성되었습니다!" << endl;
    }
};
```
* nameZomble와 attackZombie 변수를 가진 클래스로 Spawner의 자식 클래스이다.
  + 각각 좀비의 이름과 좀비의 공격력을 나타내는 변수이다.
* 기본 클래스인 Spawner의 생성자를 호출하면서 좀비 스포너의 특정한 멤버 변수들을 초기화하는 생성자이다.
* clone 함수는 부모 클래스인 Spawner의 가상 함수를 구현한 것으로 현재의 좀비 스포너 객체를 복제하여 새로운 객체를 생성하며 복제된 객체는 Spawner 포안터로 반환한다.
* spawn 함수는 부모 클래스인 Spawner의 가상 함수를 구현한 것으로 좀비를 생성하는 동작을 나타낸다. 객체가 생성되면 좀비가 생성되었습니다! 라는 메시지를 출력한다.
* clone 함수를 통해 객체를 복제하고 spawn 함수를 통해 좀비를 생성하는 동작을 정의한다. 이를 통해 Spawner 클래스를 구체화하고 특정한 동작을 다형성을 활용할 수 있다.

### SpiderSpawner 클래스
```c++
class SpiderSpawner : public Spawner {
    string nameSpider;
    double attackSpider;
public:
    // 생성자
    SpiderSpawner(int count, string name, double attack)
        : Spawner(count), nameSpider(name), attackSpider(attack) {}

    // 복제 함수 구현
    Spawner* clone() const override {
        return new SpiderSpawner(*this);
    }
    // 스폰 함수 구현
    void spawn() const override {
        cout << "거미가 생성되었습니다!" << endl;
    }
};
```
* nameSpider과 attackSpider 변수를 가진 클래스로 Spawner의 자식클래스이다.
  + 각각 거미의 이름과 거미의 공격력을 가진 변수이다.
* 기본 클래스인 Spawner의 생성자를 호출하면서 거미 스포너의 특정한 멤버 변수들을 초기화하는 생성자를 갖고 있다.
* clone 함수는 부모 클래스인 Spawner의 가상함수를 구현한 것으로 거미 스포너의 객체를 복제하여 새로운 객체를 생성한다.
* spawn 함수는 부모 클래스인 Spawner의 가상함수를 구현한 것으로 거미를 생성하는 동작을 나타낸다. 객체가 생성되면 거미가 생성되었습니다! 라는 메시지를 출력한다.
