# 빌더 패턴 예제 코드 분석
## 분석할 코드
```c++
class Product1{
    public:
    std::vector<std::string> parts_;
    void ListParts()const{
        std::cout << "Product parts: ";
        for (size_t i=0;i<parts_.size();i++){
            if(parts_[i]== parts_.back()){
                std::cout << parts_[i];
            }else{
                std::cout << parts_[i] << ", ";
            }
        }
        std::cout << "\n\n"; 
    }
};

class Builder{
    public:
    virtual ~Builder(){}
    virtual void ProducePartA() const =0;
    virtual void ProducePartB() const =0;
    virtual void ProducePartC() const =0;
};

class ConcreteBuilder1 : public Builder{
    private:
    Product1* product;

    public:
    ConcreteBuilder1(){
        this->Reset();
    }
    ~ConcreteBuilder1(){
        delete product;
    }

    void Reset(){
        this->product= new Product1();
    }
    void ProducePartA()const override{
        this->product->parts_.push_back("PartA1");
    }
    void ProducePartB()const override{
        this->product->parts_.push_back("PartB1");
    }
    void ProducePartC()const override{
        this->product->parts_.push_back("PartC1");
    }

    Product1* GetProduct() {
        Product1* result= this->product;
        this->Reset();
        return result;
    }
};

class Director{
    private:
    Builder* builder;
    public:
    void set_builder(Builder* builder){
        this->builder=builder;
    }
    void BuildMinimalViableProduct(){
        this->builder->ProducePartA();
    }   
    void BuildFullFeaturedProduct(){
        this->builder->ProducePartA();
        this->builder->ProducePartB();
        this->builder->ProducePartC();
    }
};

void ClientCode(Director& director)
{
    ConcreteBuilder1* builder = new ConcreteBuilder1();
    director.set_builder(builder);
    std::cout << "Standard basic product:\n"; 
    director.BuildMinimalViableProduct();
    
    Product1* p= builder->GetProduct();
    p->ListParts();
    delete p;

    std::cout << "Standard full featured product:\n"; 
    director.BuildFullFeaturedProduct();

    p= builder->GetProduct();
    p->ListParts();
    delete p;

    std::cout << "Custom product:\n";
    builder->ProducePartA();
    builder->ProducePartC();
    p=builder->GetProduct();
    p->ListParts();
    delete p;

    delete builder;
}

int main(){
    Director* director= new Director();
    ClientCode(*director);
    delete director;
    return 0;    
}
```
## 코드 분석
### Product
```c++
class Product1{
    public:
    std::vector<std::string> parts_;
    void ListParts()const{
        std::cout << "Product parts: ";
        for (size_t i=0;i<parts_.size();i++){
            if(parts_[i]== parts_.back()){
                std::cout << parts_[i];
            }else{
                std::cout << parts_[i] << ", ";
            }
        }
        std::cout << "\n\n"; 
    }
};
```
* Product1 클래스는 생성될 제품의 틀을 나타낸다.
* 이 클래스는 parts_라는 멤버 변수를 가지고 있어 제품의 부품들을 담는 역할을 한다.
* ListParts() 함수는 현재 제품에 포함된 부품들을 출력하는 역할.

### Builder Interface
```c++
class Builder{
    public:
    virtual ~Builder(){}
    virtual void ProducePartA() const =0;
    virtual void ProducePartB() const =0;
    virtual void ProducePartC() const =0;
};
```
* Builder 클래스는 객체를 생성하기 위한 단계들을 추상화한 인터페이스.
* ProducePartA(), ProducePartB(), ProducePartC() 함수들을 순수 가상 함수로 정의하여 이를 상속받는 구체적인 빌더 클래스에서 구현하도록 한다.

### Concrete Builder
```c++
class ConcreteBuilder1 : public Builder{
    private:
    Product1* product;

    public:
    ConcreteBuilder1(){
        this->Reset();
    }
    ~ConcreteBuilder1(){
        delete product;
    }

    void Reset(){
        this->product= new Product1();
    }
    void ProducePartA()const override{
        this->product->parts_.push_back("PartA1");
    }
    void ProducePartB()const override{
        this->product->parts_.push_back("PartB1");
    }
    void ProducePartC()const override{
        this->product->parts_.push_back("PartC1");
    }

    Product1* GetProduct() {
        Product1* result= this->product;
        this->Reset();
        return result;
    }
};
```
* ConcreteBuilder1 클래스는 Builder 인터페이스를 구현한 구체적인 빌더 클래스.
* 제품을 만드는 과정을 담당한다.
* ProducePartA(), ProducePartB(), ProducePartC() 함수를 구현하여 각 부품을 제작하고, GetProduct() 함수를 통해 최종 제품을 반환한다.

### Director
```c++
class Director{
    private:
    Builder* builder;
    public:
    void set_builder(Builder* builder){
        this->builder=builder;
    }
    void BuildMinimalViableProduct(){
        this->builder->ProducePartA();
    }   
    void BuildFullFeaturedProduct(){
        this->builder->ProducePartA();
        this->builder->ProducePartB();
        this->builder->ProducePartC();
    }
};
```
* Director 클래스는 빌더 클래스를 사용하여 제품을 조립하는 역할.
* set_builder() 함수를 통해 사용할 빌더를 설정하고, BuildMinimalViableProduct()와 BuildFullFeaturedProduct() 함수를 통해 최소한의 제품과 완전한 제품을 생성한다.
  
### Client
```c++
void ClientCode(Director& director)
{
    ConcreteBuilder1* builder = new ConcreteBuilder1();
    director.set_builder(builder);
    std::cout << "Standard basic product:\n"; 
    director.BuildMinimalViableProduct();
    
    Product1* p= builder->GetProduct();
    p->ListParts();
    delete p;

    std::cout << "Standard full featured product:\n"; 
    director.BuildFullFeaturedProduct();

    p= builder->GetProduct();
    p->ListParts();
    delete p;

    std::cout << "Custom product:\n";
    builder->ProducePartA();
    builder->ProducePartC();
    p=builder->GetProduct();
    p->ListParts();
    delete p;

    delete builder;
}
```
* ClientCode 함수는 Director를 생성하고, ConcreteBuilder1을 사용하여 다양한 종류의 제품을 생성하고 출력한다.

## 실행 결과
```c++
Standard basic product:
Product parts: PartA1

Standard full featured product:
Product parts: PartA1, PartB1, PartC1

Custom product:
Product parts: PartA1, PartC1
```
