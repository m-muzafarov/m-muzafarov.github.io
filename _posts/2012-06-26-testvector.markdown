---
layout: post
status: publish
published: true
title: TestVector
author: m-messiah
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://brainstorage.me/M_Messiah
date: 2012-06-26 21:47:33.000000000 +06:00
categories: Java
tags: []
comments: true
---

```java
import junit.framework.TestCase;
public class TestVector extends TestCase {
public void testScalar() {
Vector3D A = new Vector3D( 1, 2, 3 );
Vector3D B = new Vector3D(1, 15, 14 );
assertTrue(A.scalar(B) == 73);
}
public void testDimension() {
Vector3D A = new Vector3D( 1, 2, 3);
assertTrue(A.dimension() == 3);
}
}
```

&nbsp;

Исходники доступны на [GitHub](https://github.com/m-muzafarov/java_course/blob/master/Vectors/TestVector.java)
