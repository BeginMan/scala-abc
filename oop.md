类：

```scala
class C {
  private var a = 0 //类成员变量必须初始化
  def inc() { a += 1 }

  def current() =  a // 对于类中的“取值方法”，在定义时可省略掉括号，直接 def current = value
}


object StreamingProcessor {
  def main(args: Array[String]): Unit = {
    val c = new C()
    val cc = new C
    c.inc()
    println(c.current())  // 1
    // c.a  Not..
    c.inc
    println(c.current)  // 2

    println(cc.current) // 0
  }
}
```

- scala 默认有构造器
- 调用无参构造器或无参方法时可省略掉其后的括号
- Scala类的每个字段都有getter和setter方法
- 不允许重写Scala的setter和getter方法

```scala
// 错误示例
class Person(private var name: String) {
  def name = name
  def name_=(aName: String) {name = aName}
}
```

>Scala默认对于Person类的name属性，自动生成的getter和setter方法名分别是`name`和`name_=`。但是你可以变通的用另一种方式来避开Scala的setter和getter方法名规范，比如我们把`name`属性名改为`_name`那么这个时候Scala默认生成的getter和setter方法是`_name`和`_name_=`，你只要不用这两个方法名就可以了。

```scala
class Person (private var _name:String) {
  def name = _name
  def name_=(aName:String) {_name = aName}    // 为什么name_ 后的 = 不能加空格???
  override def toString =s"name is $name"
}

object StreamingProcessor {
  def main(args: Array[String]): Unit = {
    val p = new Person("jack")
    println(p)
    p.name = "rose"
    println(p)
  }
}
```




