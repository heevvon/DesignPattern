# 분석할 예제 코드
```C++
#include <iostream>
#include <string>
using namespace std;

class BTree { };

class SearchEngineImp {
  public :
    virtual bool Search(string s, string idxFn) = 0;
    virtual bool Search(string s, BTree& bTree) = 0;
};

class UnixSearchEngineImp : public SearchEngineImp {
  public :
    bool Search(string s, string idxFn) {
      return true;
    }
    bool Search(string s, BTree& bTree) {
      return true;
    }
};

class WindowsSearchEngineImp : public SearchEngineImp {
  public :
    bool Search(string s, string idxFn) {
      return true;
    }
    bool Search(string s, BTree& bTree) {
      return true;
    }
};

class SearchEngine {
  public :
    SearchEngine() { pImp_ = 0; }
    virtual bool Search(string s) = 0;

  protected :
    SearchEngineImp* GetSearchEngineImp() {
      if (pImp_ == 0) {
#ifdef  __WIN32__
        pImp_ = new WindowsSearchEngineImp;
#else
        pImp_ = new UnixSearchEngineImp;
#endif
      }
      return pImp_;
    }

  private :
    SearchEngineImp* pImp_;
};

class WebSearchEngine : public SearchEngine {
  public :
    WebSearchEngine(string idxFn) { indexFn_ = idxFn; }
    bool Search(string s) {
      return GetSearchEngineImp()->Search(s, indexFn_);
    }

  private :
    string indexFn_;
};

class DBSearchEngine : public SearchEngine {
  public : 
    bool Search(string s) {
      return GetSearchEngineImp()->Search(s, bTree_);
    }

  private :
    BTree bTree_;
};

void 
main() 
{
  WebSearchEngine finder("inverted_file4web.idx");
  finder.Search("디자인 패턴");
}
```

## BTree 클래스
```C++
#include <iostream>
#include <string>
using namespace std;

class BTree { };
```
- 입출력을 다루는 헤더파일이 선언됐다.
- 문자열을 다루는 헤더파일이 선언됐다.
- 네임스페이스를 선언하여 코드를 간결하게 작성했다.
- ``BTree`` 클래스는 B트리를 나타내기 위한 빈 클래스로, 아무런 멤버 변수나 멤버 함수를 가지고 있지 않다.

## SearchEngineImp 클래스
```C++
class SearchEngineImp {
public:
    virtual bool Search(`string s, string idxFn) = 0;
    virtual bool Search(`string s, BTree& bTree) = 0;
};
```
- ``SearchEngineImp`` 클래스는 검색 엔진의 구현에 대한 인터페이스를 제공하는 추상 기본 클래스이다.
  > 'Bridge' 패턴과 연관이 있는데, 구현과 추상화를 분리하여 클라이언트 코드가 검색 엔진의 구체적인 구현에 종속되지 않도록 한다.
- ``SearchEngineImp`` 클래스는 추상 클래스이기 때문에 객체를 직접 생성할 수 없다.
- ``SearchEngineImp`` 클래스의 목적은 인터페이스를 정의하여 다양한 검색 엔진의 구현에서 동일한 인터페이스를 유지하도록 하는 것이다.
- ``Search`` 함수들은 검색을 수행하며, 하나는 인덱스 파일 이름을 받고, 다른 하나는 ``BTree`` 객체를 받는다.
  - 함수들은 순수 가상함수로 선언되어 있어 구현이 없으며, 파생 클래스에서 반드시 재정의되어야 한다.
  > ``Search`` 함수는 함수 오버로딩이 된 함수이다.
- 이 클래스는 구현과 추상화를 분리하여, 검색 엔진의 구현을 변경하더라도 클라이언트 코드에 영향을 미치지 않게 한다.

## UnixSearchEngineImp 클래스
```C++
class UnixSearchEngineImp : public SearchEngineImp {
public:
    bool Search(string s, string idxFn) override;
    bool Search(string s, BTree& bTree) override;
};
```
- ``UnixSearchEngineImp`` 클래스는 ``SearchEngineImp`` 클래스를를 상속하여, 검색 엔진의 구현을 확장하고 있다.
- 부모 클래스에서 가상함수로 선언된 ``Search`` 함수를 오버라이딩한다.
- ``Search(string s, string idxFn)`` 함수
  - 문자열 's'와 문자열 'idxFn'을 매개변수로 받아 검색을 수행하는 가상함수이다.
  - 실제로는 검색 로직이 구현되어있지 않고, 항상 ``true``를 반환하도록 되어있으며, 실제 검색 로직은 파생 클래스에서 구현해야한다.
- ``Search(string s, BTree& bTree)`` 함수
  - 문자열 's'와 ``BTree`` 객체 ``bTree``를 매개변수로 받아 검색을 수행하는 가상함수이다.
  - 실제로는 검색 로직이 구현되어있지 않고, 항상 ``true``를 반환하도록 되어있으며, 실제 검색 로직은 파생 클래스에서 구현해야한다.

## WindowsSearchEngineImp 클래스
```C++
class WindowsSearchEngineImp : public SearchEngineImp {
public:
    bool Search(string s, string idxFn) override;
    bool Search(string s, BTree& bTree) override;
};
```
- ``WindowsSearchEngineImp`` 클래스는 ``SearchEngineImp`` 클래스를 상속하며, Windows 운영 체제에 특화된 검색 엔진의 구현을 제공하는 클래스이다.
- 부모 클래스에서 가상 함수로 선언된 ``Search`` 함수를 오버라이딩한다.
- ``Search(string s, string idxFn)`` 함수
  - 문자열 's'와 문자열 'idxFn'을 매개변수로 받아 검색을 수행한다.
  - 현재 구현은 단순히 항상 ``true``를 반환하고 있지만, 실제 검색 로직은 여기에서 구현되어야 한다.
- ``Search(string s, BTree& bTree)`` 함수
  - 문자열 's'와 ``BTree`` 객체 ``bTree``를 매개변수로 받아 검색을 수행한다.
  - 현재 구현은 단순히 항상 ``true``를 반환하고 있지만, 실제 검색 로직은 여기에서 구현되어야 한다.
- 이 클래스는 구체적인 Windows 환경에서의 검색 알고리즘이나 데이터 구조에 따라서 해당 클래스를 구현 및 확장할 필요가 있다.

## SearchEngine 클래스
```C++
class SearchEngine {
  public :
    SearchEngine() { pImp_ = 0; }
    virtual bool Search(string s) = 0;

  protected :
    SearchEngineImp* GetSearchEngineImp() {
      if (pImp_ == 0) {
#ifdef  __WIN32__
        pImp_ = new WindowsSearchEngineImp;
#else
        pImp_ = new UnixSearchEngineImp;
#endif
      }
      return pImp_;
    }

  private :
    SearchEngineImp* pImp_;
};
```
- ``SearchEngine`` 클래스는 추상 클래스로, 다양한 검색 엔진을 사용할 수 있도록 인터페이스를 제공한다.
- ``SearchEngine`` 클래스는 추상 클래스이기 때문에 객체를 직접 생성할 수 없다.
- ``SearchEngine`` 클래스를 사용하면, 클라이언트 코드는 검색 엔진의 구현에 직접적으로 의존하지 않으면서, 다양한 플랫폼에서 다른 검색 엔진을 사용할 수 있다.
  > 이는 'Bridge'의 핵심이다.
- 멤버 변수 ``plmp_``
  - 검색 엔진의 구현 객체를 가리키는 포인터이다.
  - 초기에는 ``nullptr``로 초기화되어 있으며, 필요한 경우 ``GetSearchEngineImp`` 함수를 호출하여 구현 객체를 생성 및 설정한다.
- ``Search(string s)`` 함수
  - 순수 가상함수로 선언되어 있어 구현이 없지만, 파생 클래스에서 반드시 이를 구현해야 한다.
  - 문자열 's'를 매개변수로 받아 검색을 수행하는 추상 메서드이다.
  - 클라이언트 코드는 검색 엔진의 구현에 직접 의존하지 않고 사용할 수 있다.
- ``GetSearchEngineImp`` 함수
  - 보호된 멤버 함수로, 검색 엔진의 구현 객체를 얻기 위한 함수이다.
  - 만약 ``pImp_``가 초기화되지 않았다면, 플랫폼에 따라 다른 구현 객체를 생성하여 ``pImp_``에 할당한다.
  - 이 함수를 통해 ``pImp_``에 저장된 구현객체를 반환한다.
  - 검색 엔진 구현이 필요할 때 호출되며, 구현이 없는 경우에 한해 구현 객체를 생성하여 반환한다.
  - 'Bridge' 패턴의 일부로, 검색 엔진의 구현과 추상화를 분리하기 위한 것이다.
  - 구현 객체가 필요한 경우에만 생성되며, 불필요한 자원 사용을 방지한다.
- 조건부 컴파일 (``#ifdef __WIN32__``)
  - 플랫폼에 따라 다른 검색 엔진 구현을 선택하기 위한 부분이다.
  - ``__WIN32__``는 Windows 운영체제에서 정의되어 있는 매크로이다.
  - Windows 운영 체제인 경우, ``WindowsSearchEngineImp`` 클래스의 객체를 생성하여 ``pImp_``에 할당한다.
  - 그렇지 않은 경우, ``UnixSearchEngineImp`` 클래스의 객체를 생성하여 ``pImp_``에 할당한다.

## WebSearchEngine 클래스
```C++
class WebSearchEngine : public SearchEngine {
public:
    WebSearchEngine(string idxFn);
    bool Search(string s) override;

private:
    string indexFn_;
};
```
- ``WebSearchEngine`` 클래스는 ``SearchEngine`` 클래스를 상속하며, 웹 검색 엔진을 구현하는 클래스이다.
- 이 클래스 사용자는 검색 엔진의 구현과 관련된 부분에 대해 신경쓰지 않고도, 간단한 인터페이스를 통해 검색을 수행할 수 있다.
- 멤버 변수 ``indexFn``
  - 웹 검색 엔진이 사용할 인덱스 파일의 경로를 저장하는 문자열 멤버 변수이다.
- 생성자
  - 인덱스 파일 이름을 매개변수로 받는 생성자이다.
  -  멤버 변수 ``indexFn_``에 받은 인덱스 파일 이름을 저장한다.
- ``Search`` 함수
  - 부모 클래스에서 가상 함수로 선언된 ``Search`` 함수를 오버라이딩한다.
  - 문자열 's'를 매개변수로 받아, ``SearchEngineImp`` 객체를 통해 실제 검색을 수행한다.
  - 실제 검색 엔진의 구현은 ``SearchEngineImp`` 클래스에 위임되며, 이는 ``GetSearchEngineImp`` 함수를 통해 얻어진 구현 객체에 대해 수행된다.
 
## DBSearchEngine 클래스
```C++
class DBSearchEngine : public SearchEngine {
public:
    bool Search(string s) override;

private:
    BTree bTree_;
};
```
- ``DBSearchEngine`` 클래스는 ``SearchEngine`` 클래스를 상속하며, 데이터베이스 검색 엔진을 구현하는 클래스이다.
- 멤버 변수 ``bTree_``
  - 데이터베이스 검색에 사용되는 ``BTree`` 객체이다.
- ``Search`` 함수
  - 부모 클래스에서 가상 함수로 선언된 ``Search`` 함수를 오버라이딩한다.
  - 문자열 s를 매개변로 받아, ``SearchEngineImp`` 객체를 통해 실제 검색을 수행한다.
  - 실제 검색 엔진의 구현은 ``SearchEngineImp`` 클래스에 위임되며, 이는 ``GetSearchEngineImp`` 함수를 통해 얻어진 구현 객체에 대해 수행된다.
  - 부모 클래스에서 상속받은 ``GetSearchEngineImp()`` 함수를 호출하여 검색 엔진의 구현 객체를 얻어온 후, 이를 통해 실제 검색을 수행한다.
- ``DBSearchEngine`` 클래스는 자체적으로 ``BTree`` 객체를 가지고 있어, 데이터베이스 검색을 수행할 때 이를 활용한다.

## main 함수
```C++
void main() {
    WebSearchEngine finder("inverted_file4web.idx");
    finder.Search("디자인 패턴");
}
```
- ``main`` 함수의 반환 타입은 일반적 ``int``가 아니라 ``void``로 되어있다.
  > ``main`` 함수의 반환 타입이 ``void``인 것은 C++ 표쥰에 어긋나는 형태이기 때문에 웬만하면 ``int``로 나타내는 것이 좋다.
- ``WebSearchEngine`` 객체 생성
  - ``WebSearchEngine`` 클래스의 객체 ``finder``를 생성한다.
  - 생성자를 호출할 때, "inverted_file4web.idx"라는 문자열을 전달하여 멤버 변수 ``indexFn_``에 해당 값을 저장한다.
  - 생성자 내에서 ``SearchEngine`` 클래스의 생성자가 호출된다.
- 검색 수행
  - ``finder`` 객체의 ``Search`` 메서를 호출하여 "디자인 패턴"이라는 문자열에 대한 검색을 수행한다.
  - 내무적으로 ``Search`` 함수는 ``GetSearchEngineImp`` 함수를 호출하여 검색 엔진의 구현 객체를 얻어오고, 이를 통해 실제 검색을 수행한다.
  - ``WebSearchEngine`` 클래스는 해당 검색을 웹 검색 엔진으로 수행한다.
