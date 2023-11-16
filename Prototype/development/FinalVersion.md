# 프로토타입 코드 개발 최종본
## 벡터를 이용한 몬스터 스포너 생성 및 전투 출력
### 프로그램
```C++
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class Spawner {
    string name;
    int health;

public:
    Spawner(string n, int h) : name(n), health(h) {}

    virtual ~Spawner() {}

    virtual const string& getName() const { return name; }
    virtual int getHealth() const { return health; }

    virtual Spawner* clone() const = 0;
    virtual void spawn() const = 0;
    virtual void receiveDamage(int damage) {
        health -= damage;
    }
};

class Player {
    string playerName;
    int playerDamage;

public:
    Player(string PN, int PD) : playerName(PN), playerDamage(PD) {}

    void attack(Spawner* spawner) {
        cout << playerName << "이(가) " << spawner->getName() << "에게 " << playerDamage << "의 데미지를 가했습니다!" << endl;
        spawner->receiveDamage(playerDamage);

        int remainingHealth = spawner->getHealth();

        if (remainingHealth <= 0) {
            cout << spawner->getName() << "이(가) 소멸되었습니다." << endl;
        }
        else {
            int remainingHealth = spawner->getHealth();
            cout << "공격력이 낮아 " << spawner->getName() << "이(가) 소멸되지 않았습니다." << endl;
            cout << "남은 체력: " << remainingHealth << endl;
        }
        delete spawner;
    }
};

template <class T>
class SpawnerPrototype : public Spawner {
public:
    SpawnerPrototype(string name, int health) : Spawner(name, health) {}

    Spawner* clone() const override {
        return new T(*static_cast<const T*>(this));
    }

    void spawn() const override {
        cout << T::getSpawnMessage() << endl;
    }
};

class ZombieSpawner : public SpawnerPrototype<ZombieSpawner> {
public:
    ZombieSpawner(string name, int health = 15) : SpawnerPrototype(name, health) {}

    static string getSpawnMessage() {
        return "좀비가 스폰되었습니다!";
    }
};

class SkeletonSpawner : public SpawnerPrototype<SkeletonSpawner> {
public:
    SkeletonSpawner(string name, int health = 7) : SpawnerPrototype(name, health) {}

    static string getSpawnMessage() {
        return "스켈레톤이 스폰되었습니다!";
    }
};

class CreeperSpawner : public SpawnerPrototype<CreeperSpawner> {
public:
    CreeperSpawner(string name, int health = 12) : SpawnerPrototype(name, health) {}

    static string getSpawnMessage() {
        return "크리퍼가 스폰되었습니다!";
    }
};

int main()
{
    vector<Spawner*> spawners;

    string playerName;

    cout << "플레이어 이름을 입력하세요: ";
    cin >> playerName;

    Player player(playerName, 10);

    spawners.push_back(new ZombieSpawner("좀비"));
    spawners.push_back(new SkeletonSpawner("스켈레톤"));
    spawners.push_back(new CreeperSpawner("크리퍼"));

    for (Spawner* spawner : spawners) {
        spawner->spawn();
        player.attack(spawner);
    }

    return 0;
}
```
### 출력 결과
```bin
플레이어 이름을 입력하세요:
```
#### Steve 입력
```bin
플레이어 이름을 입력하세요: Steve
좀비가 스폰되었습니다!
Steve이(가) 좀비에게 10의 데미지를 가했습니다!
공격력이 낮아 좀비이(가) 소멸되지 않았습니다.
남은 체력: 5
스켈레톤이 스폰되었습니다!
Steve이(가) 스켈레톤에게 10의 데미지를 가했습니다!
스켈레톤이(가) 소멸되었습니다.
크리퍼가 스폰되었습니다!
Steve이(가) 크리퍼에게 10의 데미지를 가했습니다!
공격력이 낮아 크리퍼이(가) 소멸되지 않았습니다.
남은 체력: 2
```
## 일반 객체를 이용한 몬스터 스포너 생성 및 전투 출력
### 프로그램
```C++
#include <iostream>
#include <string>
using namespace std;

class Spawner {
    string name;
    int health;

public:
    Spawner(string n, int h) : name(n), health(h) {}

    virtual ~Spawner() {}

    virtual const string& getName() const { return name; }
    virtual int getHealth() const { return health; }

    virtual Spawner* clone() const = 0;
    virtual void spawn() const = 0;
    virtual void receiveDamage(int damage) {
        health -= damage;
    }
};

class Player {
    string playerName;
    int playerDamage;

public:
    Player(string PN, int PD) : playerName(PN), playerDamage(PD) {}

    void attack(Spawner* spawner) {
        cout << playerName << "이(가) " << spawner->getName() << "에게 " << playerDamage << "의 데미지를 가했습니다!" << endl;
        spawner->receiveDamage(playerDamage);

        int remainingHealth = spawner->getHealth();

        if (remainingHealth <= 0) {
            cout << spawner->getName() << "이(가) 소멸되었습니다." << endl;
        }
        else {
            int remainingHealth = spawner->getHealth();
            cout << "공격력이 낮아 " << spawner->getName() << "이(가) 소멸되지 않았습니다." << endl;
            cout << "남은 체력: " << remainingHealth << endl;
        }
    }
};

template <class T>
class SpawnerPrototype : public Spawner {
public:
    SpawnerPrototype(string name, int health) : Spawner(name, health) {}

    Spawner* clone() const override {
        return new T(*static_cast<const T*>(this));
    }

    void spawn() const override {
        cout << T::getSpawnMessage() << endl;
    }
};

class ZombieSpawner : public SpawnerPrototype<ZombieSpawner> {
public:
    ZombieSpawner(string name, int health = 15) : SpawnerPrototype(name, health) {}

    static string getSpawnMessage() {
        return "좀비가 스폰되었습니다!";
    }
};

class SkeletonSpawner : public SpawnerPrototype<SkeletonSpawner> {
public:
    SkeletonSpawner(string name, int health = 7) : SpawnerPrototype(name, health) {}

    static string getSpawnMessage() {
        return "스켈레톤이 스폰되었습니다!";
    }
};

class CreeperSpawner : public SpawnerPrototype<CreeperSpawner> {
public:
    CreeperSpawner(string name, int health = 12) : SpawnerPrototype(name, health) {}

    static string getSpawnMessage() {
        return "크리퍼가 스폰되었습니다!";
    }
};

int main()
{
    string playerName; 

    cout << "플레이어 이름을 입력하세요: "; 
    cin >> playerName; 

    Player player(playerName, 10); 

    ZombieSpawner zombieSpawner("좀비"); 
    SkeletonSpawner skeletonSpawner("스켈레톤"); 
    CreeperSpawner creeperSpawner("크리퍼"); 

    zombieSpawner.spawn(); 
    player.attack(&zombieSpawner); 

    skeletonSpawner.spawn(); 
    player.attack(&skeletonSpawner); 
     
    creeperSpawner.spawn(); 
    player.attack(&creeperSpawner); 

    return 0;
}
```
### 출력 결과
```bin
플레이어 이름을 입력하세요:
```
#### Steve 입력
```bin
플레이어 이름을 입력하세요: Steve
좀비가 스폰되었습니다!
Steve이(가) 좀비에게 10의 데미지를 가했습니다!
공격력이 낮아 좀비이(가) 소멸되지 않았습니다.
남은 체력: 5
스켈레톤이 스폰되었습니다!
Steve이(가) 스켈레톤에게 10의 데미지를 가했습니다!
스켈레톤이(가) 소멸되었습니다.
크리퍼가 스폰되었습니다!
Steve이(가) 크리퍼에게 10의 데미지를 가했습니다!
공격력이 낮아 크리퍼이(가) 소멸되지 않았습니다.
남은 체력: 2
```
## 선택적 몬스터 생성 및 전투 출력
### 프로그램 
```C++
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class Spawner {
    string name;
    int health;

public:
    Spawner(string n, int h) : name(n), health(h) {}

    virtual ~Spawner() {}

    virtual const string& getName() const { return name; }
    virtual int getHealth() const { return health; }

    virtual Spawner* clone() const = 0;
    virtual void spawn() const = 0;
    virtual void receiveDamage(int damage) {
        health -= damage;
    }
};

class Player {
    string playerName;
    int playerDamage;

public:
    Player(string PN, int PD) : playerName(PN), playerDamage(PD) {}

    void attack(Spawner* spawner) {
        cout << playerName << "이(가) " << spawner->getName() << "에게 " << playerDamage << "의 데미지를 가했습니다!" << endl;
        spawner->receiveDamage(playerDamage);

        int remainingHealth = spawner->getHealth();

        if (remainingHealth <= 0) {
            cout << spawner->getName() << "이(가) 소멸되었습니다." << endl;
        }
        else {
            int remainingHealth = spawner->getHealth();
            cout << "공격력이 낮아 " << spawner->getName() << "이(가) 소멸되지 않았습니다." << endl;
            cout << "남은 체력: " << remainingHealth << endl;
        }
        delete spawner;
    }
};

template <class T>
class SpawnerPrototype : public Spawner {
public:
    SpawnerPrototype(string name, int health) : Spawner(name, health) {}

    Spawner* clone() const override {
        return new T(*static_cast<const T*>(this));
    }

    void spawn() const override {
        cout << T::getSpawnMessage() << endl;
    }
};

class ZombieSpawner : public SpawnerPrototype<ZombieSpawner> {
public:
    ZombieSpawner(string name, int health = 15) : SpawnerPrototype(name, health) {}

    static string getSpawnMessage() {
        return "좀비가 스폰되었습니다!";
    }
};

class SkeletonSpawner : public SpawnerPrototype<SkeletonSpawner> {
public:
    SkeletonSpawner(string name, int health = 7) : SpawnerPrototype(name, health) {}

    static string getSpawnMessage() {
        return "스켈레톤이 스폰되었습니다!";
    }
};

class CreeperSpawner : public SpawnerPrototype<CreeperSpawner> {
public:
    CreeperSpawner(string name, int health = 12) : SpawnerPrototype(name, health) {}

    static string getSpawnMessage() {
        return "크리퍼가 스폰되었습니다!";
    }
};

int main()
{
    vector<Spawner*> spawners;

    // 플레이어 이름 입력 및 생성
    string playerName;

    cout << "플레이어 이름을 입력하세요: ";
    cin >> playerName;

    Player player(playerName, 10);

    string monsterName;

    cout << "어떤 몬스터를 공격하시겠습니까? (좀비, 스켈레톤, 크리퍼): ";
    cin >> monsterName;

    if (monsterName == "좀비") {
        player.attack(new ZombieSpawner("좀비"));
    }
    else if (monsterName == "스켈레톤") {
        player.attack(new SkeletonSpawner("스켈레톤"));
    }
    else if (monsterName == "크리퍼") {
        player.attack(new CreeperSpawner("크리퍼"));
    }
    else {
        cout << "몬스터를 찾을 수 없습니다." << endl;
    }
    return 0;
}
```
### 출력 결과
```bin
플레이어 이름을 입력하세요:
```
#### Steve를 입력하고, 몬스터 중 좀비를 입력한 경우
```bin
플레이어 이름을 입력하세요: Steve
어떤 몬스터를 공격하시겠습니까? (좀비, 스켈레톤, 크리퍼): 좀비
Steve이(가) 좀비에게 10의 데미지를 가했습니다!
공격력이 낮아 좀비이(가) 소멸되지 않았습니다.
남은 체력: 5
```
#### Steve를 입력하고, 몬스터 중 스켈레톤을 입력한 경우
```bin
플레이어 이름을 입력하세요: Steve
어떤 몬스터를 공격하시겠습니까? (좀비, 스켈레톤, 크리퍼): 스켈레톤
Steve이(가) 스켈레톤에게 10의 데미지를 가했습니다!
스켈레톤이(가) 소멸되었습니다.
```
#### Steve를 입력하고, 몬스터 중 크리퍼를 입력한 경우
```bin
플레이어 이름을 입력하세요: Steve
어떤 몬스터를 공격하시겠습니까? (좀비, 스켈레톤, 크리퍼): 크리퍼
Steve이(가) 크리퍼에게 10의 데미지를 가했습니다!
공격력이 낮아 크리퍼이(가) 소멸되지 않았습니다.
남은 체력: 2
```
