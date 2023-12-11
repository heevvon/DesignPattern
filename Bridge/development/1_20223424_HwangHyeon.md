# MazeGameImpl 클래스
```C++
#include <iostream>
#include <vector> 
using namespace std;

class MazeGameImpl {
public:
	virtual void resetGame() = 0;   
	virtual void movePlayer(int direction) = 0;   
	virtual bool isGoalArrival() = 0; 
	virtual ~MazeGameImpl() {}
};
```
- ``MazeGameImpl`` 클래스는 미로 게임의 구현을 위한 인터페이스를 제공한다.
- 순수 가상 함수를 통해 게임 초기화, 플레이어 이동, 목표 도달 여부를 정의하고 있다.
- 가상 소멸자는 파생 클래스의 객체가 파괴될 때 올바른 소멸을 보장하기 위해 사용한다.

# MazeGame 클래스
```C++
class MazeGame {
	// MazeGameImpl 포인터형 객체 선언 : 게임의 구현과 추상화가 느슨하게 결합되도록 함
	MazeGameImpl* mazeGameImpl;

	// 플레이어로부터 이동 방향을 받는 메서드
	int getPlayerInput() { 
		int direction; 
		cout << "이동 방향을 선택하세요 (-1: 왼쪽, 1: 오른쪽) : ";
		cin >> direction;
		return direction;
	}

public:	
	MazeGame(MazeGameImpl* impl) : mazeGameImpl(impl) {}

	void play() {
		mazeGameImpl->resetGame();

		while (!mazeGameImpl->isGoalArrival()) {
			int direction = getPlayerInput();
			mazeGameImpl->movePlayer(direction); 
		}

		cout << "축하드립니다! 미로를 탈출하셨습니다!" << endl;  
	}
};
```
- ``MazeGame`` 클래스는 ``MazeGameImpl`` 클래스의 인터페이스를 사용하여 미로 게임을 구현한다.
- ``MazeGameImpl`` 클래스를 멤버로 가지고 있으며, 브릿지 패턴을 사용하여 구현체를 주입받는다.
- ``getPlayerInput`` 함수는 사용자로부터 이동방향을 입력받는다.
- ``play`` 함수는 게임의 주 흐름을 담당하며, 게임을 초기화하고 플레이어의 입력을 받아 미로를 탈출할 때 까지 반복한다.
- ``MazeGame`` 클래스는 구현과 추상화를 느슨하게 결합시키고 있다.
  - 따라서, ``MazeGame`` 클래스는 구현 세부 사항을 알 필요 없이 게임을 실행할 수 있다.
  - 구현체를 바꿔치기하면서도 ``MazeGame``의 인터페이스는 변경되지 않는다.
  - 이는 코드의 확장성과 유지보수성을 높이는 브릿지 패턴의 장점 중 하나이다.
