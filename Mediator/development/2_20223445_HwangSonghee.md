## 헤더 파일
```c++
#include <stdlib.h> //랜덤함수가 들어가 있는 헤더파일
#include <time.h> //랜덤함수를 계속 다르게 하기 위한 함수가 들어있는 헤더파일
```
* 해더 파일 #include <stdlib.h>는 랜덤 함수가 들어있는 헤더 파일로 랜덤 함수인 rand를 사용하기 위해서 반드시 선언해 주어야 한다.
* 해더 파일 #include <time.h>는 랜덤 함수를 계속 다르게 출력하게끔 하기 위해 사용한다. 랜덤 함수만 사용하면 출력 시 계속 같은 결과만 출력되기 때문에 해당 헤더 파일을 이용하여 시드를 바꾸어 출력마다 랜덤하게 나올 수 있게 한다.

## 콘크리트 중재자 클래스
```C++
class ConcreteMediator : public Mediator {
public:
    void sendMessage(const User* sender, const string& message) const override {
        for (const User* user : users) {
            if (user != sender) {
                user->receiveMessage(sender, message);
            }
        }
    }
};
```
* 해당 클래스는 ```Mediator``` 클래스를 상속한다.
* ```sendMessage``` 함수는 ```sender```로부터 메시지를 수신하고 다른 모든 사용자에게 해당 메시지를 전송하는 역할을 한다.
* ```sendMessage``` 함수에서 ```user```는 ```ConcreteMediator``` 클래스에 있는 사용자 목록으로 사용자 간의 통신을 중재하는 데 사용된다.
* ```receiveMessage```는 ```user``` 클래스에서 구현된 함수로 메시지를 수신할 때 호출되어 특정 사용자에게 메시지를 전달한다.

## 콘크리트 유저 클래스
```C++
class ConcreteUser : public User {
public:
    ConcreteUser(Mediator* mediator, const string& name) : User(mediator, name){}

    void sendMessage(const string& message) const override {
        cout << userName << "이(가) 보낸 메시지: " << message << endl;
        cout << endl;
        mediator->sendMessage(this, message);
    }

    void receiveMessage(const User* sender, const string& message) const override {
        cout << userName << "이(가) " << sender->getName() << "에게 받은 메시지: " << message << endl;
        //정해진 메시지에 따라 자동으로 답변
        if (message == "안녕") {
            sendMessage("안녕! 나는 AIchat이야!");
        }
        else if (message == "반가워") {
            sendMessage("나도 반가워! AIchat을 재미있게 이용해주길 바라!");
        }
        else if (message == "재미있는 이야기 해줘") {
            //"재미있는 이야기 해줘"의 랜덤 답변을 위한 배열
            string rm[] = { "반성문을 영어로 하면? 글로벌!", "오렌지 먹은지 얼마나 오렌지!" ,
                            "고구마가 입대하면? 군고구마!", "인천앞바다의 반댓말은? 인천 엄마다!",
                            "세상에서 가장 가난한 왕은? 최처임금!" };
            int m = sizeof(rm) / sizeof(rm[0]); //배열의 요소수 계산
            srand(time(NULL)); //현재 시간을 난수 계열의 시드로 사용

            cout << rm[rand()%m] << endl; //답변을 랜덤하게 출력
        }
    }
};
```
* 해당 클래스는 ```User``` 클래스를 상속한다.
* ```sendMassage``` 함수는 사용자가 메시지를 보내는 역할을 한다. 메시지를 출력하고 중재자를 통해 메시지를 전송한다.
* ```receiveMessage``` 함수는 사용자가 다른 사용자로부터 메시지를 수신할 때 호출되는 함수이다. 수신한 메시지에 따라 정해진 규칙에 따라 자동으로 답변하도록 구현된다.
* ```receiveMassage``` 함수 내의 자동 답변은 특정한 메시지를 받으면 자동으로 답변한다. “안녕”, “반가워”, “재미있는 이야기 해줘” 등 메시지에 따라 다양한 답변을 해준다. “재미있는 이야기 해줘”의 경우 미리 정의된 문자열 배열에서 랜덤하게 답변을 출력한다.
