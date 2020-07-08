---
layout: post
title: 知识图谱 - Java基础 - 语法糖 - switch
categories: 知识图谱
description: 语法糖 - switch
keywords: 语法糖, switch

---



# 语法糖, switch



## 规则

1. switch 语句中的变量类型可以是： byte、short、int 或者 char。从 Java SE 7 开始，switch 支持字符串 String 类型了，同时 case 标签必须为字符串常量或字面量。

    ```bash
    纠正：变量类型可以时char,byte,short,int 以及对应的包装类型Character/Byte/Short/Integer，还有String类型以及枚举类型
    ```

![image-20200623220750179](http://img.zhangqingdong.cn/image-20200623220750179.png)

2. switch 语句可以拥有多个 case 语句。每个 case 后面跟一个要比较的值和冒号。

3. case 语句中的值的数据类型必须与变量的数据类型相同，而且只能是常量或者字面常量。

    ```bash
    纠正：当switch语句中的值为数值类型时，case值可以为范围比它小的数值类型，例如，如果是switch(int) ,case的值可以为任意的char,byte,short类型的值。
    ```

4. 当变量的值与 case 语句的值相等时，那么 case 语句之后的语句开始执行，直到 break 语句出现才会跳出 switch 语句。

5. 当遇到 break 语句时，switch 语句终止。程序跳转到 switch 语句后面的语句执行。case 语句不必须要包含 break 语句。如果没有 break 语句出现，程序会继续执行下一条 case 语句，直到出现 break 语句。

6. switch 语句可以包含一个 default 分支，该分支一般是 switch 语句的最后一个分支（可以在任何位置，但建议在最后一个）。default 在没有 case 语句的值和变量值相等的时候执行。default 分支不需要 break 语句。

> 总结



## Switch-case 入参类型



### Ⅰ）数值类型



> 源码

```java
// 1）数值类型
public static String switchIntCase(int x) {
    String caseWhen;
    switch (x) {
        case 'A':
            caseWhen = "char case in int switch.";
            break;
        case (byte) 127:
            caseWhen = "byte case in int switch.";
            break;
        case (short) 32767:
            caseWhen = "short case in int switch.";
            break;
        case 0x7fffffff:
            caseWhen = "int case in int switch.";
            break;
        default:
            caseWhen = "deepj's. int case default.";
    }
    return caseWhen;
}
```

> 反编译后源码

```java
public static String switchIntCase(int x)
{
    String caseWhen;
    switch(x)
    {
        case 65: // 'A'
            caseWhen = "char case in int switch.";
            break;

        case 127: // '\177'
            caseWhen = "byte case in int switch.";
            break;

        case 32767: 
            caseWhen = "short case in int switch.";
            break;

        case 2147483647: 
            caseWhen = "int case in int switch.";
            break;

        default:
            caseWhen = "deepj's. int case default.";
            break;
    }
    return caseWhen;
}
```

> 总结

数值类型能够看出最底层都转化为int类型的数值，进行比对，这样也就可以解释switch(int) case中的值可以是byte,short



### Ⅱ）字符串类型

> 源码

```java
// 2）字符串类型
public static String switchStrCase(String name) {

    String personDeclaration;
    switch (name) {
        case "job":
            personDeclaration = "I'm job, I'm a student.";
            break;
        case "mic":
            personDeclaration = "I'm mic, I work for APPLE.";
            break;
        case "rues":
            personDeclaration = "I'm rues, I was a football player.";
            break;
        default:
            personDeclaration = "Hi, welcome to deepj's tutorial blog. ";
    }
    return personDeclaration;
}
```



> 反编译后源码

```java
public static String switchStrCase(String name)
{
    String s = name;
    byte byte0 = -1;
    switch(s.hashCode()) {
        case 105405: 
            if(s.equals("job"))
                byte0 = 0;
            break;

        case 108103: 
            if(s.equals("mic"))
                byte0 = 1;
            break;

        case 3511857: 
            if(s.equals("rues"))
                byte0 = 2;
            break;
    }
    String personDeclaration;
    switch(byte0) {
        case 0: // '\0'
            personDeclaration = "I'm job, I'm a student.";
            break;

        case 1: // '\001'
            personDeclaration = "I'm mic, I work for APPLE.";
            break;

        case 2: // '\002'
            personDeclaration = "I'm rues, I was a football player.";
            break;

        default:
            personDeclaration = "Hi, welcome to deepj's tutorial blog. ";
            break;
    }
    return personDeclaration;
}

```



> 总结

字符串类型可以看出是进行了两次switch比较，

第一层先使用string类型的hashcode作为switch(int) 值，case使用hashcode值，如果比较相等，再使用string类型的equals确定值相等

第二层使用第一层比较的结果来作为switch的入参。



### Ⅲ）枚举类型

> 源码

```java
// 3）枚举类型
public static String switchEnumCase(LangEnum lang) {

    String lanLabel;
    switch (lang) {
        case JAVA:
            lanLabel = "Java is simple.";
            break;
        case PHP:
            lanLabel = "PHP is the best.";
            break;
        case PYTHON:
            lanLabel = "Life is short you need python.";
            break;
        default:
            lanLabel = "Talk is cheap, show me the code.";
    }
    return lanLabel;
}

// 枚举类
enum LangEnum {
    JAVA,
    PYTHON,
    CPP,
    GO,
    PERL,
    PHP;
}
```



> 反编译后源码

```java
public static String switchEnumCase(LangEnum lang) {
    static class _cls1 {

        static final int $SwitchMap$com$deepj$architecture$common$LangEnum[];

        static {
            $SwitchMap$com$deepj$architecture$common$LangEnum = new int[LangEnum.values().length];
            try {
                $SwitchMap$com$deepj$architecture$common$LangEnum[LangEnum.JAVA.ordinal()] = 1;
            }
            catch(NoSuchFieldError nosuchfielderror) { }
            try {
                $SwitchMap$com$deepj$architecture$common$LangEnum[LangEnum.PHP.ordinal()] = 2;
            }
            catch(NoSuchFieldError nosuchfielderror1) { }
            try {
                $SwitchMap$com$deepj$architecture$common$LangEnum[LangEnum.PYTHON.ordinal()] = 3;
            }
            catch(NoSuchFieldError nosuchfielderror2) { }
        }
    }

    String lanLabel;
    switch(_cls1..SwitchMap.com.deepj.architecture.common.LangEnum[lang.ordinal()]) {
        case 1: // '\001'
            lanLabel = "Java is simple.";
            break;

        case 2: // '\002'
            lanLabel = "PHP is the best.";
            break;

        case 3: // '\003'
            lanLabel = "Life is short you need python.";
            break;

        default:
            lanLabel = "Talk is cheap, show me the code.";
            break;
    }
    return lanLabel;
}
```



> 总结

枚举类型比较复杂，先是使用了一个静态内部类，在内部维护了数组对象，，该数组对象是枚举值的序数映射。

可以把$SwitchMap$com$deepj$architecture$common$LangEnum 变量名称 替换为 enumArr ，就会简明许多。



> 尝试不当人 - switch case 枚举类，会不会出现问题

```java
尝试破坏该数组对象：使其返回错误对象，比如我输入Java，它却告诉我PHP对应的输出，
主要思路：只要破坏了这个数组对象的值，就会可能产生问题

1） LangEnum 是两个文件，编译和调用的是不一样的类对象， 
思路错误：两个文件，在调用时就会提示需要使用编译的那个类对象

2） LangEnum 是一个类，先编译一次 LangEnum.java 和 Chapter0121.java ，Chapter0121中的main方法会使用LangEnum.JAVA.origin值，之后改动LangEnum.java 中JAVA的顺序，再单独编译LangEnum.java 
思路错误：静态内部类每次在重启时后，在调用时，会重新启动，所以每次重新进行main方法的运行，都相当于重新加载了LangEnum文件
System.out.println(LangEnum.JAVA.ordinal());
System.out.println(switchEnumCase(LangEnum.JAVA));
System.out.println(Arrays.toString(LangEnum.values()));

3） 在2）的基础上，改造一下，仅调用main一次，然后静态内部类也就初始化一次，然后我再修改LangEnum后，把该文件进行重新编译，
    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);
        while (sc.hasNext()) {
            String s = sc.nextLine();
            System.out.println(LangEnum.JAVA.ordinal());
            System.out.println(switchEnumCase(LangEnum.JAVA));
            System.out.println(Arrays.toString(LangEnum.values()));
        }
    }
出现问题：1:14	Hot Swap failed.
		Chapter0121: schema change not implemented
		Chapter0121: Operation not supported by VM
		每次在不重启重新编译LangEnum的时候，告知不能单独编译这样一个文件，
		查找原因说是jvm hotswap的限制，解释说它只允许修改方法体，不能有方法或属性的添加和删除。
		我们这里修改了LangEnum的顺序，其实也就对枚举类中public static final 修饰的属性进行更改了，所以虚拟机不允许我们进行修改了，
        
综上结论：
	枚举类的switch case 是安全的，不会出现多文件,或者认为修改枚举然后未编译代码，导致出现的逻辑错误问题。
```



*参考文档：*

[菜鸟教程switch-case讲解](https://www.runoob.com/java/java-switch-case.html)