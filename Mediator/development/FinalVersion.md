# 중재자 패턴 프로그램 개발
## 프로그램
```C++
#include <iostream>
#include <string>
#include <vector>
#include <stdlib.h>
#include <time.h> 
using namespace std;

class User;

class Mediator {
protected:
    vector<User*> users;

public:
    void addUser(User* user) {
        users.push_back(user);
    }

    virtual void sendMessage(const User* sender, const string& message) const = 0;
};

class User {
protected:
    Mediator* mediator;
    string userName;

public:
    User(Mediator* mediator, const string& name) : mediator(mediator), userName(name) {
        mediator->addUser(this);
    }

    virtual void sendMessage(const string& message) const = 0;
    virtual void receiveMessage(const User* sender, const string& message) const = 0;

    const string& getName() const {
        return userName;
    }
};

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

class ConcreteUser : public User {
public:
    ConcreteUser(Mediator* mediator, const string& name) : User(mediator, name) {}

    void sendMessage(const string& message) const override {
        cout << userName << "이(가) 보낸 메시지: " << message << endl;
        cout << endl;
        mediator->sendMessage(this, message);
    }

    void receiveMessage(const User* sender, const string& message) const override {
        cout << userName << "이(가) " << sender->getName() << "에게 받은 메시지: " << message << endl;

        if (message == "안녕") {
            sendMessage("안녕! 나는 AIchat이야!");
        }
        else if (message == "반가워") {
            sendMessage("나도 반가워! AIchat을 재미있게 이용해주길 바라!");
        }
        else if (message == "재미있는 이야기 해줘") {
            string rm[] = { "반성문을 영어로 하면? 글로벌!", "오렌지 먹은지 얼마나 오렌지!" ,
                            "고구마가 입대하면? 군고구마!", "인천앞바다의 반댓말은? 인천 엄마다!",
                            "세상에서 가장 가난한 왕은? 최처임금!" };
            int m = sizeof(rm) / sizeof(rm[0]); 
            srand(time(NULL)); 
            cout << rm[rand() % m] << endl; 
        }
    }
};

int main() {
    ConcreteMediator mediator;

    ConcreteUser user1(&mediator, "황현");
    ConcreteUser user2(&mediator, "AI");

    user1.sendMessage("안녕"); 
    user1.sendMessage("반가워"); 
    user1.sendMessage("재미있는 이야기 해줘"); 

    return 0;

}
```
## 출력결과
```cin
황현이(가) 보낸 메시지: 안녕

AI이(가) 황현에게 받은 메시지: 안녕
AI이(가) 보낸 메시지: 안녕! 나는 AIchat이야!

황현이(가) AI에게 받은 메시지: 안녕! 나는 AIchat이야!
황현이(가) 보낸 메시지: 반가워

AI이(가) 황현에게 받은 메시지: 반가워
AI이(가) 보낸 메시지: 나도 반가워! AIchat을 재미있게 이용해주길 바라!

황현이(가) AI에게 받은 메시지: 나도 반가워! AIchat을 재미있게 이용해주길 바라!
황현이(가) 보낸 메시지: 재미있는 이야기 해줘

AI이(가) 황현에게 받은 메시지: 재미있는 이야기 해줘
세상에서 가장 가난한 왕은? 최처임금!
```
