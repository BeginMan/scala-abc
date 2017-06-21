看到一篇专题文章：

- [有趣的 Scala 语言系列](https://www.ibm.com/developerworks/cn/java/j-lo-funinscala2/index.html?ca=drs-)

觉得在Scala函数式编程这块写的特别好，这篇文章大部分来自上面的总结。

# 一. 变量和函数

scala中变量一种是可变的一种是不可变的，分别用`var`和`val`来定义，不可变意味着变量名不能被重新赋值。

一些建议：

- 在 Scala 中，鼓励使用 val，除非你有明确的需求使用 var
- 尽量使用 Scala 提供的类型推断（可读性好）

函数定义使用`def`. 

注意：

- 如果不是递归函数可以选择省略函数返回类型
- 匿名函数由参数列表，箭头连接符和函数体组成
- Scala 中的一条语句其实是一个表达式，函数的执行过程就是对函数体内的表达式的求值过程，最后一条表达式的值就是函数的返回值。
- 如果函数体只包含一条表达式，则可以省略 `{}`。
- 没有显示的 `return`语句，最后一条表达式的值会自动返回给函数的调用者

如：

```scala
// 匿名函数
scala> (x:Int) => x+1  // 匿名函数
res30: Int => Int = <function1>

scala> {x:Int => x+1}  // 代码较长可用花括号括起来
res31: Int => Int = <function1>

// 使用匿名函数，返回列表中的正数
List(-2, -1, 0, 1, 2, 3).filter(x => x > 0)
```

**定义函数的几种方式:**

```scala
scala> def add(x:Int): Int = {x+1}  //常规方式：参数+返回值
add: (x: Int)Int

scala> def add(x:Int) = {x+1}       //类型推导， 如不显示return 那么可省略返回类型
add: (x: Int)Int

scala> def add(x:Int) = x + 1       // 一行则 可省略{}
add: (x: Int)Int

scala> def fn(x:Int) {println(x)}   // 没有返回值, = 可省略
fn: (x: Int)Unit

scala> def fn {println("hi")}       // 没有参数，参数列表可省略
fn: Unit

scala> def fn = println("hi")       // 一行花括号可省略
fn: Unit

// 对于没有参数列表的函数，它不能用括号来调用
scala> fn()
<console>:9: error: Unit does not take parameters
              fn()
               ^
```


# 二.流程控制

scala中 if else 与其他语言不同，Scala 中的 **if else是一个表达式**，所以不用像 Java 那样显式使用 return返回相应的值。

```scala
scala> def abs(n:Int) = if (n>0) n else -n
abs: (n: Int)Int
```

当使用`while`循环是，最好用递归来处理，而且scala支持尾递归优化的。(递归函数必须显式指定返回值类型）

```scala
// 求和
// xs.head 返回列表里的头元素，即第一个元素
// xs.tail 返回除头元素外的剩余元素组成的列表
scala> def sum(xs: List[Int]): Int =
     |   if (xs.isEmpty) 0 else xs.head + sum(xs.tail)
sum: (xs: List[Int])Int

scala> sum(List(1,2,3,4,5))
res33: Int = 15

// 也可使用内置方法+匿名函数
scala> List(1,2,3,4,5).foldLeft(0)((x, y) => x+y)
res34: Int = 15
```

# 三. Scala 函数式编程

编程范式分为：

1. 命令式编程（Imperative Programming）
2. 函数式编程（Functional Programming）
3. 逻辑式编程（Logic Programming）

面向对象编程只是上述几种范式的一个交叉产物，更多的还是继承了命令式编程的基因。

[作者深入浅出举例](https://www.ibm.com/developerworks/cn/java/j-lo-funinscala3/index.html?ca=drs-) 很好诠释了scala函数式编程思想。这里我就抓下重点把。

什么是函数式编程：

>狭义地说，**函数式编程没有可变的变量、循环等这些命令式编程方式中的元素**，像数学里的函数一样，对于给定的输入，不管你调用该函数多少次，永远返回同样的结果。而在我们常用的命令式编程方式中，变量用来描述事物的状态，整个程序，不过是根据不断变化的条件来维护这些变量。

>广义地说，函数式编程重点在函数，函数是这个世界里的一等公民，函数和其他值一样，可以到处被定义，可以作为参数传入另一个函数，也可以作为函数的返回值，返回给调用者。利用这些特性，可以灵活组合已有函数形成新的函数，可以在更高层次上对问题进行抽象。本文的重点将放在这一部分。

函数式编程天然有并发的优势,程序要利用多核运算，必须采取并发，而**并发最头疼的问题就是共享数据**，狭义的函数式编程没有可变的变量，数据只读不写，并发的问题迎刃而解。这也是前面两篇文章中，一直建议大家在定义变量时，使用 val 而不是 var 的原因。爱立信公司发明的 Erlang 语言就是为解决并发的问题而生，在电信行业取得了不俗的成绩。

下面举例一个函数式编程例子：求a-b的阶层之和

```scala
scala> def fact(n:Int): Int = if (n==0) 1 else n * fact(n-1)
fact: (n: Int)Int

scala> def sumFact(a:Int, b:Int): Int = if (a > b) 0 else fact(a) + sumFact(a+1, b)
sumFact: (a: Int, b: Int)Int

scala> sumFact(1, 10)
res35: Int = 4037913
```

**演变1：**我们可以把共有的东西抽出来，比如如果是a~b之和，a~b立方之和等，那么公共部分就是`if (a>b) 0 else 处理函数(a) + 处理函数2(a+1, b)`, 这就用到了**高阶函数（Higher-Order Function），所谓高阶函数，就是操作其他函数的函数。**

```scala
//高阶函数：这里接收一个函数做参数
scala> def sum(f:Int => Int, a: Int, b: Int): Int =
     |   if (a > b) 0 else f(a) + sum(f, a+1, b)
sum: (f: Int => Int, a: Int, b: Int)Int

scala> def sumFact(a:Int, b:Int) = sum(fact, a, b)
sumFact: (a: Int, b: Int)Int

scala> sumFact(1, 10)
res36: Int = 4037913
```

**演变2：**这是函数式编程中经常用到的一个技巧，多数情况下，我们关心的是高阶函数，而不是作为参数传入的函数，所以为其单独定义一个函数是没有必要的，所以传入的函数可写成一个匿名函数：

```scala
// 立方之和
scala> def sumCube(a:Int, b:Int) = sum(x => x*x*x, a, b)
sumCube: (a: Int, b: Int)Int
```

**演变3：**[柯里化(curry)](https://zh.wikipedia.org/wiki/%E6%9F%AF%E9%87%8C%E5%8C%96)

就是把接受多个参数的函数变换成接受一个单一参数的函数，并返回一个用于接收剩余单个参数的函数。

比如Python可以这样处理：

```python
In [8]: def f(a):
   ...:     def f2(b):
   ...:         def f3(c):
   ...:             def f4(d):
   ...:                 print(a, b, c, d)
   ...:             return f4
   ...:         return f3
   ...:     return f2
In [12]: f(1)(2)(3)(4)
1 2 3 4
```

如果我们用偏函数，则有点像手动处理了：

```python
In [3]: from functools import partial

In [4]: def f(a, b, c, d):
   ...:     print(a, b, c,d)
   ...:

In [5]: cf = partial(partial(partial(f, 1), 2), 3)

In [6]: cf
Out[6]: functools.partial(<function f at 0x102c20048>, 1, 2, 3)

In [7]: cf(4)
1 2 3 4
```

在 Haskell 中，所有的函数都是柯里化的，即所有的函数只接收一个参数！那么在scala中如何实现呢：

```scala
scala> def sum(f:Int=>Int) (a:Int) (b:Int): Int = {
     |   if (a > b) 0 else f(a) + sum(f)(a+1)(b)
     | }
sum: (f: Int => Int)(a: Int)(b: Int)Int

scala> sum(x=>x)(1)(10)  // 1~10 求和
res37: Int = 55
```

更加简单：

```scala
scala> def max(x:Int)(y:Int) = if (x>=y) x else y
max: (x: Int)(y: Int)Int

scala> max(1)(3)
res39: Int = 3
```




