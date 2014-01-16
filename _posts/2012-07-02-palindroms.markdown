---
layout: post
status: publish
published: true
title: Числа-палиндромы
author: M_Messiah
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://brainstorage.me/M_Messiah
excerpt: "<h1>Задача №14</h1>
	По заданному числу М строится последовательность, в которой первый элемент равен М, а каждый следующий - полусумма элемента и его перевертыша.<br>
	По каждой из некоторого количества пар чисел М, К указать встретился ли палиндром среди не более чем К первых элементов последовательности, порождаемой числом М."
date: 2012-07-02 11:49:45.000000000 +06:00
categories: cpp
tags: []
comments: []
---
{% highlight cpp %} // ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 // ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 // ~~ Задача №14 ~~
 // ~~ По заданному числу М строится последовательность, ~~
 // ~~ в которой первый элемент равен М, а каждый ~~
 // ~~ следующий - полусумма элемента и его перевертыша. ~~
 // ~~ По каждой из некоторого количества пар чисел М, К ~~
 // ~~ указать встретился ли палиндром среди не более чем~~
 // ~~ К первых элементов последовательности, ~~
 // ~~ порождаемой числом М. ~~
 // ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 // ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#include <iostream>
#include <cmath>
using namespace std;
int m,k;
 //Нахождение перевертыша.
int revers (int n)
{
int a,b;
a=n;
for(b=0; a>0; a/=10)
{
b=a%10 + b*10;
}
return b;
}
 //Нахождение следующего элемента последовательности.
int next (int n)
{return (n+revers(n))/2; }
 //Проверка на палиндром.
bool palindrom (int m, int k)
{ int a,b;
if (m==revers(m)) goto lab;
b=m; a=0;
do
{
b=next(b);
if (b==revers(b)) goto lab;
 ++a;
} while (a<=k);
return false;
lab: cout<<b<<endl;
return true;
}
int main()
{
cout<<"Vvedite n \n";
cin>>m;
cout<<revers(m)<<endl;
cout<<"Kol-vo? ";
cin>>k;
if (palindrom(m,k)==true) cout<<"Palindrom byl \n"; else cout<<"Palindroma ne bylo \n";  }{% endhighlight %}
