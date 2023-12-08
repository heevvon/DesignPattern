## 플레이어 클래스
### 수정 전
```C++
// 플레이어 클래스
class Player {
private:
	int positionX; // 플레이어 X 위치
	int positionY; // 플레이어 Y 위치
	int health; // 플레이어 체력

public:
	Player() : positionX(0), positionY(0), health(100) {};
};  // 생성자를 이용하여 초기화
```

### 수정 후
```C++
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
```
* ```Player``` 클래스에 추가적으로 함수를 작성하였다.
* ```getPositionX()``` 함수는 플레이어의 X좌표를 반환하는 함수이다. ```const```키워드를 사용하여 멤버 변수를 수정하지 않는다.
* ```getPositionY()``` 함수는 플레이어의 Y좌표를 반환하는 함수이다. ```const```키워드를 사용하여 멤버 변수를 수정하지 않는다.
* ```getHealth()``` 함수는 플레이어의 체력을 반환하는 함수이다.  ```const```키워드를 사용하여 멤버 변수를 수정하지 않는다.
* ```setPosition(int x, int y)```함수는 플레이어의 좌표를 설정하는 함수로, X와 Y좌표를 매개변수로 받아 플레이어의 위치를 업데이트한다.
* ```decreaseHealth()``` 함수는 플레이어의 체력을 감소시키는 함수로, 체력을 1감소시키고 체력이 0보다 작아지면 0으로 설정한다.
* ```isGameOver()``` 함수는 플레이어의 체력이 0이면 게임오버(true), 플레이어의 체력이 0이 아니면 게임을 계속 진행한다.(false)

## 미로 클래스
```C++
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
```
* ```TextMazeGameImpl```클래스를 ```Maze```클래스와 합쳐서 ```Maze```클래스를 완성하였다.
* ```vector<vector<char>> maze```는 2차원 벡터로 미로의 구조를 나타낸다.
	- 벽은 '#', 빈 공간은 '.', 플레이어의 위치는 'P', 목표지점은 '!'로 표시된다.
* 생성자로 클래스가 생선될 때 미로의 초기 상태를 설정한다. 미로의 구조를 2차원 벡터로 정의하고 플레이어의 초기 위치를 (0, 0)으로 설정한다.
* ```resetGame()``` 함수는 ```MazeGameImpl``` 클래스의 함수를 오버라이딩 한 것으로, 게임을 초기 상태로 리셋시킨다. 플레이어의 위치를 (0, 0)으로 초기화하고 초기 위치에 플레이어를 표시(P)하며 플레이어의 위치를 미로를 화면에 출력한다.
* ```movePlayer(int direction)``` 함수는 ```MazeGameImpl``` 클래스의 함수를 오버라이딩 한 것으로, 플레이어를 이동시키는 함수이다. w, a, s, d로 이동 가능하며 이동을 하면 플레이어의 위치를 업데이트하고 이전 위치의 플레이어 표시를 지우고 새로운 위치에 플레이어를 표시한다. 만약 플레이어가 벽(#)에 부딪힌다면 플레이어의 체력을 감소시킨다. 플레이어의 체력이 0이 된다면 게임 오버가 된다.
* ```isGoalArrival()``` 함수는 ```MazeGameImpl``` 클래스의 함수를 오버라이딩 한 것으로, 플레이어가 목표 지점에 도달했는지 여부를 판단하는 함수이다. 플레이어가 가장 오른쪽 아래에 도달하면 true를 반환하여 게임을 클리어하게 된다.
* ```displayMaze()``` 함수는 현재 미로 상태를 화면에 출력하는 함수이다. 미로의 각 셀에는 벽, 빈 공간, 플레이어, 목표지점이 표시되며 플레이어의 기회(체력)도 표시된다.

## 메인 함수수
```C++
int main() {
    Maze maze;
    MazeGameImpl* mazeGameImpl = &maze;
    MazeGame mazeGame(mazeGameImpl);

    cout << "미로 게임을 시작합니다. 목표: '!'에 도달하세요!" << endl;
    mazeGame.play();

    return 0;
}
```
* ```Maze``` 클래스의 객체 ```maze```를 생성한다.
	- 이 객체는 미로 게임의 초기 상태를 나타낸다.
* ```Maze``` 클래스 객체를 가리키는 ```MazeGameImpl```포인터 ```mazeGameImpl```을 생성한다.
	- 이는 브릿지 패턴에서 사용되는 게임 구현의 추상화를 담당한다.
* ```MazeGame``` 클래스의 객체 ```mazeGame```을 생성하고 앞서 생성한 ```mazeGameImpl```포인터를 주입하여 초기화한다.
	- 이 부분에서 브릿지 패턴이 사용되어 게임의 구현과 추상화가 느슨하게 결합된다.
* 플레이어에게 시작 메시지를 출력한다.
* ```MazeGame``` 클래스의 ```play()``` 함수를 호출하여 미로 게임을 진해안다. 이 함수는 초기화된 미로 생태에서 플레이어의 입력을 받아 게임을 진행하고 목표 지점에 도달할 때까지 반복된다.
