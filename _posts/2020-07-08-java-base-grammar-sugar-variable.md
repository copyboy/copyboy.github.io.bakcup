---
layout: post
title: 知识图谱 - Java基础 - 语法糖 - 方法变长参数
categories: [知识图谱,Java基础]
description: 语法糖 - 方法变长参数
keywords: 语法糖, 方法变长参数
---



# 语法糖, 方法变长参数



## 方法变长参数

> 源码

```java
// 源码
public class Chapter01_2_4 {

    public static void main(String[] args) {
        sendMsg("deepj");
        sendMsg("deepj","hello","world");
    }
    private static void sendMsg(String msg) {
        System.out.println("sendMsg >>> " + msg);
    }
    private static void sendMsg(String... args) {

        for (String arg : args) {
            System.out.println(arg);
        }
    }
}

// sendMsg >>> deepj
// deepj
// hello
// world
```

> 反编译源码

```java
// 反编译后 jad -sjava Chapter01_2_4.class
public class Chapter01_2_4 {

    public Chapter01_2_4(){}

    public static void main(String args[]) {
        sendMsg("deepj");
        sendMsg(new String[] { "deepj", "hello", "world" });
    }
    private static void sendMsg(String msg) {
        System.out.println((new StringBuilder())
                           .append(" sendMsg >>>")
                           .append(msg)
                           .toString());
    }
    private static transient void sendMsg(String args[]) {
        String as[] = args;
        int i = as.length;
        for(int j = 0; j < i; j++) {
            String arg = as[j];
            System.out.println(arg);
        }
    }
}
```

> 总结

由编译后的代码可知，变长参数，就是在编译时，将它转换为参数数组进行传递。



## 注意点

>  1) 变长参数的位置

![image-20200701215103173](http://img.zhangqingdong.cn/image-20200701215103173.png)

只能存放在最后一个位置，且只能出现一个可变长参数



>  2) 匹配问题

如果重载了可变参数的方法, 优先调用匹配定长参数的方法,不定参数的那个重载方法是最后被选中的。

如上例 sendMsg("deepj"); 它既能匹配定长参数，也能匹配不定长参数，此时虚拟机会优先让它匹配定长参数。



> 3) transient 关键字

 `TODO`待更新，序列化章节进行深入探讨。



*参考链接*

[参数数量可变的方法 - Java](https://blog.csdn.net/qq_36852780/article/details/99772461)