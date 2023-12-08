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

class MazeGame {
	MazeGameImpl* mazeGameImpl;

	int getPlayerInput() {
		char direction;
		cout << "이동 방향을 선택하세요 (a: 왼쪽, d: 오른쪽, s: 아래, w: 위) : ";
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

class Player {
private:
    int positionX;
    int positionY;
    int health;
public:
    Player() : positionX(0), positionY(0), health(3) {}

    int getPositionX() const {
        return positionX;
    }
    int getPositionY() const {
        return positionY;
    }
    int getHealth() const {
        return health;
    }
    void setPosition(int x, int y) {
        positionX = x;
        positionY = y;
    }
    void decreaseHealth() {
        health -= 1;
        if (health < 0) {
            health = 0;
        }
    }
    bool isGameOver() const {
        return health == 0;
    }
};

class Maze : public MazeGameImpl {
private:
    vector<vector<char>> maze;
    Player player;

public:
    Maze() {
        maze = {
            {'.', '#', '#', '#', '#', '#', '#', '#', '#', '#', '#', '#', '#', '#', '#'},
            {'.', '.', '.', '.', '.', '#', '.', '.', '.', '.', '.', '#', '#', '#', '#'},
            {'#', '#', '.', '#', '.', '#', '.', '#', '.', '#', '.', '#', '.', '.', '#'},
            {'#', '#', '.', '#', '.', '#', '.', '#', '.', '#', '.', '#', '.', '#', '#'},
            {'#', '#', '.', '#', '.', '#', '.', '#', '.', '#', '.', '#', '.', '#', '#'},
            {'#', '#', '.', '#', '#', '#', '.', '#', '.', '#', '.', '#', '.', '#', '#'},
            {'#', '#', '.', '#', '.', '#', '.', '#', '.', '#', '.', '#', '.', '#', '#'},
            {'#', '#', '.', '.', '.', '#', '.', '#', '.', '#', '.', '.', '.', '.', '#'},
            {'#', '#', '.', '#', '.', '#', '.', '#', '.', '#', '#', '#', '#', '.', '#'},
            {'#', '#', '.', '#', '.', '.', '.', '#', '.', '.', '.', '#', '.', '.', '#'},
            {'#', '#', '.', '#', '#', '#', '#', '#', '#', '#', '#', '#', '#', '.', '#'},
            {'#', '#', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '#', '.', '#'},
            {'#', '#', '.', '#', '#', '#', '.', '#', '#', '#', '#', '#', '#', '.', '#'},
            {'#', '.', '.', '#', '.', '.', '.', '#', '#', '#', '#', '#', '#', '.', '.'},
            {'#', '#', '#', '#', '#', '#', '#', '#', '#', '#', '#', '#', '#', '#', '!'}
        };
        player.setPosition(0, 0);
        maze[player.getPositionX()][player.getPositionY()] = 'P';
    }

    void resetGame() override {
        player.setPosition(0, 0);
        maze[player.getPositionX()][player.getPositionY()] = 'P';
        displayMaze();
    }

    void movePlayer(int direction) override {
        maze[player.getPositionX()][player.getPositionY()] = '.'; 

        int newX = player.getPositionX();
        int newY = player.getPositionY();

        switch (direction) {
        case 'a': // 왼쪽
            newY -= 1;
            break;
        case 'd':  // 오른쪽
            newY += 1;
            break;
        case 'w': // 위
            newX -= 1;
            break;
        case 's':  // 아래
            newX += 1;
            break;
        }

        if (newX >= 0 && newX < maze.size() && newY >= 0 && newY < maze[0].size() && maze[newX][newY] != '#') {
            player.setPosition(newX, newY);
        }
        else {
            player.decreaseHealth(); 
        }

        maze[player.getPositionX()][player.getPositionY()] = 'P';
        displayMaze();

        if (player.isGameOver()) {
            cout << "게임 오버! 기회가 다 소진되었습니다." << endl;
            exit(0);
        }
    }

    bool isGoalArrival() override {
        return player.getPositionX() == maze.size() - 1 && player.getPositionY() == maze[0].size() - 1;
    }

    void displayMaze() const {
        for (const auto& row : maze) {
            for (char cell : row) {
                cout << cell << ' ';
            }
            cout << endl;
        }
        cout << "기회: " << player.getHealth() << endl << endl;
    }
};

int main() {
    Maze maze;
    MazeGameImpl* mazeGameImpl = &maze;
    MazeGame mazeGame(mazeGameImpl);

    cout << "미로 게임을 시작합니다. 목표: '!'에 도달하세요!" << endl;
    mazeGame.play();

    return 0;
}
```
