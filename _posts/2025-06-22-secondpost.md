---
layout: single
title:  "윤성우의 열혈 C++ 프로그래밍 : Ch 5. 복사 생성자"
excerpt: "교재 '윤성우의 열혈 C++ 프로그래밍'으로 공부하는 C++ 내용 중 복사 생성자에 관한 정리입니다."

categories:
  - Cpp
tags:
  - [Cpp,copyconstructor]
 
date: 2025-06-22
---

객체를 생성하기 위해 생성자를 사용할 수 있고, 그에 대응되는 개념으로 소멸자가 존재한다. 해당 단원에서는 그러한 일반적인 생성자를 사용하는 것이 아닌, "복사 생성자(Copy Constructor)"로도 객체가 생성될 수 있으며 디폴트 복사 생성자에 따른 "얕은 복사(Shallow Copy)"와 "깊은 복사(Deep Copy)"의 차이 등에 대해 알려준다.

책에서 제시하는 예시 코드와 함께 보자.
```cpp
#include <iostream>
using std::cin;
using std::cout;
using std::endl;

class SoSimple
{
private:
	int num1;
	int num2;

public:
	SoSimple(int n1, int n2) : num1(n1),num2(n2)
	{ }
	SoSimple(SoSimple& copy) : num1(copy.num1), num2(copy.num2)      //복사 생성자 정의
	{
		cout << "Called SoSimple(SoSimple &copy)" << endl;
	}
	void ShowSimpleData()
	{
		cout << num1 << endl;
		cout << num2 << endl;
	}
};

int main()
{
	SoSimple sim1(15, 30);
	cout << "생성 및 초기화 직전" << endl;
	SoSimple sim2 = sim1;    //SoSimple sim2(sim1); 으로 변환!
	cout << "생성 및 초기화 직후" << endl;
	sim2.ShowSimpleData();

	return 0;
}

```
Cpp의 모든 객체는 생성자의 호출을 동반한다고 하였다. 아래 main함수에서 볼 수 있듯이, 복사 생성자를 정의해두면 참조자로 어떤 변수를 참조하듯이 객체를 복사하여 생성할 수 있다. (근데... 참조자로 복사해서 생성하지 말고 그냥 변수 대입하듯이 어떤 객체의 값들을 그대로 복사해서 새로이 생성하는 생성자는 없나?)<br>

아무튼, <br>
```cpp
int num = 3;
int &num2 = num;
```
이처럼, 
```
 SoSimple sim2 = sim1; 
 SoSimple sim2(sim1);  //위 코드는 해당 형태로 묵시적 변환된다.
 ```
이러한 형태로 객체를 생성할 수 있는 것이다. <br> 저자 피셜, 좀 더 안전하게 복사 생성자를 표현하면 아래와 같은 형태로 쓸 수 있는데, 
```cpp
SoSimple(const SoSimple& copy) : num1(copy.num1) , num2(copy.num2)
{ ... }
```
실수로 복사의 원본을 변경시키는 일이 발생할 수 있으니 복사의 주체가 들어오는 자리에 const 키워드를 붙여 실수를 막아 놓기 위함이라고.

#### 만약 복사 생성자를 정의하지 않았다면?
생성자가 그러하듯, 복사 생성자를 직접 정의하지 않아도 자동적으로 삽입되는 **디폴트 복사 생성자**가 있기 때문에 복사 생성자의 직접적 삽입 없이도 멤버 대 멤버의 복사가 진행되기는 한단다. 그치만! 직접 삽입하지 않으면 문제가 되는 경우가 있다. 즉, **반드시 복사 생성자를 정의해야 하는 경우도 있다.** 이 내용이 바로 "깊은 복사"와 "얇은 복사"로 이어지는 초석이 될 것이다.

...이러고 다음 내용으로 넘어가려는데...

<!--이미지 삽입. 나의 궁금증 해결. 충격!-->
위에서 제시한 나의 소소한(?)궁금증을 해결해주는 글을 참조로 써놓으셨다. 윤성우 그는 신인가?
>참조형의 선언을 의미하는 &는 반드시 삽입해야 한다. ... 결론만 이야기한다면, & 선언이 없다면 복사 생성자의 호출은 무한루프에 빠져버린다.

~~심지어 나는 여기에 밑줄도 쳐놓았다...왜 기억이 안날까? ㅠㅠ~~
이번 Chapter를 마지막까지 공부하면 알 수 있다고 하니, 참을성을 가지고 마저 공부해보기로 하자. 

## 깊은 복사와 얕은 복사
디폴트 복사 생성자는 멤버 대 멤버의 복사를 진행하고, 이러한 방식의 복사를 얕은 복사라 하는데 이 때 문제가 될 수 있는 경우는 **멤버변수가 힙의 메모리 공간을 찹조하는 경우**라고 한다. 아래에 제시한 예제를 참고하자. 

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstring>
using namespace std;

class Person
{
private:
	char* name;
	int age;
public:
	Person(const char* myname, int myage)
	{
		name = new char[strlen(myname) + 1]; //char* type name 동적할당
		strcpy(name, myname);

		age = myage;
	}
	void ShowPersonInfo() const
	{
		cout << "이름: " << name << endl;
		cout << "나이: " << age << endl;
	}
	~Person()
	{
		delete[] name;
		cout << "called destructor!" << endl;
	}
};

int main()
{
	Person man1("Lee dong woo", 29);
	Person man2(man1);

	man1.ShowPersonInfo();
	man2.ShowPersonInfo();

	return 0;
}
```

해당 코드를 실행시키면 에러가 발생하고, 소멸자의 출력 문구인 "called destuctor!"도 한번밖에 실행되지 않는다. 생성자를 통해 생성된 man1, 복사 생성자를 통해 생성된 man2 두개의 객체에도 불구하고 말이다!

일차적으로 생각하기엔, Person 생성자 내부에는 생성자를 통해 들어온 myname을 저장해줄 공간을 동적할당해주는 코드가 있는 반면 디폴트 복사 생성자를 사용한 현 코드에서는 복사된 객체의 이름을 저장할 메모리공간을 확보할 수 없었기 때문이겠지. 

해당 내용을 확장해서, 위 코드의 메모리 구조를 도식화한 그림을 보자.

<img src="docs/assets/images/copyconstructor_img2.jpg" width="90%" height="90%" title="img1" alt=" - "/> 
<!--![copyconstructor_errormemorydisplay](docs/assets/images/copyconstructor_img2.jpg)-->

>디폴트 복사 생성자는 멤버 대 멤버의 단순 복사를 진행하기 때문에 복사의 결과로 하나의 문자열을 두 개의 객체가 동시에 참조하는 꼴을 만들어버린다. ... 이로 인해서 객체의 소멸과정에서 문제가 발생한다.

아래 그림은 man2 객체가 소멸된 상황이다.

<img src="docs/assets/images/copyconstructor_img1.jpg" width="90%" height="90%" title="img2" alt=" - "/> 
<!--![copyconstructor_man2objectdeleted](docs/assets/images/copyconstructor_img1.jpg)-->

man2가 소멸되고 남은 man1도 소멸되려 할 때, man1의 멤버인 name이 참조하는 문자열이 이미 소멸된 상태이다. 그렇기에 소멸자의 delete[]문장의 실행은 이미 지워진 문자열을 대상으로 지우겠다는 뜻이 되어, 문제가 된다.

이 문제를 해결하려면 간단히, 복사 생성자를 정확히 정의하면 되겄지.
```cpp
...
Person(const Person& copy) : int age(copy.age)
{
	name = new char[strlen(copy.name)+1];
	strcpy(name, copy.name);

}
```
하나의 문자열을 두 객체가 참조하지 않도록 복사 생성자에도 동적할당 문장을 잘 넣어주자.

## 복사 생성자의 호출시점

저자는 복사 생성자가 호출되는 시점을 크게 세가지로 구분해준다.(편하다!)

1. 기존에 생성된 객체를 이용해서 새로운 객체를 초기화하는 경우
2. Call-by-value 방식의 함수호출 과정에서 객체를 인자로 전달하는 경우
3. 객체를 반환하되, 참조형으로 반환하지 않는 경우

이들의 공통점은 다음과 같다.

>객체를 새로 생성해야 한다. 단, 생성과 동시에 동일한 자료형의 객체로 초기화해야 한다.

동일한 자료형의 객체로 초기화와 동시에 생성! 이것이 복사 생성자의 호출 조건이라고. 
아래 예제를 보자. 

```cpp
#include <iostream>
using namespace std;

class SoSimple
{
private:
	int num;

public:
	SoSimple(int n) : num(n) { }

	SoSimple(const SoSimple& copy) : num(copy.num)
	{
		cout << "Called SoSimple(const SoSimple& copy)" << endl;
	}

	void ShowData()
	{
		cout << "num: " << num << endl;
	}
};

void SimpleFuncObj(SoSimple ob)
{
	ob.ShowData();
}

int main()
{
	SoSimple obj(7); //생성자를 통한 객체생성
	cout << "함수호출 전" << endl;
	SimpleFuncObj(obj);
	cout << "함수호출 후" << endl;

	return 0;
}
```
해당 예제 실행시, 놀랍게도 아래의 결과가 나온다.

```
함수호출 전
Called SoSimple(const SoSimple& copy)
num: 7
함수호출 후
```

복사 생성자가 호출되었다는 얘긴데! 
일단 main함수의 SimpleFuncObj(obj);에서 객체가 생성되었다는 건 알겠다.
함수의 매개변수 부분에서 다음과 같은 초기화 및 객체 생성이 이루어졌겠지.
```
SoSimple ob = obj
```
이는 위에서 얘기한 복사 생성자의 호출 조건 중 2번, Call-by-value 방식의 함수호출 과정에서 객체를 인자로 전달하는 경우에 대한 예제이다. 함수의 매개변수 타입이 SoSimple type이며 *연산자나 &연산자가 붙어있지 않은 것을 봐서도 Call-by-value임을 예상할 수 있는 것!<br>
(근데 왜 복사 생성자의 매개변수 type은 참조자로 받아야 하는 건데? 이 궁금증은 아직도 해소되지 않았다)

다음으로 복사 생성자가 호출되는 조건 3 - 객체를 반환하되, 참조형으로 반환하지 않은 경우에 대한 예제이다.

```cpp
#include <iostream>
using namespace std;

class SoSimple
{
private:
	int num;
public:
	SoSimple(int n) : num(n) { }
	SoSimple(const SoSimple& copy) : num(copy.num)
	{
		cout << "Called SoSimple(const SoSimple& copy)" << endl;
	}
	SoSimple& AddNum(int n) //얘는 생성자가 아니라 return type이 SoSimple의 참조형인 함수!
	{
		num += n;
		return *this;
	}
	void ShowData()
	{
		cout << "num: " << num << endl;
	}
};

SoSimple SimpleFuncObj(SoSimple ob) //얘는 SoSimple 객체를 참조형이 아닌 그대로 return하는 함수!
{
	cout << "return 이전" << endl;
	return ob;
}

int main()
{
	SoSimple obj(7);
	SimpleFuncObj(obj).AddNum(30).ShowData(); //SimpleFuncObj의 return type은 참조형이 아닌 SoSimple 객체 - 복사생성자 호출, 객체 생성 ->그렇기에 바로 AddNum을 그 뒤에서 호출할 수 있었던 것!
	obj.ShowData();

	return 0;
}

```
실행결과
```
Called SoSimple(const SoSimple& copy)
return 이전
Called SoSimple(const SoSimple& copy)
num: 37
num: 7
```
생성 결과를 보면 SoSimple class의 복사 생성자가 두 번 호출되었음을 알 수 있다.<br> 첫번째는 함수의 매개변수를 통해 생성된 객체(여기선 SimpleFuncObj의 매개변수인 ob가 obj를 통해 초기화됨)일 테고, 두번째는 객체가 함수의 return값으로 튀어나오면서 생성된 것이겠다.<br> 첫번째 ShowData()가 실행된 객체는 obj를 통해 초기화된 객체(복사생성자 호출 1)가 return으로 튀어나오며(복사생성자 호출 2)생성된 객체의 함수이고 두번째 ShowData는 맨 처음 초기화했던 obj를 통해 실행시킨 것이다. 둘의 결과가 다른 것으로, 둘은 서로 다른 객체임을 확인할 수 있다.

다만 SimpleFuncObj(obj)에서 SoSimple 타입의 객체를 Call-by-value 방식의 함수에 매개변수로 전달해주며 생성된 객체 ob와, 그 ob의 값이 return되며 생성되는 객체 간의 관계성이 좀 헷갈린다. 언뜻 생각해봐서는 객체 ob가 그대로 튀어나온 것처럼 느껴져, 생성자가 두 번이나 호출될 필요가 있는지 의문이 들기도 한다. 이러한 의문에, 저자는 이렇게 답한다. 

>객체를 반환하게 되면 **'임시객체'**라는 것이 생성되고, 이 객체의 복사 생성자가 호출되면서 return문에 명시된 객체가 인자로 전달된다. **즉, 최종적으로 반환되는 객체는 새롭게 생성되는 임시객체이다.** ...함수호출이 완료되고 나면 지역적으로 선언된 객체 ob는 소멸되고 obj 객체와 임시객체만 남는다. 

그러니까 함수 내에서의 객체 ob가 이어달리기에서 바톤 넘겨주듯이 새로운 객체에게 자신의 정보들을 복사 생성자로 넘겨주고 자기는 소멸되는 과정이 일어나는 듯...
#### 그렇다면 임시 객체는 언제 소멸되는가?
이에 대해서도 이런저런 얘기가 많지만, 결론만 말하면
1. 임시객체는 다음 행으로 넘어가면 바로 소멸되어 버린다.
2. 참조자에 참조되는 임시객체는 바로 소멸되지 않는다.

고 한다. 잘 기억해두기!


이정도로 Ch 3는 마무리한다. 사실 OOP 프로젝트 복습해보려 하니 복사 생성자에 대한 내용이 기억이 잘 나지 않아서 정리했는데 분량이 꽤 길어진듯...! 그래도 확실히 복습은 잘 되었다. 이 기억 제발 오래오래 가져갈 수 있기를...!!
