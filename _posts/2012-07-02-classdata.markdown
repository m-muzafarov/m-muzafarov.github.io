---
layout: post
status: publish
published: true
title: Класс Дата
author: M_Messiah
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://brainstorage.me/M_Messiah
excerpt: "Class Data C++"
date: 2012-07-02 11:52:57.000000000 +06:00
categories: cpp
tags: []
comments: []
---

{% highlight cpp %}
#include <iostream>
using namespace std;
class Date {
	int day, month;
	public:
		Date() {day=1; month=1;}
		Date(int m) {day=1; month = m;}
		~Date(){}
		void GetDate(int d, int m) {d=day; m=month;}
		void SetDate(int d, int m) {day=d; month=m;}
		void InputDate() {int d,m; cout<<"\n Input Day/Month \n"; cin>>d>>m; day=d; month=m;}
		void OutputDate() {
			cout<<" \n"<<day;
			switch (month) {
				case 1: cout<<" January"; break;
				case 2: cout<<" February"; break;
				case 3: cout<<" Marth"; break;
				case 4: cout<<" April"; break;
				case 5: cout<<" May"; break;
				case 6: cout<<" June"; break;
				case 7: cout<<" July"; break;
				case 8: cout<<" August"; break;
				case 9: cout<<" September"; break;
				case 10: cout<<" October"; break;
				case 11: cout<<" November"; break;
				case 12: cout<<" December"; break;
			}
		}
		friend bool CompareDate(Date &X, Date &Y) { return (X.day==Y.day) && (X.month==Y.month);}
		int operator ==(Date Y) { return CompareDate(*this,Y);}
		Date& operator +(int x) {this->day=(this->day+x)%30; return *this;}
		};
		
int main() {
	Date dt,dr;
	Date A[3];
	dr.SetDate(27,7);
	for (int i=1; i<3; i++) {A[i]= A[i]+ i*10; A[i].OutputDate();}
	A[0].SetDate(31,03);
	A[0].OutputDate();
	dt.InputDate();
	if (dt==dr) cout<<"\n Happy Birthday! \n"; else cout<<"\n Please wait \n";
	return 0;
}{% endhighlight %}
