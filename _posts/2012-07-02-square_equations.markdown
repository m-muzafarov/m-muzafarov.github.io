---
layout: post
status: publish
published: true
title: Решение квадратных уравнений
author: ––=Messiλh=––
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://m.muzafarov.vk.com
excerpt: "Листинг\r\n\r\n"
wordpress_id: 158
wordpress_url: http://messiah.ks8.ru/wordpress/?p=158
date: 2012-07-02 11:36:01.000000000 +06:00
categories:
- C++
tags: []
comments: []
---
Листинг

<a id="more"></a><a id="more-158"></a>
<pre class="brush: cpp; gutter: true"> #include <iostream>
 #include <cmath>
 using namespace std;
 int main()
 {
 cout<<"Reshenie kvadratnih uravneniy \n";
 int i;
 double a,b,c;
 &#47;&#47;Пошел цикл
 do
 {
 cout<<"a,b,c->";
 cin>>a>>b>>c;
 if(a==0) cout<<"Ne kvadratnoe ur-e \n";
 &#47;&#47;Если квадратное - пошло решение
 else
 {
 double t=-b&#47;2&#47;a;
 double d(t*t-(c&#47;a));
 &#47;&#47;Проверка дискриминанта
 if(d<0) cout<<"Net deystv korney \n";
 else
 {
 double x1,x2;
 d=sqrt(d);
 x1=t-d;
 x2=t+d;
 &#47;&#47;Выдача корней
 cout<<"x1= "<<x1<<", x2= "<<x2<<endl;
 }
}
 &#47;&#47;Продолжить цикл?
 cout<<"\n Continue? )1&#47;0 -> ";
 cin>>i;
 } while (i==1);
return 0;
 }<&#47;pre>
