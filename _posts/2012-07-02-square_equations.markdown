---
layout: post
status: publish
published: true
title: Решение квадратных уравнений
author: M_Messiah
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: https://github.com/m-messiah
excerpt: "Решение квадратных уравнений на C++"
date: 2012-07-02 11:36:01.000000000 +06:00
categories: cpp
tags: []
comments: true
---

```cpp
#include <iostream>
#include <cmath>
using namespace std;
int main() {
	cout<<"Reshenie kvadratnih uravneniy \n";
	int i;
	double a,b,c;
	// Пошел цикл
	do {
		cout<<"a,b,c->";
		cin>>a>>b>>c;
		if(a==0) cout<<"Ne kvadratnoe ur-e \n";
		// Если квадратное - пошло решение
		else {
			double t=-b/2/a;
			double d(t*t-(c/a));
			// Проверка дискриминанта
			if(d<0) cout<<"Net deystv korney \n";
			else {
				double x1,x2;
				d=sqrt(d);
				x1=t-d;
				x2=t+d;
				// Выдача корней
				cout<<"x1= "<<x1<<", x2= "<<x2<<endl;
			}
		}
		// Продолжить цикл?
		cout<<"\n Continue? )1/0 -> ";
		cin>>i;
	} while (i==1);
	return 0;
}
```

