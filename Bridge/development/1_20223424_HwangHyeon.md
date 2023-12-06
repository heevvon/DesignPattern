```C++
#include <iostream>
#include <vector>
using namespace std;

class MazeGameImpl {
public:
	virtual void resetGame() = 0;    // 게임 초기화 정의
	virtual void movePlayer(int direction) = 0;    // 방향을 매개변수로 받아 플레이어 이동 정의
	virtual bool isGoalArrival() = 0; // 목표 지점 도달 여부 체크
	virtual ~MazeGameImpl() {}
};

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
	// 생성자 : MazeGameImpl을 받음. 브릿지 패턴에서 구현체를 주입하는 부분
	MazeGame(MazeGameImpl* impl) : mazeGameImpl(impl) {}

	// 게임의 주 흐름을 담당하는 메서드
	void play() {
		// 게임 초기화
		mazeGameImpl->resetGame();

		// 목표지점에 도달할 때까지 루프를 돌며 게임을 진행
		while (!mazeGameImpl->isGoalArrival()) {
			// 플레이어로부터 이동 방향을 입력받음
			int direction = getPlayerInput();
			// 입력받은 이동 방향에 따라 플레이어를 이동 시킴
			mazeGameImpl->movePlayer(direction); 
		}

		cout << "축하드립니다! 미로를 탈출하셨습니다!" << endl;  
	}
};
```
