---
layout: post
status: publish
published: true
title: Совершенные числа
author: m-messiah
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://brainstorage.me/M_Messiah
excerpt: "Совершенные числа на C++"
date: 2012-07-02 11:51:33.000000000 +06:00
categories: cpp
tags: []
comments: true
---

```cpp
#include <iostream>
#include <cmath>
#include <conio.h>
using namespace std;
int s;
int multnum (int n) {
	int m,x(1);
	for (m=n; m>0; m=m/10) { x*= m%10;}
	return x;
}
bool prime(int n) {
	int m,k; m=sqrt((double)n)+.5;
	k=2;
	while (k<=m)
		if (n%k==0) return false;
		else k++;
	return true;
}
int main() {
	cout<<" Finding minimal number on \n which numerals multiplication \n is equal to typed number S \n ~~~~~~~~~~~~~~~~~~~~~~~~~~~~ \n";
	cout<<"Please, type a number S \n";
	cin>>s;
	int b;
	cout<<"Type a border of traverse \n";
	cin>>b;
	if(prime(s) && s>10) goto lab;
	int n;
	for (n=1; n<=b && multnum(n)!=s; n++);
	if (n<=b) cout<<"The Number to be found is "<<n<<endl;
	else
		lab: cout<<"I Can`t find a number in this [1,"<<b<<"] range... Error. \n";
	_getch();
}
```

