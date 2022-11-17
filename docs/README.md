### 引言
在写代码中经常遇到意料之外的错误，那就是空指针异常——java.lang.NullPointerException。一般这种情况我们都会使用if/else判空。但是这样写多了会显得繁琐，还会增加代码的复杂度。这个时候可以用Optional来优雅的判空。

### 什么是Optional
Optional是一个容器对象，可以包含也可以不包含非null值。Optional在Java 8中引入，目的是解决  NullPointerExceptions的问题。本质上，Optional是一个包装器类，其中包含对其他对象的引用。在这种情况下，对象只是指向内存位置的指针，并且也可以指向任何内容。从其它角度看，Optional提供一种类型级解决方案来表示可选值而不是空引用

### 如何使用
Optional.of()：传递参数，如果of中的对象是null，就报空指针异常。
```java
Optional<T> optional = Optional.of()
```
例：
```java
List<String> list = null;
Optional<List<String>> result = Optional.of(list);
```
会报java.lang.NullPointerException错误
***

Optional.ofNullable()：允许ofNullable传递null对象
```java
Optional<T> optional = Optional.ofNullable();
```
Optional.ofNullable() 源码
```java
    /**
     * Returns an {@code Optional} describing the specified value, if non-null,
     * otherwise returns an empty {@code Optional}.
     *
     * @param <T> the class of the value
     * @param value the possibly-null value to describe
     * @return an {@code Optional} with a present value if the specified value
     * is non-null, otherwise an empty {@code Optional}
     */
    public static <T> Optional<T> ofNullable(T value) {
        return value == null ? empty() : of(value);
    }
```
例：
```java
List<String> list = null;
Optional<List<String>> result = Optional.ofNullable(list);
System.out.println("result = " + result);
```
输出结果：`result = Optional.empty`
***
Optional.isPresent()：判断Optional实例是否为空
```java
Optional.isPresent()
```
Optional.isPresent(x -> do something)
如果为true，执行括号中内容
例：
```java
List<String> list = new ArrayList<>();
list.add("hello");
list.add("world");
Optional<List<String>> result = Optional.ofNullable(list);
System.out.println("result = " + result);
result.ifPresent(x -> {
  System.out.println("x = " + x);
  x.forEach(e -> System.out.println(e));
});
```
执行结果 
result = Optional[[hello, world]]
x = [hello, world]
hello
world
***
Optional.orElse()：如果optional为空的话返回orElse中的对象
```java
Optional.orElse()
```
例：
```java
String str = null;
String result;
result = Optional.ofNullable(str).orElse("/");
System.out.println("result = " + result);
// 等同于下列代码
System.out.println("======== 我是分割线 ==========");
if (StrUtil.isBlank(str)) {
  result = "/";
}
System.out.println("result = " + result);
```
输出结果
result = /
======== 我是分割线 ==========
result = /

如果如果optional不为空的话返回原本值
例：
```java
String str = "hello";
String result;
result = Optional.ofNullable(str).orElse("/");
System.out.println("result = " + result);
// 等同于下列代码
System.out.println("======== 我是分割线 ==========");
if (StrUtil.isBlank(str)) {
  result = "/";
}
System.out.println("result = " + result);
```
输出结果
result = hello
======== 我是分割线 ==========
result = hello

### 对象类型空指判断
构造一下简单的对象：
```java
@Data
public class User {
    private UserInfo info;
}

@Data
public class UserInfo {
    private String address;
}
```
给User对象赋予基本的值
```java
User user=new User();
UserInfo userInfo=new UserInfo();
userInfo.setAddress("hz");;
user.setInfo(userInfo);
```
一般情况下在使用User对象时需要使用多层if/else判空，就像下面这样：
```java
if (user!=null){
  UserInfo info = user.getInfo();
  if (info!=null){
    String address = info.getAddress();
  }
}
```
使用Optional就可以优雅的解决问题：
```java
String address = Optional.ofNullable(user)
        .map(u -> u.getInfo())
        .map(info -> info.getAddress())
        .orElse(null);
```
