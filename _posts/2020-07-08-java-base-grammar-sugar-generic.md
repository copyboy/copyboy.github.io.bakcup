---
layout: post
title: 知识图谱 - Java基础 - 语法糖 - 泛型
categories: 知识图谱
description: 语法糖 - 泛型
keywords: 语法糖, 泛型
---





## 泛型做了什么



**把类型明确的工作推迟到创建对象或调用方法的时候才去明确的特殊的类型**



## 为什么需要泛型

> 设想没有泛型会发生什么？

```java
class Chapter01_2_2_Test {

    @Test
    public void generic_without() {

        Map map = new HashMap();
        map.put("cat1", new Cat("Cat1"));
        map.put("cat2", new Cat("Cat2"));
        map.put("cat3", new Cat("Cat3"));
        map.put("cat4", new Dog("Dog1"));

        for (int i = 0; i < map.size(); i++) {
            Cat cat = (Cat) map.get("cat" + (i + 1));
            System.out.println(cat.getName());
        }
    }

}
```

在对象取用时，需要强行转换为需要的对象类型，如果存在其他类型，会导致出现`ClassCastException`

```java
for (int i = 0; i < map.size(); i++) {
    Object o = map.get("cat" + (i + 1));
    if (o instanceof Cat) {
        Cat cat = (Cat) o;
        System.out.println(cat.getName());
    } else if (o instanceof Dog) {
        Dog dog = (Dog) o;
        System.out.println("忽略DOG的参数"+dog.getName());
    }
}
```

可以在取用对象值的时候， 先经过 instanceof 判断实际类型，再进行强制转换，这样可以避免运行时代码出现错误。

但是这样每次新增一种类型之后，调用的地方就需要进行适配处理，拓展性太差。



## 使用泛型的好处



1. 代码更加简洁【不用强制转换】

2. 程序更加健壮【只要编译时期没有警告，那么运行时期就不会出现ClassCastException异常】

3. 可读性和稳定性【在编写集合的时候，就限定了类型】



## 泛型使用

### 1 泛型类

```java
// 常用于定义通用报文请求/返回，把请求/返回特有的数据放在data中传输，通用属性提取出来使用
@Data
class ApiResult<T> {

    private String code;
    private String msg;
    private T data;
}
```

### 2 泛型方法

```java
public class BaseRequest {
    private String seqNo;
}
public class BaseResponse {
    private String code;
    private String msg;
}

// 泛型方法常用的地点：
// 1. 不明确当前需要传递什么值，但有一种统一的处理逻辑， 使用<T> 
// 2. 需要了解T内部的一些公用字段，需要限定T的上界，用于逻辑处理
class restClient {
    public <T> void send(T req) {
        System.out.println(req.toString());
    }
    public <T extends BaseRequest, R extends BaseResponse> R sendReq(T req, Class<R> clz) {
        System.out.println(req.getSeqNo());
        // restTemplate 请求获取结果，可以直接与restTemplate结合取得对象结果
        // 或者通过获取string 对象,经过Json转化得到实体对象
        R response = null;
//        HttpEntity<String> entity = new HttpEntity<>(req);
//        response = restTemplate.postForObject(url, entity, clz);
        return response;
    }
}
```

### 3 泛型类派生

```java
interface Api<T>{
    Api<T> success(T t);
    Api<T> error(T t);
}
// 子类明确泛型类的类型参数变量
class CommonApi implements Api<String>{

    String code;
    public CommonApi(String code){
        this.code = code;
    }
    @Override
    public Api<String> success(String s) {
        return new CommonApi("SUCCESS="+s);

    }
    @Override
    public Api<String> error(String s) {
        return new CommonApi("ERROR="+s);
    }
}
// 子类不明确泛型类的类型参数变量，需要在使用时明确泛型类的具体对象
class AbstractApi<T> implements Api<T> {

    @Override
    public Api<T> success(T t) {
        return null;
    }

    @Override
    public Api<T> error(T t) {
        return null;
    }
}
```

### 4 泛型通配符

```java
class CommonCharacter{
    // Raw use of parameterized class 'List'  不够优雅
    public void test0(List list){
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }
    }
    // 使用通配符来适配所有的List参数对象，默认? 继承自 Object
    public void test1(List<?> list){
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }
    }
    // 当List内容上限已知时，可以限定该上限
    public void test2(List<? extends Number> list) {
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i).intValue());
        }
    }
    // 有上限就会有下限，下限使用场景：<? super Type> 传递进来的只能是Type或Type的父类
    // 使用场景较少，暂时能想到的只有 Comparator ，当某个对象需要组合其他对象的功能时，可能需要实现
    public void test3(Comparator<? super TreeSet<?>> comparator) {
    }
}

```

```shell
在泛型的上限和下限中有一个原则：PECS(Producer Extends Consumer Super)

带有子类限定的可以从泛型读取【也就是--->(? extend T)】-------->Producer Extends
带有超类限定的可以从泛型写入【也就是--->(? super T)】-------->Consumer Super

```



## 泛型擦除

泛型是提供给`javac`编译器使用的，它用于限定集合的输入类型，让编译器在源代码级别上，即挡住向集合中插入非法数据。

也就是在实际的字节码文件中，泛型信息已经全部被擦除了。

进行反编译验证：`jad -sjava CommonCharacter.class`

```java
class CommonCharacter {

    CommonCharacter() {
    }
    public void test0(List list) {
        for(int i = 0; i < list.size(); i++)
            System.out.println(list.get(i));
    }
    public void test1(List list) {
        for(int i = 0; i < list.size(); i++)
            System.out.println(list.get(i));
    }
    public void test2(List list) {
        for(int i = 0; i < list.size(); i++)
            System.out.println(((Number)list.get(i)).intValue());
    }
    public void test3(Comparator comparator1) {
    }
}
```

可以明显看出泛型参数类型已经被去除掉。



> 参考文档

[泛型就这么简单](https://segmentfault.com/a/1190000014120746)

