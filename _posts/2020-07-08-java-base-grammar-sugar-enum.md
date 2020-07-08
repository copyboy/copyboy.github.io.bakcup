---
layout: post
title: 知识图谱 - Java基础 - 语法糖 - 枚举
categories: [知识图谱,Java基础]
description: 语法糖 - 枚举
keywords: 语法糖, 枚举
---



# 语法糖, 枚举



## 简单使用

> 源码

```java
enum ApiStatus {
    SUCCESS,
    ERROR,
    ;
}
```

> 反编译源码 jad -sjava Chapter01_2_5.class

```java
static final class ApiStatus extends Enum {

    public static ApiStatus[] values() {
        return (ApiStatus[])$VALUES.clone();
    }

    public static ApiStatus valueOf(String name) {
        return (ApiStatus)Enum.valueOf(com/deepj/architecture/chapter01/Chapter01_2_5$ApiStatus, name);
    }

    public static final ApiStatus SUCCESS;
    public static final ApiStatus ERROR;
    private static final ApiStatus $VALUES[];

    static {
        SUCCESS = new ApiStatus("SUCCESS", 0);
        ERROR = new ApiStatus("ERROR", 1);
        $VALUES = (new ApiStatus[] {
            SUCCESS, ERROR
        });
    }

    private ApiStatus(String s, int i) {
        super(s, i);
    }
}

```

> 总结

- 类默认继承了 `Enum` ，也就是说枚举类不能再继承类，但是可以实现接口
- 内部拥有一个静态的两个参数的构造方法，`String s, int i`
- 源码中定义的属性，添加了`public static final ` 修饰符进行修饰
- 定义了一个内部私有`$VALUES[]`, 用于提供一个静态公有方法`values()`获取所有枚举值的数组
- 静态代码块用于创建静态成员对象，以及给私有的`$VALUES[]`赋值
- 埋个坑 `Enum.valueOf` 的具体作用 TODO



## 常用方式

> 源码

```java
enum ApiResult {
    SUCCESS("0000", "成功"),
    ERROR("0001", "失败"),
    ERROR_404("0002", "请求不存在"),
    ;
    private String code;
    private String msg;

    ApiResult(String code, String msg) {
        this.code = code;
        this.msg = msg;
    }
    public String getCode() {
        return code;
    }
    public String getMsg() {
        return msg;
    }
}
```

> 总结

就是给这个枚举类添加属性，并且在构造的时候直接加上该属性`SUCCESS("0000", "成功")`

其他需要注意的是反编译的构造方法是4个参数，再就是跟简单用法保持一致

```java
private ApiResult(String s, int i, String code, String msg) {
    super(s, i);
    this.code = code;
    this.msg = msg;
}
```



## 不常见用法 - 定义抽象类

> 源码

```java
enum Operator {

    ADD("+") {
        @Override
        public int eval(int a, int b) {
            return a + b;
        }
    },
    ;
    private String code;

    Operator(String code) {
        this.code = code;
    }

    public String getCode() {
        return code;
    }
	// 定义了一个内部的抽象方法，在ADD属性中进行重写
    public abstract int eval(int x, int y);
}
// 使用方式
// int result = Operator.ADD.eval(3, 5);
// 我觉得这个可以用来替代C++里面自定义操作符的功能，内部就是通过匿名内部类实现的

```

> 反编译源码

```java
static abstract class Operator extends Enum {

    public static Operator[] values() {
        return (Operator[])$VALUES.clone();
    }

    public static Operator valueOf(String name) {
        return (Operator)Enum.valueOf(com/deepj/architecture/chapter01/Chapter01_2_5$Operator, name);
    }

    public String getCode() {
        return code;
    }

    public abstract int eval(int i, int j);

    public static final Operator ADD;
    private String code;
    private static final Operator $VALUES[];

    static  {
        ADD = new Operator("ADD", 0, "+") {

            public int eval(int a, int b) {
                return a + b;
            }

        }
        ;
        $VALUES = (new Operator[] {
            ADD
        });
    }

    private Operator(String s, int i, String code) {
        super(s, i);
        this.code = code;
    }

}
```





## 不常见用法 - 继承接口

> 源码

```java
enum AssertEnum implements Predicate<ApiStatus> {

    fetchResult {
        @Override
        public boolean test(ApiStatus apiStatus) {
            return apiStatus == ApiStatus.SUCCESS;
        }
    },
    ;
}

// 使用方式
// boolean test = AssertEnum.fetchResult.test(ApiStatus.SUCCESS);

// 源码中有处比较不一样的地方, 就是在还原匿名内部类的时候，它有两个test方法，一个是原生的test(Object obj)，
// 另一个就是我们定义的test(ApiStatus apiStatus)， 这是因为泛型擦除导致的一个方法重载
```

> 反编译源码部分

```java
fetchResult = new AssertEnum("fetchResult", 0) {

    public boolean test(ApiStatus apiStatus) {
        return apiStatus == ApiStatus.SUCCESS;
    }

    public volatile boolean test(Object obj) {
        return test((ApiStatus)obj);
    }
}
```

