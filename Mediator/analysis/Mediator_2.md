## 코드 분석 결과

## 헤더 파일
```C++
#include <iostream>
#include <string>
#include <vector>
struct Game;
using namespace std;

#include <boost/signals2.hpp>
using namespace boost::signals2;
```
* ```<iostream>```은 입출력 관련 기능을 위해, ```<string>```은 문자열을 사용하기 위해, ```<vector>```는 동적 배열을 위해 사용한다.

## 이벤트 데이터 구조체
```C++
struct EventData
{
  virtual ~EventData() = default;
  virtual void print() const = 0;
};
```
* ```~EventData()```은 가상 소멸자로 파생 구조체에서 메모리 누수 없이 적절한 정리 작업을 할 수 있다. 해당 가상 함수는 ```default```을 사용하여 디폴트로 구현한다.
* ```print()```함수는 순수 가상 함수로 선언하고 있으며 ```const```키워드를 사용해 이 함수가 객체 내부 상태를 변경하지 않음을 나타낸다.
    - ```print()```함수는 순수 가상 함수이기 때문에 반드시 파생 구조체에서 재정의를 해주어야 한다.

## 플레이어 득점 데이터 구조체
```C++
struct Player;
struct PlayerScoredData : EventData
{
  string player_name;
  int goals_scored_so_far;

  PlayerScoredData(const string& player_name, const int goals_scored_so_far)
    : player_name(player_name),
      goals_scored_so_far(goals_scored_so_far)
  {
  }

  void print() const override
  {
    cout << player_name << " has scored! (their " 
      << goals_scored_so_far << " goal)" << "\n";
  }
};
```
* ```PlayerScoredData``` 구조체는 ```EventData``` 구조체를 상속 받고 있으며 이를 통해 ```EventData``` 구조체의 멤버 변수와 멤버 함수를 사용할 수 있다.
* ```PlayerScoredData``` 구조체는 멤버 변수로 ```player_name```(플레이어의 이름)과 ```goals_scored_so_far```(현재까지의 골 수)을 가지고 있다.
* 생성자는 플레이어의 이름과 현재까지의 골 수를 받아 객체를 초기화한다.
* ```print()```함수는 ```EventData``` 구조체의 가상 함수를 재정의하고 있으며, 함수를 실행하면 플레이어의 이름과 현재까지의 골 수를 출력한다.

## 게임 구조체
```C++
struct Game
{
  signal<void(EventData*)> events; // 관찰자
};
```
* ```signal``` 타입의 객체인 ```event```를 선언하고 있다.

## 플레이어 구조체
```C++
struct Player
{
  string name;
  int goals_scored = 0;
  Game& game;


  Player(const string& name, Game& game)
    : name(name),
      game(game)
  {
  }

  void score()
  {
    goals_scored++;
    PlayerScoredData ps{name, goals_scored};
    game.events(&ps);
  }
};
```
* ```Player``` 구조체는 ```name```(이름)과 기본값이 0으로 초기화 되어 있는 ```goals_scored```(득점한 골 수)를 멤버 변수로 가지고 있다.
* ```Game& game```는 참조 변수를 통해 플레이어가 어떤 게임에 속해 있는지를 나타낸다.
* ```Player```의 생성자를 통해 플레이어의 이름과 속한 게임을 받아 객체를 초기화한다.
* ```score()```함수는 플레이어가 골을 넣었을 때 호출되는 함수로, 골을 넣으면 ```goals_scored```를 증가시키고 ```PlayerScoredData```이벤트를 생성하여 게임에 전달한다.
    - 이 이벤트는 골을 넣은 플레이어의 이름과 현재까지의 골 수를 가지고 있다.
    - ```game.events(&ps)```를 통해 생성된 이벤트 데이터를 게임에 전달한다.

## 코치 구조체
```C++
struct Coach
{
  Game& game;

  explicit Coach(Game& game)
    : game(game)
  {
    // 선수가 3점 미만의 득점을 했으면 격려해준다
    game.events.connect([](EventData* e)
    {
      PlayerScoredData* ps = dynamic_cast<PlayerScoredData*>(e);
      if (ps && ps->goals_scored_so_far < 3)
      {
        cout << "coach says: well done, " << ps->player_name << "\n";
      }
    });
  }
};
```
* ```Game& game```는 참조를 통해 코치가 속한 게임을 나타낸다.
* ```Coach``` 생성자를 통해 코치가 속한 게임을 받아 객체를 초기화한다.
* ```game.events.connect```를 통해 게임의 이벤트 시그널에 새로운 슬롯을 연결한다.
    - 여기에 사용되는 ```connect```함수는 특정 이벤트가 발생했을 떄 실행할 함수를 등록하는 역할을 한다.
* ```[](EventData* e) {...}```는 람다 표현식을 사용하여 이벤트가 발생했을 때 실행할 함수를 정의한다.
* 람다 함수 내에서 이벤트 데이터를 ```PlayerScoredData```로 다운캐스팅한다. ```dynamic_cast```는 런타임에 안전한 타입 캐스팅을 수행하며, ```PlayerScoredData```로의 캐스팅이 성공하면 ```ps```는 유효한 포인터가 된다.
* ```if``` 조건문을 이용하여 다운캐스팅이 성공하면 선수가 3점 미만을 득점했는지 확인한다. 확인 결과 선수가 2점 미만의 득점을 했다면 해당 선수에게 격려의 메시지를 전달한다.

## 메인 함수
```C++
int main()
{
  Game game;
  Player player{ "Sam", game };
  Coach coach{ game };

  player.score();
  player.score();
  player.score(); // ignored by coach

  getchar();
  return 0;
}
```
* ```Game``` 구조체의 객체 ``game``과 ```Player```의 객체 ```player```(이름: 샘, game에 속해있음)와 ```Coach```의 객체 ```coach```(game 객체에 연결된 이벤트 감시)를 생성한다.
* 플레이어가 골을 넣을 때마다 ```PlayerScoredData```이벤트가 발생하고 코치에게 전달된다.
* 세 번째 ```player.score()```는 코치가 플레이어가 3점 미만의 득점을 했을 때만 메시지를 출력하도록 되어 있기 때문에 무사된다.
* ```getchar()```를 통해 사용자가 입력을 할 때까지 프로그램이 일시 정지된다.
