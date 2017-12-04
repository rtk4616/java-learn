# Lambda 表达式

<!-- toc -->

## 定义

Lambda(λ) 表达式是一种在 _被调用的位置_ 或者 _作为参数传递给函数的位置_ **定义匿名函数对象** 的简便方法。下面是关于 Lambda 表达式的几个点：
* 匿名（Anonymous） - 不像其他普通方法那样具有名字
* 函数（Function） -  Lambda 表达式不像普通方法那样属于某个特定的类，它是独立于类存在的。但是和方法一样，Lambda 表达式有参数列表、函数主体和返回值，还可能有可以抛出的异常列表。
* 传递（Passed around）- Lambda 表达式可以作为参数传递给方法或者存储在变量中。
* 简洁（Concise）- 无需像匿名类那样写很多的模板代码。

下面是一个示例
```java
@FunctionalInterface
interface Calculator {
    int cal(int a, int b);
}

public class HelloWorld {
    public static void main(String[] args) {
        Calculator c = (a, b) -> a + b;
        System.out.println(c.cal(1, 2));
        c = (a, b) -> a * b;
        System.out.println(c.cal(1, 2));
    }
}
```

## Lambda 形式
Lambda 表达式的基本形式如下所示：
```
(argument list) -> code
```
下面是一个例子：

![](/images/lambda/形式.png)

如上所示： Lambda 表达式包含三个部分：
* 参数列表（A list of parameters） - 上图中为 `(Apple a1, Apple a2)`
* 箭头（An arrow） - 把参数列表和 Lambda 主体分隔开
* Lambda 主体（The body of the lambda） - 上图中为 `a1.getWeight().compareTo(a2.getWeight())`，该 Lambda 主体会返回 compareTo 的结果。

Lambda 函数的主体可以是表达式（expression）或者语句（statement），所以 Lambda 函数返回值有下面两种情况：
* 如果 Lambda 主体为表达式，那么 Lambda 函数的返回值就是表达式的计算值
* 如果 Lambda 主体为语句，那么 Lambda 返回值就是语句的返回值

关于语句和表达式的区别，可以参考 [这篇文章](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/expressions.html)，这里简单说一下：假设有一条语句 `int c = a + b;`，那么表达式就是指 `c = a + b`，即不包含 `int` 和 `;`，每个表达式都会有一个计算值（void 也算一种特殊的计算值）。

所以细分一下，Lambda 表达式有两种形式：
```
(parameters) -> expression
```
和（使用大括号）
```
(parameters) -> {statements}
```

下面是 Lambda 表达式的几个例子：

![](/images/lambda/demo.png)

| 使用场景 | 使用示例 |
|:---|:---|
| boolean 表达式 | `(List<String> list) -> list.isEmpty()` |
| 创建对象 | `() -> new Apple(10)` |
| Consuming from an object | `(Apple a) -> { System.out.println(a.getWeight()); }` |
| Select/extract from an object | `(String s) -> s.length()` |
| 合并两个值 | `(int a, int b) -> a * b` |
| 比较两个对象 | `(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())` |



## 使用 Lambda
我们可以在函数式接口 （Functional interface）中使用 Lambda 表达式。简单来说，函数式接口就是只定义一个抽象方法的接口（接口中可以包含额外的 default 方法）。例如 Comparator 和 Runnable 都是函数式接口：
```java
public  interface Comparator<T> {
    int compare(T o1, T o2);
}

public interface Runnable {
    void run();
}
```
我们一般在接口定义中加上 `@FunctionalInterface` 注解来表明该接口是一个函数式接口。例如下面的形式：
```java
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}
```
当一个接口通过 `FunctionalInterface` 被声明为函数式接口时，编译器将会检查接口的合法性，如果接口不合法，会报编译错误。

现在考虑一个问题，Lambda 表达式是如何匹配函数式接口的呢？假设我们有一个如下定义的函数式接口：
```java
@FunctionalInterface
interface Calculator {
    int cal(int a, int b);
}
```
下面是使用 lambda 表达式以及匿名类来创建 Calculator 对象的示例代码。在下面的代码中对象 c 和 c2 的实现是等价的。
```java
public void demo() {
    Calculator c = (int a, int b) -> a + b;

    Calculator c2 = new Calculator() {
        @Override
        public int cal(int a, int b) {
            return a + b;
        }
    };
}
```
从上面的例子中，我们可以看到 **Lambda 表达式** 是和函数式接口中的 **抽象方法** 进行匹配的，其中 Lambda 表达式中参数匹配 cal 方法的参数，Lambda body 的内容作为抽象方法的具体实现，Lambda body 的计算值作为方法的返回值。这也是为什么要求函数式接口只能有一个抽象方法的原因。

函数式接口中抽象方法的签名（signature）描述了 Lambda 表达式的签名，因为 Lambda 表达式并没有名字，所以这里的签名只关注两个方面：**方法参数** 和 **返回值**。我们将抽象方法所描述的 Lambda 形式称为函数描述符（function descriptor）。在 Calculator 类中，cal 方法对应的函数描述符为 `(int, int) -> int`，即接受两个 int 类型作为参数，表达式的计算值为 int 类型。所以下面的 Lambda 表达式都是合法的：
```java
(int a, int b) -> a
(int a, int b) -> a + b
(int a, int b) -> 0
```