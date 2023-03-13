---
title: "JAVA LV 1"
subtitle: "연습문제"
layout: post
author: "Gihoon"
date: 2023-03-13 02:00:00
header-style: text
hidden: true
tags:
  - java
---
### 모든 자료와 출처는 자바의 정석(남궁성) 이다. 풀이는 내가 아는 선에서 먼저 해볼 것이며, 그 후에 틀린부분에 있어서는 오답노트를 작성할 것이다.

![pratice1](https://github.com/gihoonbackend/gihoonbackend.github.io/blob/master/images/pratice1.png?raw=true)
![pratice2](https://github.com/gihoonbackend/gihoonbackend.github.io/blob/master/images/pratice2.png?raw=true)
![pratice3](https://github.com/gihoonbackend/gihoonbackend.github.io/blob/master/images/pratice3.png?raw=true)

### 다음 표의 빈 칸에 개의 기본형 을 알맞은 자리에 넣으시오 8 (primitive type) .

우선 논리형에는 boolean이 있으며 1 byte이다  
문자형은 char > 2 byte, 정수형은 byte,short,int,long 의 순서로 1,2,4,8 byte이다.  
실수형은 float, double의 순서로 4, 8 byte이다.

### 주민등록번호를 숫자로 저장하고자 한다 이 값을 저장하기 위해서는 어떤 자료형 . (data type) 을 선택해야 할까? regNo 라는 이름의 변수를 선언하고 자신의 주민등록번호로 초기화 하는 한 줄의 코드를 적으시오.

```
long regNo = 0000001111111L ;
```

정수형 타입으로는 int 형을 주로 사용하지만 주민등록번호는 13자리의 정수이므로 int형의 범위를 넘어선다. 그래서 long형을 사용해야하며 접미사 'L'을 잊으면 안된다.

### 다음의 문장에서 리터럴 변수 상수 키워드를 적으시오.

```
int i = 100;
long l =100L;
final float PI = 3.14f;
```

리터럴 : 100 , 100L, 3.14f  
변수 : i, l,  
키워드 : int, long, final, float  
상수 : PI

### 다음 중 기본형 이 아닌 것은 (primitive type) ?

a. int  
b. Byte  
c. double  
d. boolean  
  
  
b. Byte는 참조형으로 기본형이 아니다.  
기본형은 8개로 boolean, byte, short, long, int, double, float, char 이며, 그 외의 탑인은 모두 참조형이다.

### 다음 문장들의 출력결과를 적으세요 오류가 있는 문장의 경우 괄호 안에 오류 라고 적으시오.

```
System.out.println(“1” + “2”) ( ) → 12
System.out.println(true + “”) ( ) → true
System.out.println(‘A' + 'B') ( ) → 'A', 'B'의 문자코드는 각각 65와 66이므로 131이다.
System.out.println('1' + 2) ( ) → '1'의 문자코드는 49이므로 51이다.
System.out.println('1' + '2') ( ) → '2'의 문자코드는 50이므로 99이다.
System.out.println('J' + “ava”) ( ) → 문자열과 덧셈연삼이므로 'J' 문자는 문자열이 된다. 따라서 Java이다.
System.out.println(true + null) ( ) → 오류
```

### 다음 중 키드가 아닌 것은 모두 고르시오 ( )

a. if  
b. True  
c. NULL  
d. Class  
e. System  
  
  
d,c,d,e 이다. true는 자바에서 사용하는 키워드이지만 True는 키워드가 아니다.  
다음 예시는 자바에서 사용되는 키워드이다.  
\[abstract default if package this  
assert do goto private throw  
boolean double implements protected throws  
break else import public transient  
byte enum instanceof return true  
case extends int short try  
catch false interface static void  
char final long strictfp volatile  
class finally native super while  
const float new switch  
continue for null synchronized\]

### 다음 중 변수의 이름으로 사용할 수 있는 것은 모두 고르시오

a. $ystem  
b. channel#5  
c. 7eleven  
d. If  
e. 자바  
f. new  
g. $MAX\_NUM  
h. hello@com  
  
  
a,d,e,g 이다. 변수의 이름은 다음과 같은 규칙이 있다.

1.  대소문자가 구분되며 길이에 제한이 없다

-   True true . 와 는 서로 다른 것으로 간주된다

2.  예약어를 사용해서는 안 된다

-   true 는 예약어라서 사용할 수 없지만 True는 가능하다

3.  숫자로 시작해서는 안 된다

-   top10 , 7up . 은 허용하지만 는 허용되지 않는다

4.  '\_' '$' . 특수문자는 와 만을 허용한다

-   $harp , S#arp .

### 참조형 변수 와 같은 크기의 기본형을 모두 고르시오.

a. int  
b. long  
c. short  
d. float  
e. double  
  
  
a, d이다. 모든 참조형 변수는 4 byte이므로, 크기가 4 byte인 기본형 타입은 a, d이다.

### 다음 중 형변환을 생략할 수 있는 것은 모두 고르시오.

byte b = 10;  
char ch = 'A';  
int i = 100;  
long l = 1000L;  
a. b = (byte)i;  
b. ch = (char)b;  
c. short s = (short)ch;  
d. float f = (float)l;  
e. i = (int)ch;  
  
  
a. b = (byte)i; // int(4byte) byte(1byte) → 이므로 반드시 형변환 필요  
b. ch = (char)b; // byte(1byte) char(2byte) → 이지만 범위가 달라서 형변환 필요  
c. short s = (short)ch; // char,short 2byte 은 이지만 범위가 달라서 형변환 필요  
d. float f = (float)l; // float(4byte) long(8byte) 의 범위가 보다 커서 생략가능  
e. i = (int)ch; // char(2 byte) int(4byte) → 이므로 생략가능

### char 타입의 변수에 저장될 수 있는 정수 값의 범위는 (10진수로 적으시오)

char은 2의 16제곱개의 값을 표현할 수 있다. (2bit = 2\*8bit)  
2의 16제곱은 64436개이며 0을 포함해야하므로 0~65535개이며 char 범위이다.

### 다음중 변수를 잘못 초기화 한 것은 모두 고르시오 ? ( )

a. byte b = 256;  
b. char c = '';  
c. char answer = 'no';  
d. float f = 3.14  
e. double d = 1.4e3f;  
  
  
a. byte b = 256; // byte (-128~127) . 의 범위 를 넘는 값으로 초기화 할 수 없음  
b. char c = ''; // char는 반드시 한 개의 문자를 지정해야함  
c. char answer = 'no'; // char . 에 두 개의 문자를 저장할 수 없음  
d. float f = 3.14 // 3.14 3.14d . f 는 의 생략된 형태 접미사 를 붙이거나 형변환필요  
e. double d = 1.4e3f; // double(8byte) float (4byte) OK

### 다음 중 메서드의 선언부로 알맞은 것은 모두 고르시오 main ? ( )

a. public static void main(String\[\] args)  
b. public static void main(String args\[\])  
c. public static void main(String\[\] arv)  
d. public void static main(String\[\] args)  
e. static public void main(String\[\] args)  
  
  
a,b,c,e 이다.  
a. public static void main(String\[\] args)  
b. public static void main(String args\[\])  
c. public static void main(String\[\] arv) // args 매개변수 의 이름은 달라도 됨  
d. public void static main(String\[\] args) // void main . 는 반드시 앞에 와야 한다  
e. static public void main(String\[\] args) // public static 과 은 위치가 바뀌어도 됨

### 다음 중 타입과 기본값이 잘못 연결된 것은 모두 고르시오 ? ( )

a. boolean - false  
b. char - '\\u0000'  
c. float - 0.0  
d. int - 0  
e. long - 0  
f. String - "  
  
  
c, e, f이다.  
c. float - 0.0 // float 0.0f . 0.0 0.0d d 는 가 기본값 은 에서 접미사 가 생략된 것  
e. long - 0 // long 0L  
f. String - "" // String . null.