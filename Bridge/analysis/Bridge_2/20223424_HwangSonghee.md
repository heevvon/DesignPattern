# 분석 결과

## 렌더러 인터페이스(객체가 어떻게 그려지는지 결정한다.)
```C++
#include <iostream>
#include <string>
#include <vector>
#include "Person.h"
using namespace std;

struct Renderer
{
  virtual void render_circle(float x, float y, float radius) = 0;
};
```
* ```render_circle``` 함수는 원을 렌더링하는 순수가상함수이며, 파라미터로 ```x```, ```y```, ```radius(반지름)```를 받는다.
  - ```render_circle``` 함수는 파생 클래스에서 반드시 재정의를 해야한다.

## 벡터렌더러 구조체
```C++
struct VectorRenderer : Renderer
{
  void render_circle(float x, float y, float radius) override
  {
    cout << "Rasterizing circle of radius " << radius << endl;
  }
};
```
* ```Renderer``` 구조체를 상속받고 있다. 이를 통해 ```VectorRenderer``` 구조체는 ```Renderer``` 구조체의 멤버 변수와 멤버 함수를 사용할 수 있게 된다.
* ```Renderer``` 구조체의 순수 가상 함수인 ```render_circle``` 함수를 구현하고 있다.
* ```override``` 키워드를 통해 해당 함수가 부모 클래스에서 가상함수로 선언된 함수를 재정의하고 있음을 알 수 있다.
* 함수가 실행되면 "Rasterizing circle of radius"(반지름를 이용하여 원을 렌더링한다.)라는 메시지를 출력 후 반지름 값을 출력한다.

## 래스트렌더러 구조체
```C++
struct RasterRenderer : Renderer
{
  void render_circle(float x, float y, float radius) override
  {
    cout << "Drawing a vector circle of radius " << radius << endl;
  }
};
```
* ```Renderer``` 구조체를 상속받고 있다.
* ```Renderer``` 구조체의 함수인 ```rneder_circle``` 함수를 ```override```를 통해 한번 더 재정의하고 있다.
* 함수가 실행되면 "Drawing a vector circle of radius"(반지름을 이용하여 원을 그린다.)를 출력하고 반지름을 출력한다.

## 모양 구조체(무엇을 그릴지 결정한다.)
```C++
struct Shape
{
protected:
  Renderer& renderer;
  Shape(Renderer& renderer) : renderer{ renderer } {}
public:
  virtual void draw() = 0; // 구현 종속적이다.
  virtual void resize(float factor) = 0; // 추상화에 종속적이다.
};
```
* ```Renderer``` 구조체에 대한 참조를 멤버 변수로 가지고 있다. 렌더링을 수행하기 위해서 ```Renderer```과 연결되어 있다.
* 생성자를 통해 렌더러에 대한 참조를 받아들이고, 멤버 초기화 목록을 사용하여 ```renderer``` 멤버 변수를 초기화한다.
* ```draw``` 함수는 순수가상함수로 파생 구조체에서 재정의를 해주어야한다. ```draw``` 함수는 도형을 그리는 역할을 함다.
* ```resize``` 함수 또한 순수가상함수로 파생 구조체에서 재정의를 해주어야한다. ```resize``` 함수는 도형의 크기를 조정하는 역할을 하며 ```factor``` 매개 변수를 가지고 있다.

## 원 구조체
```C++
struct Circle : Shape
{
  float x, y, radius;

  void draw() override
  {
    renderer.render_circle(x, y, radius);
  }

  void resize(float factor) override
  {
    radius *= factor;
  }

  Circle(Renderer& renderer, float x, float y, float radius)
    : Shape{renderer},
      x{x},
      y{y},
      radius{radius}
  {
  }
};
```
* ```Circle``` 구조체는 ```Shape``` 구조체를 상속한다. 즉, ```Shape``` 구조체의 멤버 변수와 멤버 함수를 사용할 수 있다.
* ```x```, ```y```, ```radius(반지름)```를 멤버 변수를 가지고 있다.
* ```draw``` 함수는 ```Shape``` 구조체의 가상함수를 구현하고 있다. ```draw``` 함수는 현재 원을 렌더러에 그리도록 구현되어있다.
* ```resize``` 함수 또한 ```Shape``` 구조체의 가상함수를 구현하고 있다. ```resize``` 함수는 현재 원의 반지름을 주어진 ```factor``` 값에 따라 곱하여 조절한다.
* 주어진 ```factor```가 1보다 크면 원이 확대되고 1보다 작으면 원이 축소된다.
* ```Circle(Renderer& renderer, float x, float y, float radius)``` 생성자는 렌더러와 초기 운의 상태를 받아들이고, 멤버 초기화 목록을 사용하여 ```Shape``` 구조체의 생성자를 호출하고 ```x```, ```y```, ```radius```를 초기화한다.

## 브릿지 함수
```C++
void bridge()
{
  RasterRenderer rr;
  Circle raster_circle{ rr, 5,5,5 };
  raster_circle.draw();
  raster_circle.resize(2);
  raster_circle.draw();
}
```
* ```ResterRenderer``` 구조체의 객체 ```rr```을 생성한다.
    - ```Renderer``` 구조체를 상속한 구체적인 렌더러로 비트맵 이미지 등을 그릴 수 있는 런더러이다.
* ```ResterRenderer``` 객체 ```rr```을 사용하여 ```Circle``` 구조체의 객체 ```raster_circle```을 생성한다. 이 객체는 중심 좌표가 (5, 5)이고 반지름이 5인 원이다.
* ```raster_circle``` 객체의 ```draw``` 함수를 호출하여 렌더러에게 원을 그리도록 한다.
* ```raster_circle``` 객체의 ```resize``` 함수를 호출하여 현재 원의 크기를 2배로 늘린다.
* 다시 ```draw``` 함수를 호출하여 크기가 조절된 원을 다시 그린다.
* **브릿지 패턴을 통해 렌더러와 도형 사이의 다리 역할을 한다.**

## 메인 함수
```C++
int main()
{
  

  Person p;
  p.greet();

  getchar();
  return 0;
}
```
* ```Person``` 구조체의 객체 ```p```를 생성한다.
* ```p```객체의 ```greet``` 함수를 호출한다.
* ```getchar```함수는 프로그램이 사용자의 입력을 받을 때까지 대기하며 사용자가 키를 누를 때까지 실행을 일시 중단 시킨다.
