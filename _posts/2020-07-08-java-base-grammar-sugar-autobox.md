---
layout: post
title: 知识图谱 - Java基础 - 语法糖 - 自动拆装箱
categories: [知识图谱,Java基础]
description: 语法糖 - 自动拆装箱
keywords: 语法糖, 自动拆装箱
---



# 语法糖, 自动拆装箱



## 基本类型与包装类型

| 8大基本类型 | 包装类型  | 缓存值     | 缓存重定义                          |
| ----------- | --------- | ---------- | ----------------------------------- |
| byte        | Byte      | [-128,127] | 无                                  |
| char        | Character | [0,127]    | 无                                  |
| short       | Short     | [-128,127] | 无                                  |
| int         | Integer   | [-128,127] | java.lang.Integer.IntegerCache.high |
| long        | Long      | [-128,127] | 无                                  |
| float       | Float     | 无         | 无                                  |
| double      | Double    | 无         | 无                                  |
| boolean     | Boolean   | 无         | 无                                  |

## 什么是自动拆装箱

> 概念解析

Java 系统内由 基本类型转换为包装类型，称为装箱，相反由包装类转化为基本类型的称为拆箱，因为这些动作不需要开发人员参与，所以叫做自动拆装箱。

> 使用场景

```java
// 源码
public static void main(String[] args) {
    Integer i = 10;
    int j = i;
}
// 反编译代码 jad -sjava Chapter01_2_3.class
public static void main(String args[]){
    Integer i = Integer.valueOf(10);
    int j = i.intValue();
}
```

`Integer i = 10;`

当把基本类型值直接赋值给包装类型时，这个过程就会产生自动装箱

`int j = i;`

当把包装类型i直接赋值给基本类型时，会产生自动拆箱的动作。

其他基本类型与包装类型的自动拆装箱与此类似。



> 基本类型可以通过包装类型互转

![image-20200701115802363](http://img.zhangqingdong.cn/image-20200701115802363.png)

Byte/Short/Integer/Long/Float/Double 都继承自`Number` ,所以都可以转换成这六类对象的基本类型。

![image-20200701143132503](http://img.zhangqingdong.cn/image-20200701143132503.png)

## 有什么用

1. 可以直接给包装类型赋值，不需要显示转换
2. 包装类与基本类型可以混用，无需进行强制转换



## 踩坑指南

> 包装类缓存

```java
private static void testEqual() {
    Integer a = 127;
    Integer b = 127;
    System.out.println("a == b => " + (a == b));

    Integer x = 128;
    Integer y = 128;
    System.out.println("x == y => " + (x == y));
}

// ======== output =========
// a == b => true
// x == y => false
```

如果不知道自动装箱机制，这里就会感觉莫名其妙，明明一样的处理，为什么，一个127 是true，128就是false。

但经过上面的装箱机制，我们知道



 Integer x = 128;  `->` Integer x = Integer.valueOf(128);



现在看下`Integer.valueOf() `方法源码：

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

这里经过了一个比较， 如果`i` 的值在`IntegerCache.low`和`IntegerCache.high`的区间内，

就拿`IntegerCache.cache `中的缓存来赋值。

这个缓存的值在**基本类型与包装类型** 表中已经展示了出来，区间为[-128,127] ，且这个值可以配置。

配置项为`java.lang.Integer.IntegerCache.high`，仅Integer类型可配置



> 空指针

```java
private static void testNullPoint(){
    Integer a = 10;
    // ==== 经过各种业务操作 start ====
    a = null;
    // ==== 经过各种业务操作 end ====
    int b = a;
}

// com.deepj.architecture.chapter01.Chapter01_2_3
// Exception in thread "main" java.lang.NullPointerException
// 		at com.deepj.architecture.chapter01.Chapter01_2_3.testNullPoint(Chapter01_2_3.java:48)
// 		at com.deepj.architecture.chapter01.Chapter01_2_3.main(Chapter01_2_3.java:15)
```

同理，在`int b = a;` 该行抛出一个空指针，如果不知道自动拆箱机制，也会导致开发人员一脸生无可恋。

此时知道有拆箱机制，看下具体原因 `int b = a.intValue();`此时`a`为null， 也就显而易见报了空指针



> 基本类型与包装类型的混合操作

```java
// 源码
private static void testEquals() {
    int a = 128;
    Integer b = 128;
    System.out.println("1>>> " + (a == b));
    System.out.println("2>>> " + b.equals(a));

    System.out.println("3>>> " + b.equals(127 + 1));
    System.out.println("4>>> " + b.equals(127L + 1));
}
// 反编译 jad -sjava Chapter01_2_3.class
private static void testEquals(){
    int a = 128;
    Integer b = Integer.valueOf(128);
    System.out.println((new StringBuilder()).append("1>>> ")
                       .append(a == b.intValue()).toString());
    System.out.println((new StringBuilder()).append("2>>> ")
                       .append(b.equals(Integer.valueOf(a))).toString());
    System.out.println((new StringBuilder()).append("3>>> ")
                       .append(b.equals(Integer.valueOf(128))).toString());
    System.out.println((new StringBuilder()).append("4>>> ")
                       .append(b.equals(Long.valueOf(128L))).toString());
}

// =========== output =================
// 1>>> true
// 2>>> true
// 3>>> true
// 4>>> false
```

+ **当一个基础数据类型与封装类进行==、+、-、*、/运算时，会将封装类进行拆箱，对基础数据类型进行运算。**
+ **当基础数据类型与包装类型进行 equals 比较时，所传入的参数类型是原始数据类型时，会自动对其装箱，** 且相关equals方法被重写为 `obj instanceof Integer && value == ((Integer)obj).intValue()`



> 无意识装箱性能消耗

```java
private static void testPerformance() {
    long time1 = System.currentTimeMillis();
    long sum = 0L;
    for (int i = 0; i < Integer.MAX_VALUE / 2; i++) {
        sum += i;
    }
    System.out.println(sum);
    System.out.println(System.currentTimeMillis() - time1);

    System.out.println("==========================================");

    long time2 = System.currentTimeMillis();
    Long sum1 = 0L;
    for (int i = 0; i < Integer.MAX_VALUE / 2; i++) {
        sum1 += i;
    }
    System.out.println(sum);
    System.out.println(System.currentTimeMillis() - time2);
}

// =================== output =======================

// 576460750692810753
// 288
// ==========================================
// 576460750692810753
// 3065

```

仅仅是定义 `long sum = 0L;` 与 ` Long sum1 = 0L;` 的区别，就导致最终的一个性能消耗差了 10倍多

究其原因在于 最终给sum1赋值时，因为是包装类型，需要使用 Long.valueOf() 进行赋值，此时需要创建多个Long对象，

浪费内存也消耗了时间。

![image-20200701212529258](http://img.zhangqingdong.cn/image-20200701212529258.png)
