---
layout: single
title: "윤성우의 열혈 C++ 프로그래밍 - OOP 단계별 프로젝트 (2)"
excerpt: "C++ 교재 내의 프로젝트 진행 - 2단계"

categories : 
    - Cpp
tags:
    - [윤성우의 열혈 C++,OOP Project]

date: 2025-06-24
---

## 윤성우의 열혈 C++, 프로젝트로 공부하기!

윤성우의 "열혈 C++ 프로그래밍"이라는(제법 오래된 책이다!) C++ 교재 안에는 챕터별로 배운 내용으로 진행할 수 있는 OOP 프로젝트가 있다. <br>
## 프로젝트 내용<br>
내용은 간단히 말해 '은행 계좌 관리 프로그램'이며, 주 기능은 총 4가지가 있다.<br>
1. 계좌 개설 <br>
2. 입금 <br>
3. 출금 <br>
4. 전체고객 잔액조회 <br>

또한, 해당 기능 구현을 위한 몇 가지 가정이 존재한다. 
+ 통장의 계좌번호는 중복되지 아니한다. (중복검사 하지 않겠다는 뜻).
+ 입금 및 출금액은 무조건 0보다 크다 (입금 및 출금액의 오류검사 않겠다는 뜻.)
+ 고객의 계좌정보는 계좌번호, 고객이름, 고객의 잔액, 이렇게 세가지만 저장 및 관리한다.
+ 둘 이상의 고객 정보 저장을 위해서 배열을 사용한다.
+ 계좌번호는 정수의 형태이다. 
<br>
이에 따른 실행 결과는 아래와 같다.<br>
```
-----Menu-----
1. 계좌개설
2. 입금
3. 출금
4. 계좌정보 전체 출력
5. 프로그램 종료
선택: 1

[계좌개설]
계좌ID: 115
이 름: 이우석
입금액: 15000

-----Menu-----
1. 계좌개설
2. 입금
3. 출금
4. 계좌정보 전체 출력
5. 프로그램 종료
선택: 2

[입   금]
계좌 ID: 115
입금액: 70
입금완료료
...
```
(이런식으로 예시가 주어져 있긴 하나, 출력에 있어서 디테일한 출력 내용은 조금 다르게 진행한 부분도 있다.)

<br>
해당 내용들을 잘 기억하며, 프로젝트를 진행하고 어려웠던 부분 내지는 중요한 부분 등을 위주로 기록해 볼 예정이다. <br><br>
<strong>참고로, 왜 프로젝트 1이 아니라 2부터 진행하냐면...</strong>

프로젝트 1단계는 C 문법으로 구현을 진행하는 복습 차원의 단계이기도 했고, 때문에 프로젝트 2와 구현하는 내용 자체는 같다. <br>
그치만 필요하다면 나중에라도 올려야겠다. 

그럼, OOP 단계별 프로젝트 02단계 렛츠기릿~!

## OOP 단계별 프로젝트 02단계 내용<br>
C++의 기원은 C로, 그 뿌리는 같으나 이 둘의 가장 큰 차이라고 하면 C는 절차 지향 언어, C++은 객체 지향 언어라는 점일 것이다. 그리고 그 차이의 근원이 되는 문법이라 하면 바로 "클래스"를 말할 수 있을 것이다.<br><br>
해당 프로젝트는 책 내용 중 Chapter 4. 클래스에 관한 단원의 끝부분에 제시되어 있으며, 그 단원의 취지와 맞게 1단계에서 구현했던 내용을 클래스를 이용하여 구현하는 것이 목적이다.<br>
추가적으로, 해당 프로젝트의 구현에 두 가지 조건이 추가로 걸리는데...
1. project 1에서 구현한 구조체의 경우 char형 배열을 멤버로 둬서 고객의 이름을 저장했는데, 여기서는 동적 할당의 형태로 구현하자.
2. 객체를 저장하는 배열에 있어서, 객체 포인터 배열을 선언해서 객체를 저장하자.


이러한 조건들을 바탕으로, 아래와 같은 코드를 구현했다.

## 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstring>
#define LEN 10
#define MAX_ACT 100

using std::cin;
using std::cout;
using std::endl;

int printmenu(void);
void create_account();
void deposit();
void withdraw();
void account_information();
void program_end();

class Information
{
private:
	char* name;
	int ID;
	int total_money;

public:

	Information(const char* customer_name, int customer_ID, int customer_money) : ID(customer_ID), total_money(customer_money)
	{
		name = new char[strlen(customer_name) + 1];
		strcpy(name, customer_name);
	}

	int ID_check()
	{
		return ID;
	}

	char* name_check()
	{
		return name;
	}

	int money_check()
	{
		return total_money;
	}

	int output(int money)
	{
		total_money -= money;

		if (money <= 0)
		{
			money = 0;
			return 0;
		}

		return money;
	}

	void input(int money)
	{
		total_money += money;
	}

	~Information()
	{
		cout << ID << "번 정보가 제거됩니다." << endl;
		delete[] name;
	}
};

Information* account_array[MAX_ACT];
int num = 0;

int main()
{
	while (1)
	{
		int chosen = printmenu();

		switch (chosen)
		{
		case 1:
			create_account();
			break;

		case 2:
			deposit();
			break;

		case 3:
			withdraw();
			break;

		case 4:
			account_information();
			break;

		case 5:
			program_end();
			break;

		default:
			cout << "Illigal selection." << endl;
		}
	}

	return 0;
}

int printmenu(void)
{
	int number;

	const char* menulist[5] = { "1. 계좌개설", "2. 입 금", "3. 출 금","4. 계좌정보 전체 출력","5. 프로그램종료" };

	cout << "-----Menu-----" << endl;
	for (int i = 0; i < 5; i++)
	{
		cout << menulist[i] << endl;
	}

	cout << "선택: ";

	cin >> number;

	return number;
}

void create_account()
{
	int temp_ID;
	char temp_name[LEN];
	int temp_input;

	cout << endl;
	cout << "[계좌개설]" << endl;
	cout << "계좌ID : ";
	cin >> temp_ID;
	cout << "이름 : ";
	cin >> temp_name;
	cout << "입금액: ";
	cin >> temp_input;
	cout << endl;

	account_array[num] = new Information(temp_name, temp_ID, temp_input);
	num += 1;
}

void deposit()
{
	int ID;
	int deposit_money;
	//int flag = 0;

	cout << endl;
	cout << "[입   금]" << endl;
	cout << "계좌 ID: ";
	cin >> ID;

	for (int i = 0; i < num; i++)
	{
		if (account_array[i]->ID_check() == ID)
		{
			cout << "입금액: ";
			cin >> deposit_money;
			account_array[i]->input(deposit_money);
			//flag += 1;
			cout << "입금완료" << endl;
			return;
		}
	}

	cout << "해당 ID는 없는 ID입니다." << endl;
	cout << endl;
}

void withdraw()
{
	int ID;
	int withdrawal_money;
	//int flag = 0;

	cout << endl;
	cout << "[출   금]" << endl;
	cout << "계좌 ID: ";
	cin >> ID;

	for (int i = 0; i < num; i++)
	{
		if (account_array[i]->ID_check() == ID)
		{
			cout << "출금액: ";
			cin >> withdrawal_money;
			if (account_array[i]->output(withdrawal_money) == 0)
			{
				//flag += 1;
				cout << "잔액이 부족합니다." << endl;
				return;
			}
			//flag += 1;
			cout << "출금완료" << endl;
			return;
		}
	}
	
	cout << "해당 ID는 없는 ID입니다." << endl;
	cout << endl;
}

void account_information()
{
	int ID;

	cout << endl;
	cout << "계좌정보 출력" << endl;

	for (int i = 0; i < num; i++)
	{
		cout << "계좌 ID: " << account_array[i]->ID_check() << endl;

		cout << "이 름: " << account_array[i]->name_check() << endl;

		cout << "잔 액: " << account_array[i]->money_check() << endl;

		cout << endl;
	}
}

void program_end()
{
	cout << "프로그램을 종료합니다. 계좌 정보가 제거됩니다." << endl;
	for (int i = 0; i < num; i++)
	{
		account_array[i]->~Information();
	}
	cout << "감사합니다." << endl;

	exit(1);
}

```
쓰고나니 겁나게 길구마잉

저자가 강조하는 클래스의 역할은 '정보 은닉'과 '캡슐화'이다. class 내의 키워드 'private'와 'public'을 적절히 사용하여 클래스 내의 정보가 함부로 노출되거나 변경되지 못하게끔 정보 은닉을 적절히 잘 시켜줬는지, 또 함수의 구성을 내부적으로 효율적으로 작동하게끔 잘 싸 놓았는지에 대해 논할 수 있는 단어이다. (본인은 그렇게 이해했지만, 한편으론 상당히 광범위하고 추상적인 표현처럼 느껴져 맞게 표현했는지 확신할 수 없다.) 

```cpp
class Information
{
private:
	char* name;
	int ID;
	int total_money;

public:

	Information(const char* customer_name, int customer_ID, int customer_money) : ID(customer_ID), total_money(customer_money)
	{
		name = new char[strlen(customer_name) + 1];
		strcpy(name, customer_name);
	}

	int ID_check()
	{
		return ID;
	}

	char* name_check()
	{
		return name;
	}

	int money_check()
	{
		return total_money;
	}

	int output(int money)
	{
		total_money -= money;

		if (money <= 0)
		{
			money = 0;
			return 0;
		}

		return money;
	}

	void input(int money)
	{
		total_money += money;
	}

	~Information()
	{
		cout << ID << "번 정보가 제거됩니다." << endl;
		delete[] name;
	}
};
```


해당 예제에서는 class명을 Information으로 지정하여, 개인의 정보를 저장하고 입출력할수 있는 기능을 하게끔 지정하였다. 그에 따라 변수는 이름과 아이디, 그리고 가지고 있는 총 금액을 저장할 수 있는 total_money를 지정하였으며, 일반적으로 그렇듯이 private으로 숨겨 놓았다. public에서는 생성자와 소멸자, 그리고 각각 아이디와 이름, 돈의 액수를 확인할 수 있게끔 출력하는 함수, 원하는 만큼의 금액을 입력받아 돈을 넣거나 뺄 수 있는 input, output 함수를 만들었다.

특히 해당 클래스에서 회원정보의 이름을 저장하는 변수는 char*로 지정하여 동적 할당을 받아 저장하는 방식이기에 소멸자에서는 해당 공간을 지워주는 delete문이 있다. 

(그렇기 때문에 복사 생성자의 지정도 필수적이겠는데, 이는 다음 단계에서 할 일이다.)

```
Information* account_array[MAX_ACT];
int num = 0;
```
회원의 정보를 연속적으로 저장할 수 있게끔 배열로 지정하고, 자료형은 클래스 포인터이다.<br>회원을 추가할 때마다 num 변수에 +1씩 하여 총 저장된 인원을 파악할 수 있다.

```cpp
void withdraw()
{
...
	for (int i = 0; i < num; i++)
	{
		if (account_array[i]->ID_check() == ID)
		{
			cout << "출금액: ";
			cin >> withdrawal_money;
			if (account_array[i]->output(withdrawal_money) == 0)
			{
				//flag += 1;
				cout << "잔액이 부족합니다." << endl;
				return;
			}
			//flag += 1;
			cout << "출금완료" << endl;
			return;
		}
	}
	return;
}
```
중간중간 flag를 사용했던 흔적이 있다. 원래는 if문이 실행되었다면 flag에 1을 더하고, 그리하여 flag가 0이라면 if문이 사용되지 않았다는 증거로 "해당 아이디는 없는 아이디"라는 문구를 출력하려 했으나, 저렇게 return을 넣어놓으면 if문이 실행될 시 아래 "해당 아이디는 없는 아이디..."라는 문구가 출력되기도 전에 함수가 끝나고 아니면 마지막에 문구가 출력되게 된다. (이는 저자의 아이디어였다. 똑똑한 저자.) 해당 방식이 훨씬 효율적이어 보여 함수를 수정하였다. 

```cpp
void program_end()
{
	cout << "프로그램을 종료합니다. 계좌 정보가 제거됩니다." << endl;
	for (int i = 0; i < num; i++)
	{
		account_array[i]->~Information();
	}
	cout << "감사합니다." << endl;

	exit(1);
}
```
프로그램을 끝내고 싶을 때 실행시키는 함수이다. 배열에 저장된 모든 회원의 동적할당공간이 제거되게끔 소멸자를 실행시킨다.

이렇게 해당 프로젝트의 전 코드와 특징적인 부분들을 작성해 보았다. 작성 직후 책에 저자가 적어놓은 코드와 비교해가며 괜찮은 부분은 책의 코드를 인용하고, 본인의 방식을 그대로 유지한 부분도 있다. 해당 챕터의 내용처럼 클래스의 정보은닉, 캡슐화, 생성자와 소멸자에 대해 실습할 수 있는 좋은 프로젝트였던듯!
