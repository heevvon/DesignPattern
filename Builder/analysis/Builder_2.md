# 빌더 패턴 예제 코드 분석
## 분석할 코드
```c++
#include <iostream>
#include <string>
#include <vector>
#include <sstream>
#include <memory>
using namespace std;

struct HtmlBuilder;

struct HtmlElement {
    string name;
    string text;
    vector<HtmlElement> elements;
    const size_t indent_size = 2;

    HtmlElement() {}
    HtmlElement(const string& name, const string& text)
        : name(name), text(text){}

    string str(int indent = 0) const {
        ostringstream oss;
        string i(indent_size * indent, ' ');
        oss << i << "<" << name << ">" << endl;
        if (text.size() > 0)
            oss << string(indent_size * (indent + 1), ' ') << text << endl;

        for (const auto& e : elements)
            oss << e.str(indent + 1);

        oss << i << "</" << name << ">" << endl;
        return oss.str();
    }

    static unique_ptr<HtmlBuilder> build(string root_name) {
        return make_unique<HtmlBuilder>(root_name);
    }
};

struct HtmlBuilder {
    HtmlBuilder(string root_name) {
        root.name = root_name;
    }

    HtmlBuilder& add_child(string child_name, string child_text) {
        HtmlElement e{ child_name, child_text };
        root.elements.emplace_back(e);
        return *this;
    }

    HtmlBuilder* add_child_2(string child_name, string child_text) {
        HtmlElement e{ child_name, child_text };
        root.elements.emplace_back(e);
        return this;
    }

    string str() { return root.str(); }

    operator HtmlElement() const { return root; }
    HtmlElement root;
};

int main() {
    auto text = "hello";
    string output;
    output += "<p>";
    output += text;
    output += "</p>";
    cout << "<p>" << text << "</p>" << endl;

    string words[] = { "hello", "world" };
    ostringstream oss;
    oss << "<ul>";
    for (auto w : words)
        oss << "  <li>" << w << "</li>";
    oss << "</ul>";
    cout << oss.str() << endl;

    HtmlBuilder builder{ "ul" };
    builder.add_child("li", "hello").add_child("li", "world");
    cout << builder.str() << endl;

    HtmlBuilder builder2 = *HtmlElement::build("ul")
        ->add_child_2("li", "hello")
        ->add_child_2("li", "world");
    cout << builder2.str() << endl;

    return 0;
}
```

## 코드 분석
### HtmlElement
```c++
struct HtmlElement {
    string name;
    string text;
    vector<HtmlElement> elements;
    const size_t indent_size = 2;

    HtmlElement() {}
    HtmlElement(const string& name, const string& text)
        : name(name), text(text){}

    string str(int indent = 0) const {
        ostringstream oss;
        string i(indent_size * indent, ' ');
        oss << i << "<" << name << ">" << endl;
        if (text.size() > 0)
            oss << string(indent_size * (indent + 1), ' ') << text << endl;

        for (const auto& e : elements)
            oss << e.str(indent + 1);

        oss << i << "</" << name << ">" << endl;
        return oss.str();
    }

    static unique_ptr<HtmlBuilder> build(string root_name) {
        return make_unique<HtmlBuilder>(root_name);
    }
};
```
* ```HtmlElement```구조체는 HTML 요소를 표현하는 구조체로, 이름(name), 텍스트(text), 하위 요소들(elements)을 포함한다.
* ```indent_size```는 들여쓰기에 사용될 크기를 나타내는 상수이다. 들여쓰기 크기를 2로 설정했으므로, 2칸씩 들여쓰기가 적용된다.
* 이후 이름과 텍스트를 인자로 받아 ```HtmlElement``` 객체를 초기화한다.
* ```str```함수는 현재 HTML 요소를 문자열 형태로 반환하는 함수로, 들여쓰기를 적용하여 HTML 요소를 문자열로 표현한다.
* ```build```함수는 ```HtmlBuilder``` 객체를 생성하는 정적 함수이다.

### HtmlBuilder
```c++
struct HtmlBuilder {
    HtmlBuilder(string root_name) {
        root.name = root_name;
    }

    HtmlBuilder& add_child(string child_name, string child_text) {
        HtmlElement e{ child_name, child_text };
        root.elements.emplace_back(e);
        return *this;
    }

    HtmlBuilder* add_child_2(string child_name, string child_text) {
        HtmlElement e{ child_name, child_text };
        root.elements.emplace_back(e);
        return this;
    }

    string str() { return root.str(); }

    operator HtmlElement() const { return root; }
    HtmlElement root;
};
```
* ```HtmlBuilder```는 HTML 요소를 구성하기 위한 빌더 역할을 한다.
* 생성자 ```HtmlBuilder(string root_name)```은 빌더를 초기화하는데 사용된다. 이때 ```root_name```은 빌더의 루트 요소 이름으로 설정된다.
* ```add_child``` 함수는 자식 요소를 추가하는 메서드이다. 이 함수는 ```child_name```과 ```child_text```를 받아 해당 요소를 생성하고, 루트 요소의 하위 요소로 추가한다.
* ```add_child_2``` 함수는 포인터를 반환하는 버전의 ```add_child``` 함수이다. 동일한 기능을 수행하지만, 반환 형식이 다르다.
* ```str()``` 함수는 현재 빌더가 가지고 있는 루트 요소를 문자열로 변환하여 반환한다.
* ```operator HtmlElement() const```는 ```HtmlBuilder```를 ```HtmlElement```로 변환하는 연산자 오버로딩을 정의한다. 이를 통해 ```HtmlBuilder``` 객체를 ```HtmlElement```로 변환할 수 있다.

### Main
```c++
int main() {
    auto text = "hello";
    string output;
    output += "<p>";
    output += text;
    output += "</p>";
    cout << "<p>" << text << "</p>" << endl;

    string words[] = { "hello", "world" };
    ostringstream oss;
    oss << "<ul>";
    for (auto w : words)
        oss << "  <li>" << w << "</li>";
    oss << "</ul>";
    cout << oss.str() << endl;

    HtmlBuilder builder{ "ul" };
    builder.add_child("li", "hello").add_child("li", "world");
    cout << builder.str() << endl;

    HtmlBuilder builder2 = *HtmlElement::build("ul")
        ->add_child_2("li", "hello")
        ->add_child_2("li", "world");
    cout << builder2.str() << endl;

    return 0;
}
```
* ```<p>, text, </p>``` 문자열을 직접 조합하여 출력한다.
* 문자열 스트림을 사용하여 ```words``` 배열의 요소를 ```<ul>```과 ```<li>```로 묶어 문자열을 조합한 후 출력한다.
* 이후 객체 지향적인 방법으로 HTML 요소를 생성하고 출력한다.
* 빌더 패턴을 사용하여 HTML 요소를 구성하고, ```HtmlBuilder```를 통해 HTML 요소를 문자열로 변환하여 출력한다.

다양한 방식으로 HTML을 생성하고 출력한다. 직접 문자열을 조합, 문자열 스트림을 사용하여 조합하거나 객체 지향적인 방법으로 빌더 패턴을 활용하여 HTML 요소를 조립한 후 문자열로 변환하여 출력하는 방법을 보여준다.

## 출력 결과
```c++
<p>hello</p>
----------
<ul>
  <li>hello</li>
  <li>world</li>
</ul>
----------
<ul>
  <li>
    hello
  </li>
  <li>
    world
  </li>
</ul>

----------
<ul>
  <li>
    hello
  </li>
  <li>
    world
  </li>
</ul>
```
